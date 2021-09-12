---


---

<h1 id="devsecops-series-1-alibaba-cloud의--security-플랫폼과--hashicorp의--terraform-vault-packer로--구현하는-secops">[DevSecOps Series 1] Alibaba Cloud의  Security 플랫폼과  HashiCorp의  Terraform, Vault, Packer로  구현하는 SecOps</h1>
<blockquote>
<p>본  데모  블로그는  Alibaba Cloud와  HashiCorp가  함께한  DevSecOps 웨비나를  준비하면서  작성했습니다. 웨비나와  블로그  작성에  도움을  주신  HashiCorp의  GS Lee에게  감사합니다.</p>
</blockquote>
<blockquote>
<p>본 블로그는 편의상 높임체를 사용하지 않았습니다.</p>
</blockquote>
<h2 id="배경--설명">1. 배경  설명</h2>
<p>현재  많은  기업들은  그들의  개발, 운영  프로세스를  최적화하기  위해  많은  노력을  하고  있다. 특히  그들은  개발, 운영  사이에서  끊임없는  프로세스를  만들어서  조직이  애플리케이션  서비스를  빠르고  안전하게  배포하는  것을  목표로  가지고  있다.<br>
이러한  변화를  우리는  <strong>DevOps</strong>라고  이야기한다.</p>
<p>하지만  최근에는  DevOps 프로세스를  구성하는  과정에서  보안  강화에  대한  부분이  강조되고  있다.<br>
기업은  모든  IT 개발과  운영  프로세스에서  개발, 배포, 테스트, 관리에  이르기까지  모두  보안을  고려해야한다. 이  것을  <strong>DevSecOps</strong>라고  말한다.</p>
<p>우리는  본  데모에서  Alibaba Cloud와  HashiCorp의  서비스로  인프라부터  네트워크까지  이어지는  DevSecOps를  구현하는  방법에  대해서  배워볼  것이다.</p>
<h2 id="solution-overview">2. Solution Overview</h2>
<p>데모  환경은  Alibaba Cloud의  싱가포르  리전을  사용하며  아래와  같은  아키텍쳐를  가지고  있다.</p>
<p>먼저  HashiCorp의  <strong>Terraform</strong>을  이용해서  Alibaba Cloud의  서비스  자동화를  위한  Provider 설정을  한다. 그  이후  Terraform을  이용해  <strong>Vault</strong>를  배포한다. Vault는  배포될  인스턴스에서  사용할  SSH를  위한  OTP 생성을  담당한다.</p>
<p>그  이후, <strong>Packer</strong>를  이용해  ECS를  배포할  때  사용할  골든이미지를  만든다. 그리고  만들어진  골든이미지를  통해  ECS, SLB를  배포할  것이다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978133-aa5ab7d9-6e50-47a0-a057-a6b2974fc648.png" alt="image"></p>
<p>골든이미지로  만들어진  서비스의  네트워크  보안을  강화하기  위해  Alibaba Cloud의  4가지  보안 서비스를  사용할  것이다.</p>
<p>먼저, <strong>Security Center</strong>로  구성된  Asset에  대한  보안을  관리한다.<br>
그  이후, 우리는  <strong>Cloud Firewall, WAF, Anti-DDoS</strong>를  차례대로  연결하며  풀스택  보안을  구현할  것이다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978180-28168bed-84c0-4ff8-8bdb-208ff499ec9d.png" alt="image"></p>
<h2 id="사전--조건">3. 사전  조건</h2>
<p><strong>[Alibaba Cloud]</strong></p>
<ul>
<li><a href="https://www.alibabacloud.com/product/security-center?spm=a2c63.l28256.6791778070.dnavproductcloudsec8.76a87cceWvwldU">Security Center</a></li>
<li><a href="https://www.alibabacloud.com/product/cloud-firewall?spm=a2c63.l28256.6791778070.dnavproductcloudsec5.76a87cceroYKPw">Cloud Firewall</a></li>
<li><a href="https://www.alibabacloud.com/product/web-application-firewall?spm=a2c63.m28257.6791778070.dnavproductcloudsec6.26125922eRyI0C">WAF</a></li>
<li><a href="https://www.alibabacloud.com/product/ddos?spm=a2c63.m28257.6791778070.dnavproductcloudsec2.26125922CA2OcU">Anti-DDoS</a></li>
</ul>
<p><strong>[HashiCorp]</strong></p>
<ul>
<li><a href="https://registry.terraform.io/providers/aliyun/alicloud/latest/docs">Terraform</a></li>
<li><a href="https://www.vaultproject.io/docs/secrets/alicloud">Vault</a></li>
<li><a href="https://www.packer.io/docs/builders/alicloud">Packer</a></li>
</ul>
<p>구성에  사용할  모든  스크립트와  소스코드는  아래  GitHub Repository에서  참조할  수  있다.<br>
<a href="https://github.com/alicloud-hashicorp/webinar/tree/main/202110/episode01">https://github.com/alicloud-hashicorp/webinar/tree/main/202110/episode01</a></p>
<h2 id="main-steps">4. Main Steps</h2>
<h3 id="terraform-registry에서--alibaba-cloud-provider-살펴보기">4.1 Terraform Registry에서  Alibaba Cloud Provider 살펴보기</h3>
<p><strong>Terraform</strong>은 HashiCorp에서  제공하는  IaC (Infrastructure as Code)를  구현할  수  있는  자동화  플랫폼이다. 이번  데모에서는  Terraform의  Opensource 버전이  아닌  Enterprise 버전을  사용할  것이다.</p>
<p>먼저  Terraform을  사용하기에  앞서, Provider에  대해서  이해할  필요가  있다. Provider는  Terraform으로  연결을  만드는  메커니즘으로  Terraform registry에서  각  CSP에  대한  사용법을  친절하게  게시해  놓았다.</p>
<p><a href="https://registry.terraform.io">Terraform Registry</a> 에  접속하면  아래와  같은  화면을  볼  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978292-02bdebcd-73b6-4e10-91a8-ac98e131e9a2.png" alt="image"></p>
<p>페이지에서  Alibaba Cloud 항목을  클릭하면 Alibaba Cloud에서  제공하는  각  서비스와의  연결을  위한  메뉴얼을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978316-f664f48c-f94d-458c-9dcf-1d4459fb35f1.png" alt="image"></p>
<p><em>Argument reference</em>는  예시  기반으로  상세한  사용  방법이  적혀있다. 이 메뉴얼을 참조한다면 Terraform을  처음  접하는  사람이라도  쉽게  사용할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978355-4112a668-67ef-4f55-ba90-d77a3e87a0dd.png" alt="image"></p>
<h3 id="terraform과--alibaba-cloud-연결하기">4.2 Terraform과  Alibaba Cloud 연결하기</h3>
<p>Terraform을  통해서  Alibaba Cloud의  서비스를  관리하기  위해서는  첫번째로  credential 설정이  필요하다.</p>
<p>Credential은  총  <a href="https://registry.terraform.io/providers/aliyun/alicloud/latest/docs#authentication">4가지  방법</a>으로  설정할  수  있지만  본  데모에서는  Access/Secret Key를  이용한  Static credential을  사용할  것이다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978403-61eafad2-c9fc-4ac6-90b3-203a6a1e0c2c.png" alt="image"></p>
<p>Static credential을  사용하기  위해  우리는  Alibaba Cloud의  AccessKey Pair를  발급받을  필요가  있다. Alibaba Cloud Console의  우측  상단  <em>Profile</em>에서  AccessKey Pair를  발급받을  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978431-21f5e90d-413f-424c-ba99-00c05db4348b.png" alt="image"></p>
<blockquote>
<p>나중에  AccessKey를  사용해야하기  때문에  Enabled된  Key를  노트해두자</p>
</blockquote>
<p>이제  본격적으로  Terraform과  Alibaba Cloud 연결을  위한  tf script를  작성할  것이다. 위에  표기해  놓은  GitHub repo의  <a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/01_Howto/provider.tf"><em>01_Howto &gt; provider.tf</em></a> 에서  연결을  위한  소스코드를  볼  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978468-e8a356d4-91d0-48c2-91a1-1660128da056.png" alt="image"></p>
<ul>
<li><em>Required_providers</em> : 소스  디렉토리  정의</li>
<li><em>Version</em> : 버전  정의</li>
</ul>
<p>프로바이더에  대한  옵션을  정의했으면  아래  명령어로  Account에  대한  연결을  시작할  수  있다.</p>
<pre><code>Terraform init
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/132978507-acd198bc-02e6-4e98-8bb3-34780adae5b9.png" alt="image"></p>
<p><strong>Terraform has been successfully initialized!</strong> 문구를  확인했다면  초기화가  완료된  것이다.</p>
<h3 id="vault-배포를--위한--terraform-설정">4.3 Vault 배포를  위한  Terraform 설정</h3>
<p><strong>Vault</strong>는  HashiCorp에서  제공하는  멀티  인프라  관리를  위한  보안  플랫폼이다. 우리는  이번  데모에서  Vault의  많은  보안  기능  중, SSH 연결을  위한  OTP 발급에  대한  기능을  사용할  것이다.</p>
<p>먼저  Vault를  Alibaba Cloud 환경에  배포하기  위하여  Alibaba Cloud에서  가장  기본적인  리소스인  ECS, Network에  대한  컨피그레이션을  정의한다.</p>
<blockquote>
<p>인스턴스  Provisioning을  위해  아래  링크를  참조할  수  있다.<br>
<a href="https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/auto_provisioning_group">https://registry.terraform.io/providers/aliyun/alicloud/latest/docs/resources/auto_provisioning_group</a></p>
</blockquote>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/02_Vault/ecs_vault.tf"><em>Github repo &gt; 02_Vault &gt; ecs_vault.tf</em></a> 에는  Vault를  배포할  인스턴스에  대해  정의한다. 이  환경에서 우리는  Ubuntu 이미지를  사용하며  보안  강화를  위해  랜덤한  비밀번호를  생성하여  접속할  수  있도록  만들었다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978846-09ced3a1-39c9-4292-bfaa-ad0917fb4ddf.png" alt="image"></p>
<p><em>ecs_vault.tf</em> 파일안의  user_data 부분을  확인해보면  Vault 배포를  위한  Initial Script가  적용되어  있음을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978894-8c5dea5e-711d-4ab0-80f9-9c7410a8f7a6.png" alt="image"></p>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/02_Vault/network.tf"><em>Github repo &gt; 02_Vault &gt; network.tf</em></a> 파일을  확인해보면  인스턴스를  배포할  VPC, Zone, vSwitch, Security group이  정의되어  있음을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132978982-a9e39334-dbff-4fe1-9b44-6b6f788669f6.png" alt="image"></p>
<p>우리는  Terraform Script를  실행한  후  나올  결과값을  정의할  수  있다. <a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/02_Vault/output.tf"><em>Github repo &gt; 02_Vault &gt; output.tf</em></a> 파일을  확인해보면  terraform apply의  결과값으로  public ip, 랜덤하게  생성된 password, ssh 접속  경로를  확인할  수  있도록  정의되어있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979016-0103f32d-f4b3-4e63-9677-95bf87675f56.png" alt="image"></p>
<h3 id="terraform을--이용한--vault-배포">4.4 Terraform을  이용한  Vault 배포</h3>
<p>위의  단계에서  정의한  tf 파일들의  실행을  위해  아래  명령어를  입력한다.</p>
<pre><code>Terraform apply
</code></pre>
<p>배포가  완료되면  Alibaba Cloud ECS Console에서  생성된  Vault의  인스턴스를  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979044-b77d7093-e710-4d0b-8c52-6c14368c99db.png" alt="image"></p>
<p>또한  Vault 콘솔에  접속하기  위한  SSH, Public IP, password를  아래  명령어로  확인할  수  있다</p>
<pre><code>Terraform output
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/132979067-305303eb-bf8f-4076-b5d5-cc8fc0e1b458.png" alt="image"></p>
<blockquote>
<p>데모에서는  Vault를  Unseal하는  과정을  생략했다. 만약  Vault가  정상적으로  동작하지  않을  경우  Status를  확인하여  Unseal하는  작업을  진행해주면  된다.</p>
</blockquote>
<h3 id="서비스가--배포될--ecs의--ssh연결을--강화할--otp-설정을-위한-파일-정의">4.5 서비스가  배포될  ECS의  SSH연결을  강화할  OTP 설정을 위한 파일 정의</h3>
<h4 id="pam-설정">4.5.1 Pam 설정</h4>
<p><code>/etc/pam.d/sshd</code> 파일의 <code>@include common-auth</code> 부분을 다음과 같이 변경 추가한다.</p>
<pre><code>#@include common-auth  
auth requisite pam_exec.so quiet expose_authtok log=/tmp/vaultssh.log /usr/local/bin/vault-ssh-helper -config=/etc/vault-ssh-helper.d/config.hcl -dev  
auth optional pam_unix.so not_set_pass use_first_pass nodelay
</code></pre>
<h4 id="sshd_config-설정">4.5.2 sshd_config 설정</h4>
<p><code>/etc/ssh/sshd_config</code> 파일의 <code>ChallengeResponseAuthentication</code> 부분을 수정한다.</p>
<blockquote>
<p>이미 있는 옵션은 값 수정, 없는 옵션은 추가</p>
</blockquote>
<pre><code>ChallengeResponseAuthentication yes  
UsePAM yes  
PasswordAuthentication no
</code></pre>
<h4 id="vault-ssh-helper-다운로드">4.5.3 vault-ssh-helper 다운로드</h4>
<p>Vault Server와  연결할  클라이언트는  helper라고  부르는  Agent 를 다운받아야 한다. 링크는 아래에서 확인할 수 있다.</p>
<blockquote>
<p><a href="https://releases.hashicorp.com/vault-ssh-helper/">https://releases.hashicorp.com/vault-ssh-helper/</a></p>
</blockquote>
<h4 id="helper-설정">4.5.4 helper 설정</h4>
<p>Vault Server와  연결할  클라이언트는  helpe 설정이  필요하다. 우리는 <a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/03_Packer/files/vault.hcl">vault.hcl</a> 파일에서  연결을  위한  컨피그레이션을  확인할  수  있다.</p>
<h4 id="배포할-운영-애플리케이션-스크립트-설정">4.5.5 배포할 운영 애플리케이션 스크립트 설정</h4>
<p>테스트에 사용할 운영 애플리케이션 스크립트를 작성한다.<br>
<a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/03_Packer/files/deploy_app.sh">https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/03_Packer/files/deploy_app.sh</a></p>
<p><strong>4.5 단계에서 설정한 5가지를 Packing하기 위해 모두 files 디렉토리에 정의한다.</strong></p>
<h3 id="packer-설정">4.6 Packer 설정</h3>
<p>원래 운영자는  운영  이미지를  만들기  위해서  아래와  같은  절차를  반드시  진행해야  했다.</p>
<p><em>ㄱ. 골든  이미지  선택<br>
ㄴ. 임시  인스턴스(네트워크  구성요소  포함) 구동<br>
ㄷ. 패키지  설치  및  인스턴스  커스터마이징<br>
ㄹ. 구성  완료된  인스턴스에서  이미지  추출</em></p>
<p><strong>Packer</strong>는  위와  같은  복잡한  운영  이미지를  Packing하는  작업을  자동화한다. 우리는  Packer를  이용해서  Immutable Infrastructure를  구성할  수  있다.</p>
<p>Packer 구성에서  주목할만한  컨피그레이션은  바로  Access/Secret Key이다. 우리는  보호가  필요한  값을  로컬에  평문으로  저장하지않고  vault에  저장하여  코드에서  참조하는  모델로  설계할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979587-26ca0b76-8cf6-4e46-8708-e478811c199c.png" alt="image"></p>
<p>참조되는  변수  값은  Vault 콘솔의  secrets 메뉴에  저장할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979660-27984573-014d-407c-9457-b10a224f2c84.png" alt="image"></p>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/03_Packer/packer.pkr.hcl">Github repo &gt; 03_Packer &gt; packer.pkr.hcl</a>에서  Packer를  실행할  스크립트를  정의한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979707-82702cfa-a963-41e2-8c3c-fd8cdba4d27d.png" alt="image"></p>
<ul>
<li><em>Build 영역</em>  : /files에  추가한  ssh, helper 관련  파일들을  패키징하기  위해  정의</li>
<li><em>Vault OTP 영역</em>  : 삽입한  ssh 관련  파일로  OTP 설정을  정의</li>
<li><em>Apache 영역</em>  : 삽입한  deploy app shell 파일로  테스트  애플리케이션  배포</li>
</ul>
<h3 id="packer-build-수행">4.7 Packer build 수행</h3>
<p>정의한  Packer의  컨피그레이션으로  Packer Build를  수행한다.</p>
<pre><code>packer build -force
</code></pre>
<blockquote>
<p>-force 옵션은  기존에  배포한  이미지가  있을  경우  그  이미지를  overwrite한다</p>
</blockquote>
<p>실행한  후  로그를  확인해보면  vpc와  vswitch, security group이  임시로  생성되고  운영  이미지를  추출할  인스턴스를  생성한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979759-7ef1fbb7-9dca-4189-ae6e-3a7c1a3b1af3.png" alt="image"></p>
<p>Alibaba Cloud ECS 콘솔을  확인해보면  임시  인스턴스가  생성되는  것을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979770-9442f9e7-69c6-4a02-bb09-e9489e068206.png" alt="image"></p>
<p>임시로  생성된  인스턴스는  packer config에서  포함한 5개의 패키지와  파일, 스크립트가  포함되어  생성되며, 완료가되면 새로운  이미지로  추출하고  인스턴스는  삭제된다</p>
<blockquote>
<p>Packer를  실행하다가  실패하면  모두  중지  처리가  되어서  임시로  생성한  리소스들을  모두  삭제</p>
</blockquote>
<p>생성된  운영  이미지는  ECS의  Image 메뉴에서  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979812-b0b3f529-0602-4001-96f5-41755b73057d.png" alt="image"></p>
<h3 id="terraform-enterprise로--운영--환경--배포">4.8 Terraform enterprise로  운영  환경  배포</h3>
<p>이번  단계에서는  준비된  이미지로  테스트  애플리케이션을  운영할  인스턴스, 네트워크  환경을  배포할  것이다. 물론  우리는  Terraform을  이용해서  자동화된  운영  배포를  수행할  것이다.</p>
<p>Provider를  구성할  때  로컬이  아닌  백엔드에서  Terraform이  수행되도록  설정할  수  있다. 그러면  우리는  엔터프라이즈  환경에서  운영할  때  로컬에  문제가  생기거나  운영자의  롤이  바뀌는  등의  변화가  있을  때  편하게  스크립트  실행  환경  관리를  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132979846-fd99d050-46d2-4aa6-86be-7a21788039dd.png" alt="image">- Provider를  설정할  때, backend “remote” 컨피그레이션으로  Terraform을  실행할  리모트  서버를  설정</p>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/04_ECS/ecs.tf"><em>Github repo &gt; 04_ECS &gt; ecs.tf</em></a>에서  운영  애플리케이션을  배포할  ECS 인스턴스의  컨피그레이션을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132980077-468d7ac3-899c-42fb-93f2-e4725fb7480b.png" alt="image">- <em>Data “alicloud_images”</em> : most_recent 옵션으로  최신의  이미지를  참조</p>
<ul>
<li><em>Data “alicloud_instance_types”</em> : 각  옵션에서  정의하는  core/mem 개수로  적절한  인스턴스  타입  정의</li>
<li><em>Resource “alicloud_instance”</em> : 정의된  컨피그레이션으로  인스턴스를  배포</li>
</ul>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/04_ECS/network.tf">Github repo &gt; 04_ECS &gt; network.tf</a>에서  운영  애플리케이션을  배포할  네트워크  환경을  정의할  수  있다. 여기서는  각각  vpc, zone, vswitch, security group 등을  정의한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132980153-26463976-bede-4b54-bd0e-1c8932947f4b.png" alt="image"></p>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/04_ECS/output.tf"><em>Github repo &gt; 04_ECS &gt; output.tf</em></a>에서  배포가  완료된  후의  결과에  대한  내용을  정의할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132980215-ab7b0b38-4185-47f6-bbd5-feb573351699.png" alt="image">- <em>Output “ecs_ids”</em>: 여기서  나오는  instance id를  이용하여  다음  단계에서  진행할  배포된  ECS 인스턴스의 ID들을  얻을  수  있다.</p>
<p>이번  단계에서는  위에  설정한  것과  같이  Local에서  Terraform script를  실행하지  않고  Terraform Cloud 환경에서  script를  실행할  것이다.</p>
<pre><code>Terraform init
</code></pre>
<p>위  명령어를  입력하면  엔터프라이즈  콘솔에  워크스페이스가  생성된다</p>
<p>물론  CLI Terminal에  Terraform apply로  실행할  수도  있지만  아래와  같은  <a href="app.terraform.io">Terraform Cloud</a>에  들어가서  Apply를  수행할  수도  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132980791-6fb1b854-002f-47c8-86b3-169e02b16ad3.png" alt="image"></p>
<p>먼저  script를  실행하기  이전에  Variable 옵션에  들어가서  <em>instance_count</em>를  3으로  해준다. 이  변화는  우리가  이번  스크립트로  ECS instance를  3개  배포한다는  것을  의미한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981217-b8838e52-76b2-4153-abd3-7f0f2be47390.png" alt="image"></p>
<pre><code>Terraform apply
</code></pre>
<p>이번  단계에서는  위의  명령어를  먼저  터미널에서  실행하고 Terraform Cloud 콘솔에서  진행에  대한  Status를  확인한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981257-78555574-563a-4504-a0ba-6904a70b4967.png" alt="image"></p>
<ul>
<li><em>Plan</em> : 임시로  Terraform script를  실행하여  결과를  테스트</li>
<li><em>Policy check</em> : 사전에  정의해  놓은  정책으로  정상적으로  인스턴스가  배포되었는지  확인</li>
</ul>
<blockquote>
<p>Policy check는  각  기업의  정책에  맞게  커스터마이징을  할  수  있다.</p>
</blockquote>
<p>설정  이후에  <em>Confirm&amp;Apply</em>를  누른다.</p>
<p>Terraform Script의  수행이  모두  완료되면, Alibaba Cloud ECS Console에서  생성된  3개의  인스턴스를  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981306-060e3e96-b938-4377-af33-150ec33c13e4.png" alt="image"></p>
<h3 id="slb-배포로--ecs인스턴스--연결">4.9 SLB 배포로  ECS인스턴스  연결</h3>
<p>위에서  배포된  3개의  ECS 인스턴스를  연결하기  위해  SLB를  연결하는  작업을  할  것이다.</p>
<p><a href="https://github.com/alicloud-hashicorp/webinar/blob/main/202110/episode01/05_SLB/slb.tf"><em>Github repo &gt; 05_SLB &gt; slb.tf</em></a>파일에서  SLB 설정에  대한  컨피그레이션  파일을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981344-8af76d94-0ec4-49de-b958-58122c5c3bcb.png" alt="image"></p>
<p>for_each 설정으로  다이나믹  컨피그레이션  설정을  한다. 이  설정으로  ECS의  개수가  바뀌면  바로  연결해준다. 위의  <em>remote_state</em>는  전역변수  처리를  위해  04_ECS 워크스페이스에서  배포된  ECS의  instance_id 변수값을  얻어온다.</p>
<p>다음  단계에서  04_ECS 워크스페이스에서 terraform apply가  실행되었을  때  05_SLB 스크립트를  자동으로  실행하기  위해  트리거  설정을  해준다</p>
<p><em><strong>Workspace &gt; settings &gt; runtrigger</strong></em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981383-371ff007-11e8-47f3-a65f-d8e0df9ad643.png" alt="image"></p>
<p>설정  후에  <em>Confirm&amp;Apply</em>를  콘솔에서  수행한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981411-c7cbf2e8-fd30-4356-84c0-608c2e8afe98.png" alt="image"></p>
<p>Alibaba Cloud 콘솔에서  SLB에  3개의  ECS 인스턴스가  추가되었음을  확인한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981430-6b883eb2-5b3e-4af7-beca-e7f9720b5d3d.png" alt="image"></p>
<h3 id="vault의--otp-기능--확인">4.10 Vault의  OTP 기능  확인</h3>
<p>이번  단계에서는  배포된  ECS에  SSH로  접속할  때  사용할  Vault의  OTP 기능을  사용해  볼  것이다.</p>
<p><em>Vault Console &gt; Secrets &gt; ssh-otp</em>를  확인하면  위  단계에서  설정한  otp_key_role을  확인  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981468-b58c6ad4-efbb-484e-af26-fb698d6229a1.png" alt="image"></p>
<p>OTP에  들어가서  접속할  ID와  IP address 입력한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981494-9ec07e3a-0bab-41c2-a45c-c026539a5b8a.png" alt="image"></p>
<p>다음으로  <em>Generate</em>을  선택하면  OTP가  나온다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981510-4bf663ca-b589-41c9-bd16-41a1e93d80a8.png" alt="image"></p>
<p>다음으로, 우리는 생성된  OTP를  복사하고, 터미널에서  이  key를  이용해서  터미널에서  SSH 접속을  시도한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981541-7d4637d5-4913-4d2c-bc89-8b17e8addc84.png" alt="image"></p>
<p>결과적으로, 우리는  Vault의  OTP를  이용해서  SSH 연결을  성공한  것을  확인할  수  있다.</p>
<blockquote>
<p>OTP는  한번  사용하면  다시는  사용할  수  없다.  다시  Generate 해야한다.<br>
이  방법으로  우리는  ssh protocol을  사용한  공격을  거의  완벽하게  방어할  수  있다.</p>
</blockquote>
<h3 id="배포된--애플리케이션--확인">4.11 배포된  애플리케이션  확인</h3>
<p>우리는  서비스가  정상적으로  배포되었는지  확인하기  위하여, 브라우저를  하나  열고  SLB에  설정된  Public IP를  입력한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981612-4de75067-55f4-491f-af41-db339c9258e4.png" alt="image"></p>
<p><strong>귀여운  고양이가  나온다면  연결에  모두  성공한  것이다.</strong></p>
<p>여기까지  HashiCorp의  서비스를  이용해서  알리바바  클라우드의  서비스를  안전하게  자동화하여  배포하는  방법에  대해서  알아보았다.<br>
이제  알리바바  클라우드에서  4가지  보안  서비스를  이용해서  SecOps를  수행하는  걸  해볼것이다.<br>
물론  아래에  수행하는  모든  작업을  Terraform script로  자동화  할  수  있지만  오늘은  Step-by-Step으로  보여줄것이다.</p>
<h3 id="security-center-overview">4.12 Security Center Overview</h3>
<p><strong>Security Center</strong>는  중앙화되어서  다이내믹하게  Security에  대한  위협에  대한  방어, 알림, 감지  등을  수행한다.<br>
이  서비스의  기능으로  Anti-ransomware, antivirus, web tamper proofing, container image scan, compliance check 등이  포함된다.</p>
<p>우리는  Security Center의  콘솔에  접속하면  아래와  같은  화면을  볼  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981676-88689988-6252-4b5e-b08e-06ee26908e87.png" alt="image"></p>
<p>overview에서  첫  번째로  봐야할  사항은  Secure Score이다.</p>
<p>Security Score는  나의  Cloud 환경에  구성된  Asset들의  Security에  대한  평가  수치를  보여준다.<br>
우리는  예시  화면에서  현재  매우  위험한  Score를  가지고  있는  환경을  볼  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981761-2b2fc36a-f225-4687-83f7-4a2d61e2fe45.png" alt="image"></p>
<p>여기서  Fix now를  누르면  위험한  Alert, Risk의  목록이  나온다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981787-353f5794-50e1-45c9-a2b8-584842afe2ff.png" alt="image"></p>
<p>우리는  테스트를  위해서  일부러  AccessKey를  Github에  업로드를  해놓았다. 여기서  조치를  위해  <em>Process Now</em>를  수행할  수  있다.</p>
<p>다음으로, 우리는  AccessKey Leak Detection 메뉴에서  현재  위험에  노출된  AccessKey를  제거하거나  변경할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981812-99203d2b-a583-4be9-ad83-f6e95bdf33d2.png" alt="image"></p>
<h3 id="secops를--위한--alibaba-cloud의--security-center의--세부--기능--살펴보기">4.13 SecOps를  위한  Alibaba Cloud의  Security Center의  세부  기능  살펴보기</h3>
<p>Security Center에는  이번  블로그에서  모두  다루기  힘들만큼  많은  기능들이  포함되어있다. 하지만  오늘은  SecOps를  위한  몇가지  기능을  간단히  살펴볼  것이다.</p>
<p>첫  번째로  살펴볼  기능은  <em>Config Assessment</em>이다.</p>
<p>Config Assessment는  Cloud 환경에  구성된  Asset의  Configuration을  평가한다. 만약  우리의  환경의  Configuration에  보안상  문제가  발생할만큼의 위험한  설정이  있다면  이  메뉴에서  확인과  해결을  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981847-2e31dd64-8d01-49f8-9bdd-070fbedcde82.png" alt="image"></p>
<p>두  번째로  살펴볼  기능은  <strong>Reports</strong>이다.</p>
<p>우리는  운영을  할  때, Daily, Weekly 등  주기적으로  reporting을  받을  필요가  있다. 이러한  Agony가  있을  때, 우리는  Reports 기능을  이용할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981866-1b50f626-cb02-495b-bfc3-093621e7a9bb.png" alt="image"></p>
<p>우리는  생성할  Report의  기본  Information을  적고  reporting을  받을  metric을  정의하여  주기적으로  보고  받을  정보를  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981882-48c9ac41-ed78-4acc-b177-28fa41eb698d.png" alt="image"></p>
<p>마지막으로  살펴볼  기능은 <strong>Horus eye</strong>이다.<br>
이  기능은  현재  우리의  환경에  구성된  Asset에  대한  정보를  확인할  수  있으며  Cloud Firewall, WAF, Anti-DDoS를  Enable 할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981908-9dbd3048-e6d3-4900-873e-100785993cc9.png" alt="image"></p>
<h3 id="cloud-firewall에서--네트워크--방화벽--설정">4.14 Cloud Firewall에서  네트워크  방화벽  설정</h3>
<p>우리의  환경의  네트워크  보호를  위해  첫  번째로  사용할  서비스는  <strong>Cloud Firewall</strong>이다.</p>
<p>Cloud Firewall은  North- south bound traffic, East-west bound traffic를  모두  보호하는  L4 Firewall 서비스이다.<br>
이 서비스는 사용하기  쉽고, 완벽하게 관리되며, 스케일이  쉽고, 안정적이며,  리얼타임으로  방어한다.<br>
우리는  구성된  우리의  환경을  보호하기  위해  Firewall 기능을  Enable할  것이다.</p>
<p><em>Cloud Firewall Console &gt; Firewall Settings &gt; Internet Firewall</em>을  접속하면  외부로  노출된  Public Asset리스트를  확인할  수  있다.<br>
이  화면에서  우리는  모든  Asset을  선택하고나서 <em>Enable</em>을 클릭한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981946-732fb053-94f2-4507-ac22-45cfc8830635.png" alt="image"></p>
<p>위에서  작업한  Enable 만으로  모든  인터넷의  트래픽을  방어하지  못한다. 우리는  세부  방화벽  설정을  위해  <em>Access Control 적용</em>을  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132981964-1bb5a112-29d5-4e6c-bcb3-74c02bd74c15.png" alt="image"></p>
<p>4.15 WAF 설정</p>
<p>위에서  진행한  L4 방화벽  작업이  완료되면  Application 방화벽인  WAF 설정이  필요하다.</p>
<p>**WAF(Web Application Firewall)**는  웹사이트와  앱  서비스를  보호하는  보안  이다.<br>
WAF는  악성  트래픽을  식별하고  트래픽을  scrubbing  및  필터링한  다음  일반  트래픽을  웹  서버로  전달한다.<br>
WAF는  공격으로부터  웹  서버를  보호하고  데이터와  비즈니스의  보안을  보장한다.</p>
<p>우리는  4.11에서  확인한  우리의  서비스를  위해  ‘<a href="http://meow.4zangnim.com">meow.4zangnim.com</a>’ 도메인을  사용할  것이다. 이  도메인을  위한  WAF 설정을  <em>WAF Console &gt; Asset Center &gt; Website Access &gt; Add Domain Name</em>에서  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982344-5136f3e6-7541-4bae-84d4-a182483694ba.png" alt="image"></p>
<ul>
<li><em>Domain Name</em> : 보호될  URL 정의, 여기서는  <a href="http://meow.4zangnim.com">meow.4zangnim.com</a> 사용</li>
<li><em>Protocol Type</em> : 도메인에서  사용될  프로토콜  정의</li>
<li><em>Destination Server IP</em> : WAF에  연결할  Origin Server IP 입력, 여기서는  SLB의  Public IP 입력</li>
<li><em>Destination Server Port</em> : 각  프로토콜에서  사용할  포트  정의</li>
<li><em>Load Balancing Algorithm</em> : WAF에서  사용할  LB 알고리즘  정의</li>
<li><em>L7 Proxy</em> : DDoS나  CDN을  WAF 앞에  구성할  때  사용</li>
</ul>
<p>이제, <em>Next</em>를  누르고  아래  설정  화면을  살펴본다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982425-90c2207d-b670-4008-8f4c-e80c94e67778.png" alt="image"></p>
<p>여기서  가장  중요한  부분은  바로  CNAME이다.<br>
원래  아래와  같은  로직을  위해서  CNAME을  DNS에  추가해야  하는데  우리는  WAF 앞에  Anti-DDoS를  배치할  것이므로  해당  값을  노트해  놓는다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982460-0d833535-5d0d-47eb-b1cb-3e49ad73f752.png" alt="image"></p>
<h3 id="anti-ddos-설정">4.16 Anti-DDoS 설정</h3>
<p>마지막으로, 우리의  애플리케이션을  DDoS로  부터  방어하기  위해  Anti-DDoS 설정을  이번  단계에서  수행한다.</p>
<p><strong>Anti-DDoS</strong>는 DDoS 공격을  완화하기  위해 Alibaba Cloud에서  제공하는  프록시  기반  완화  서비스이다. 이러한  서비스는  볼륨 Metric DDoS 공격으로부터  네트워크  서버를  보호하는  데  사용할  수  있다. 볼륨  및  리소스  고갈 DDoS 공격으로부터  서버를  보호하기  위해 Anti-DDoS는 DNS 확인을  사용하여  트래픽을 Alibaba Cloud Anti-DDoS 네트워크로  전달한다.</p>
<p><em>Anti-DDoS Console &gt; Website Config &gt; Add Domain</em>에서 DDoS 보호를 설정할 수 있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982507-f8a98f1b-39d3-479b-b1a3-068fe7186fc2.png" alt="image"></p>
<ul>
<li><em>Instance</em> : 방어에  사용할  Anti-DDoS 인스턴스  선택</li>
<li><em>Domain</em> : 방어가  될  Domain 입력, 여기서는  meow.4zangnim.com을  입력</li>
<li><em>Protocol</em> : 도메인에서  사용할  프로토콜  선택</li>
<li><em>Server IP</em> : Origin Server IP 혹은  Domain을  입력, <strong>여기서는  위  단계에서  노트한  WAF의  CNAME을  입력</strong></li>
</ul>
<p>위의  항목을  모두  입력한  후  <em>Add</em>를  클릭한다.</p>
<p>그  다음  아래에  나오는  항목들을  확인한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982572-adb0db69-fdc1-4497-9406-2af0c079f461.png" alt="image"></p>
<p>결과창에서 Anti-DDoS의  CNAME을  복사한다. 우리는 이  URI로  도메인  설정을  수행할  것이다.</p>
<h3 id="dns에--add-record">4.17 DNS에  Add record</h3>
<p>마지막  단계로  <strong>Anti-DDoS, WAF, Cloud Firewall</strong>이  모두  설정된  CNAME을  Alibaba Cloud DNS에  등록한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982610-da704669-e180-490e-9c17-0d487e0f0b5e.png" alt="image"></p>
<ul>
<li><em>Type</em> : CNAME 선택</li>
<li><em>Host</em> : 보호할  도메인인  <a href="http://meow.4zangnim.com">meow.4zangnim.com</a> 입력</li>
<li><em>Value</em> : Anti-DDoS에서  마지막에  도출된  CNAME 입력</li>
</ul>
<p>설정이  완료되었다면  <em>Confirm</em>을  눌러준다.</p>
<h2 id="결과">5. 결과</h2>
<h3 id="결과--페이지--확인">5.1 결과  페이지  확인</h3>
<p>우리는  정상적으로  서비스가  연결되었는지  확인하기  위해  브라우저에  연결된  도메인인  meow.4zangnim.com을  입력한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132982642-92c787e0-89f4-4bcb-8e53-d9abbca31238.png" alt="image"></p>
<p>만약  귀여운  고양이가  쳐다보고  있다면  모든  서비스가  정상적으로  연결된  것이다.</p>
<p>사실, 우리는  기업환경에서  서비스가  보안  플랫폼에  연결되었다고  완벽하게  안심하지는  않는다. 우리는  우리의  환경이  안전하게  보호되고있는지  확인하기  위해  Alibaba Cloud 서비스들의  모니터링  도구를  이용할  수  있다.</p>
<h3 id="anti-ddos에서--트래픽--모니터링--확인">5.2 Anti-DDoS에서  트래픽  모니터링  확인</h3>
<p><strong>Anti-DDoS</strong>에서는  Ingress Traffic이  정상적인지, 비정상적인지  판단하기  위해  Traffic의  크기를  집중적으로  분석한다.<br>
우리는  Anti-DDoS 콘솔에서  Instance, Domain 단위로 Traffic을  볼  수  있으며  가장  높은  수치의  Peak bandwidth 등을  확인할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132986810-24efde3b-c070-4eff-b361-d7bf1e7d27a6.png" alt="image"></p>
<h3 id="waf의--대시보드--확인">5.3 WAF의  대시보드  확인</h3>
<p>우리는  WAF의  대시보드에서  서비스  애플리케이션의  다양한  Application 공격을  모니터링  할  수  있다. 아래  보이는  이쁜  대시보드는  Alibaba Cloud의  Data Visualization 서비스인  <strong>DataV</strong>를  이용하여  사전  정의된  화면이다.</p>
<p>우리는  이  대시보드를  이용하여  Application에  대한  보안  운영을  쉽게  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132986858-040d1cd8-66a5-4911-ac95-b4c20d9de3ce.png" alt="image"></p>
<h3 id="cloud-firewall-모니터링">5.4 Cloud Firewall 모니터링</h3>
<p>우리는  또한  Cloud Firewall Console에서  Outbound Connection 관련  정보  등  네트워크  레이어의  모니터링을  할  수  있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/132986908-b8db801b-9eaf-461f-b7c9-1ba95437cdb2.png" alt="image"></p>
<h2 id="마무리">6. 마무리</h2>
<p>우리는  지금까지  Alibaba Cloud와  HashiCorp의  서비스를  이용해서  <strong>DevSecOps, 그  중  SecOps프로세스를  효율적으로  관리할  수  있는  아키텍쳐와  데모</strong>를  살펴보았다.</p>
<p>만약  이  예시를  응용하여  각  기업환경의  프로세스에  맞는  환경을  구성한다면  안전하고  빠른  운영  환경을  쉽게  구현할  수  있을  것이다.</p>
<blockquote>
<p>Alibaba Cloud를  운영하기  위해  Terraform Script를  찾고있다면  이  링크에서  다양한  예시  스크립트를  확인할  수  있다.<br>
<a href="https://github.com/aliyun/terraform-provider-alicloud">https://github.com/aliyun/terraform-provider-alicloud</a></p>
</blockquote>
<p>다음  블로그에서는  DevSecOps 중, DevSec의  영역을  Alibaba Cloud X HashiCorp로  구현하는  데모에  대해서  소개할  것이다.</p>

