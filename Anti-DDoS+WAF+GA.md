---


---

<h1 id="중국---글로벌간-네트워크-가속화-및-보안-강화를-위한-waf-anti-ddos-ga-구성-가이드">중국 - 글로벌간 네트워크 가속화 및 보안 강화를 위한 WAF, Anti DDoS, GA 구성 가이드</h1>
<h2 id="introduction-구성-요건">1. Introduction, 구성 요건</h2>
<h3 id="ga란">1.1 GA란?</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/145521812-ab2a4732-797d-45bb-b9ec-b0df19bb7675.png" alt="image"></p>
<p>GA는 가속 영역의 각 영역에 가속 IP 주소를 할당합니다. 이러한 지역의 클라이언트는 가속 IP 주소를 통해 클라이언트와 가장 가까운 액세스 포인트에 연결할 수 있습니다. 액세스 포인트는 클라이언트 요청을 수신하고 해당 요청을 Alibaba Cloud 글로벌 네트워크로 전달합니다. 그런 다음 GA는 클라이언트 요청을 최적의 끝점에 배포할 경로를 자동으로 선택합니다. 이는 네트워크 정체를 피하고 네트워크 대기 시간을 줄이는 데 도움이 됩니다. 엔드포인트는 SLB(서버 로드 밸런서) 인스턴스, ECS(Elastic Compute Service) 인스턴스, Alibaba Cloud 공용 IP 주소, 원본 서버의 사용자 지정 IP 주소 및 원본 서버의 사용자 지정 도메인 이름이 될 수 있습니다.</p>
<ul>
<li>Basic Bandwidth : 기본 대역폭 계획은 인터넷을 통해 그리고 Alibaba Cloud의 내부 네트워크 내에서 데이터 전송을 위한 대역폭을 제공합니다. 그러나 기본 대역폭 요금제는 중국 본토와 중국 본토 외부 지역 간의 데이터 전송에는 적용되지 않습니다.</li>
<li>Cross-border acceleration bandwidth plan : 중국 본토와 중국 본토 외부 지역 간의 데이터 전송 가속이 필요한 경우 국경 간 가속 대역폭 요금제를 구매해야 합니다.</li>
</ul>
<h3 id="구성-요건">1.2 구성 요건</h3>
<ol>
<li>ICP를 보유</li>
<li>중국에서 글로벌로 서비스를 제공</li>
<li>Alibaba Cloud 외부의 환경에 백엔드를 구성</li>
<li>GA의 Accelerated IP 지역을 3개로 구성</li>
</ol>
<h2 id="anti-ddos-waf-ga-구성-절차">2. Anti-DDoS, WAF, GA 구성 절차**</h2>
<p><a href="https://www.alibabacloud.com/help/en/doc-detail/160388.html"><strong>https://www.alibabacloud.com/help/en/doc-detail/160388.html</strong></a><br>
(+ Multiple endpoint : <a href="https://www.alibabacloud.com/help/doc-detail/200132.html">https://www.alibabacloud.com/help/doc-detail/200132.html</a>)</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523161-5d383023-8418-4a6e-a3d5-a6056b371bed.png" alt="image"></p>
<h3 id="ga-instance-구매를-위한-요건-확인">2.1 GA Instance 구매를 위한 요건 확인</h3>
<p>요건에 따라 GA의 Instance Spec과 Bandwidth의 형태가 달라질 수 있습니다.</p>
<p>참조 1) GA Instance specification</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523180-a3ae2462-c5f1-4341-a47f-4fe5b6c6f5de.png" alt="image"></p>
<p>참조 2) Bandwidth fee</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523265-a62477af-d8f4-4492-b399-c22c700184a4.png" alt="image"></p>
<h3 id="ga-instance-만들고-2가지basic-cross-border-bandwidth-바인딩">2.2 GA Instance 만들고 2가지(Basic, Cross-border) Bandwidth 바인딩</h3>
<p>먼저 GA Instance를 구매합니다.</p>
<p><em>GA 콘솔 우측 상단 &gt; Create Instance</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523316-b110ddad-fe88-49a9-b168-e08b5c68d8ca.png" alt="image"></p>
<ul>
<li>Specification : 각 요건에 따라 선택, 본 건에서는 <strong>Small III 선택</strong></li>
</ul>
<p>구매 후 구성된 Instance를 확인합니다.<br>
<img src="https://user-images.githubusercontent.com/34003729/145523370-487b1ec3-3aa2-4566-aa23-51cc75cea38e.png" alt="image"></p>
<p>스크린 샷의 Basic/Cross-border Bandwidth에서 Bind Now를 눌러 각 Bandwidth를 구매하고 바인딩합니다.</p>
<p><em>Basic Bandwidth Bind now &gt; purchase</em><br>
<img src="https://user-images.githubusercontent.com/34003729/145523421-5ee6e809-878e-406e-967a-cba62f5de77f.png" alt="image"></p>
<ul>
<li>Peak Bandwidth : 가장 트래픽이 몰렸을 때의 Bandwidth를 예상하여 산정</li>
<li>Bandwidth Type : Basic은 중국에서만 Bandwidth를 처리할 경우, Enhanced는 Cross-Border가 필요할 경우 사용. 본 건에서는 <strong>Enhanced 를 선택</strong></li>
</ul>
<p><em>Cross-border Acceleration Bandwidth Bind now &gt; purchase</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523510-9b29722c-ef3c-49e3-ab0c-fe4df8b4ee77.png" alt="image"></p>
<ul>
<li>Bandwidth : 가속화할 Bandwidth를 산정, 만약 가속화 지역이 3개이며 각 지역에서 5Mb 만큼의 Bandwidth가 필요하다면 <strong>15 Mb</strong> 선택</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/145523573-e95554b6-a479-4a0e-b866-f2c56d04fbb9.png" alt="image"></p>
<p>바인딩된 두 가지 Bandwidth 확인</p>
<h3 id="listener-및-end-point추가">2.3 Listener 및 End Point추가</h3>
<p>GA인스턴스에 가속화를 구현하기 위한 두 가지 컴포넌트인 Listener와 End Point를 추가합니다.</p>
<p><em>GA 콘솔 &gt; Standard Instance &gt; Instance ID &gt; Listener &gt; Add Listener 클릭</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523644-8ab28ef0-10a6-46a1-953b-20290e0cbc7a.png" alt="image"></p>
<ul>
<li>클라이언트에서 사용할 Protocol과 Port Number를 정의. 본 건에서는 <strong>HTTP/80을 사용</strong></li>
<li>Client Affinity : Source Ip Address를 drop-down에서 사용할 수 있으며, 이 옵션이 enable 되면 특정 Client IP는 같은 endpoint로 트래픽이 전송 됨</li>
</ul>
<p>Next를 클릭하여 다음 구성으로 넘어갑니다.</p>
<h3 id="default-endpoint-group-설정">2.4 Default Endpoint Group 설정</h3>
<p>GA에서는 Layer 7 스마트 라우팅 기능을 지원합니다. 본 시나리오에서는 도메인 기반 스마트 라우팅을 설정합니다.</p>
<p>HTTP 리스너에 대한 Default Endpoint Group 및 Virtual Endpoint Group을 구성할 수 있습니다.</p>
<ul>
<li>Default Endpoint Group : 리스너를 생성할 때 Default Endpoint Group을 구성해야 합니다. 시스템은 Default Endpoint Group에 대한 기본 Forwarding Rule을 자동으로 추가합니다. 사용자 지정 Forwarding Rule의 일치 조건을 충족하지 않는 요청은 기본 Forwarding Rule의 Default Endpoint Group으로 전달됩니다.</li>
<li>Virtual Endpoint Group : 리스너를 만든 후 Virtual Endpoint Group을 만들고 Endpoint Group페이지에서 Virtual Endpoint Group에 대한 사용자 지정 Forwarding Rule을 추가할 수 있습니다. GA는 Forwarding Rule의 일치 조건을 충족하는 요청을 연결된 최적의 엔드포인트로 전달합니다.</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/145523760-8ff5002b-d254-4f7d-a37c-e8d157afe077.png" alt="image"></p>
<p>먼저 Server A를 위한 Default endpoint group을 설정합니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523828-1ee8aaf2-279d-490a-9660-71d9a3608716.png" alt="image"></p>
<ul>
<li>Region : 중국 본토로부터 가속화할 지역을 선택. 본 건에서는 <strong>China (Hong Kong)을 선택</strong></li>
<li>Backend Service : 백엔드로 구성된 서버가 Alibaba Cloud의 서비스인지, Alibaba Cloud 외부의 서비스인지 선택. 본 건에서는 <strong>Off Alibaba Cloud 선택</strong></li>
<li>Preserve Client IP : 이 옵션을 Enable하면 백엔드 서비스에서 Original IP를 확인 가능</li>
<li>Endpoint : 각 백엔드에 접속을 위한 Endpoint를 정의. IP/Domain으로 정의할 수 있으며 Blue/Green, Canary 배포가 필요할 경우 Weight 정의 가능. <strong>본 건에서는 Server A의 IP 입력</strong></li>
</ul>
<p>모든 설정을 확인하여 Listener와 Endpoint 설정을 완료합니다.</p>
<h3 id="virtual-endpoint-group-설정">2.5 Virtual Endpoint Group 설정</h3>
<p>GA의 리스너에서 Layer 7의 forwarding rule로 도메인 조건에서 분기하기 위해 Server B를 위한 Virtual Endpoint Group을 추가합니다.</p>
<p><em>GA 콘솔 &gt; Instance 클릭 &gt; 구성한 Listener ID 클릭 &gt; Endpoint Group 선택</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145523974-a6bb563f-1e46-4b87-b7a8-9cc483ccbc68.png" alt="image"></p>
<p>Server B에 대한 Endpoint 추가</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524048-fce028bb-4793-4161-9b23-195e12904a37.png" alt="image"></p>
<ul>
<li>Default Endpoint Group을 만들 때와 같은 형태로 Configuration 추가</li>
</ul>
<h3 id="forwarding-rule-추가">2.6 Forwarding Rule 추가</h3>
<p>Server B에 대한 Forwarding Rule을 추가합니다. Server B 도메인으로 접근하는 트래픽 이외에는 모두 default endpoint group으로 라우팅되므로 Forwarding Rule은 Server B를 위한 구성 하나면 충분합니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524108-2ae24dfe-ffdb-4158-aca9-97f63c81ab19.png" alt="image"></p>
<ul>
<li>IF : Domain Name 선택하여 조건을 넣을 domain을 명시. <strong>본 건에서는 servera.test.com을 입력</strong></li>
<li>Forward to Virtual Endpoint Group : 조건에 맞는 트래픽일 경우에 라우팅할 Virtual Endpoint Group을 선택. <strong>본 건에서는 Server B를 위한 Virtual Endpoint group 선택</strong></li>
</ul>
<h3 id="acceleration-area-추가">2.7 Acceleration area 추가</h3>
<p><em>GA 콘솔 – Standard Instance – Instance ID – Acceleration Area클릭</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524234-e3f6a9f6-4d22-44e3-ae76-028af5172e10.png" alt="image"></p>
<ul>
<li>Add Region 선택</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/145524279-e743bc90-f535-4b9a-a699-0d4991904f41.png" alt="image"></p>
<ul>
<li>Regions : 가속화가 필요한 리전 선택</li>
<li>Bandwidth : 해당 지역에 부여할 Bandwidth 선택</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/145524448-6582fe56-e5c6-4393-8309-bd944f5539d9.png" alt="image"></p>
<p>생성된 Accelerated IP Address 체크합니다.</p>
<h3 id="ga-instance의-cname-확인">2.8 GA Instance의 CNAME 확인</h3>
<p><em>GA 콘솔 &gt; Instance 선택 &gt; Instance information 확인</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524492-b6aa740f-b2e5-41a0-bc0e-9f0992094774.png" alt="image"></p>
<ul>
<li>WAF의 백엔드 설정에 필요한 정보인 GA instance의 CNAME을 노트</li>
</ul>
<h3 id="waf-설정">2.9 WAF 설정</h3>
<p><em>WAF Console &gt; Purchase WAF subscription</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524543-bf3e0894-bf9c-4a1b-a699-525ee0cfaf91.png" alt="image"></p>
<ul>
<li>Region : 가속화할 지역 선택. <strong>본 건에서는 China Mainland 선택</strong></li>
<li>Plan : 구성할 WAF 형태 선택. 구분은 아래서 확인 가능</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/145524591-01c3def0-2b75-4b00-9124-e3419272ed9d.png" alt="image"></p>
<ul>
<li>Bot Manager : Enable하면 구성된 봇으로 웹 공격에 대한 탐지를 보다 강화할 수 있습니다. 상세는 해당 링크에서 확인할 수 있습니다. <a href="https://www.alibabacloud.com/help/doc-detail/159911.htm#task-vfm-vdl-l2b">Set a bot threat intelligence rule</a></li>
<li>Mobile App Protection : 만약 모바일 앱 서비스를 지원하는 서비스를 구성할 경우 enable</li>
<li>Extra Domain : 추가적인 Domain 구매, 10개 이상의 서브도메인을 구성할 때 추가 구매 필요</li>
</ul>
<h3 id="waf에서-add-website-configuration">2.10 WAF에서 Add website configuration</h3>
<p>WAF에 도메인을 설정합니다.</p>
<p><em>WAF Console &gt; Asset Center &gt; Website Access &gt; Add Domain Name</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524662-58264e4a-0ed4-4e91-ad59-00f3457b13d3.png" alt="image"><br>
<img src="https://user-images.githubusercontent.com/34003729/145524723-800b10ce-7b75-4f98-b313-e44c39fd306f.png" alt="image"></p>
<ul>
<li>Domain Name : 도메인 네임 입력. *<em>본 건에서는 <em>.test.com을 입력</em></em></li>
<li>Protocol Type : <strong>HTTP, HTTPS(SSL Offload를 위해 enable USE HTTP 선택)</strong></li>
<li>Domain : 앞에서 노트한 <strong>GA Instance의 CNAME 입력</strong></li>
<li>Http Port : <strong>80</strong></li>
<li>Load Balancing Algorithm : IP hash or Round Robin이지만 본 조건에서는 CNAME을 백엔드로 두기 때문에 의미 없음</li>
<li>Does a layer 7 proxy exist in from of WAF : <strong>YES, 첫번째 선택</strong> (만약 X-forwarded-for 방어를 하기 위해 헤더를 수정한 로직이라면 2번째 선택)</li>
<li>Enable traffic mark : 헤더에 특정 값을 기록해서 모니터링 할 수 있도록 설정. 본 설정에서는 선택 안함</li>
</ul>
<p>NEXT를 누르면 나오면 CNAME을 노트합니다. 해당 값은 Anti-DDoS의 백엔드 입력시 사용됩니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145524955-7be47160-22c6-4ba9-aea1-afccef8f0c36.png" alt="image"></p>
<h3 id="anti-ddos-설정">2.11 Anti-DDoS 설정</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/145525001-90abc476-119f-4bed-b74d-ac123cb74770.png" alt="image"></p>
<ul>
<li>Type : Anti-DDoS Pro (Mainland China) 선택</li>
<li>Basic Protection : 기본으로 막아주는 최대 Protection 규모</li>
<li>Burstable protection : Basic 용량을 초과했을 때 추가로 방어 가능한 규모, pay-as-you-go로 빌링</li>
<li>Business Scale : 기본 예상되는 Business 트래픽 양, 초과시 패킷 로스나 성능 저하가 생길 수 있음</li>
<li>Function Plan : Standard 선택 / Enhanced는<br>
Supports Non-Standard ports for HTTP(S) and WebSocket(s)<br>
Supports GeoIP blocking rules (i.e. Allow access from Europe only)<br>
Supports custom application layer Access-Control-Rules<br>
Supports static website caching<br>
Supports for custom TLS protocol versions and cipher suites<br>
Supports for CDN Interaction<br>
의 케이스에서 사용</li>
<li>Domain : 인스턴스에 추가할 수 있는 HTTP 및 HTTPS 도메인 이름의 수를 지정</li>
</ul>
<h3 id="anti-ddos-website-config-설정">2.12 Anti-DDoS Website Config 설정</h3>
<p>이제 Anti-DDoS의 도메인을 설정합니다.</p>
<p><em>Anti-DDoS Pro &gt; Provisioning &gt; Website Config &gt; Add Domain</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145525108-65815101-ed45-42ec-b3b8-c0850866199b.png" alt="image"></p>
<ul>
<li>Domain : ICP가 등록된 도메인을 입력( * 같은 wildcard entry 가용 가능)</li>
<li>Protocol : HTTP, HTTPS, Websocket, Websockets 모두 선택</li>
<li>Server IP : 위 단계에서 노트한 WAF의 CNAME 입력</li>
</ul>
<p>또한 생성된 Anti-DDoS 인스턴스의 CNAME을 노트합니다. 이 값은 다음 단계인 DNS 추가에 사용됩니다.</p>
<h3 id="dns-추가">2.13 DNS 추가</h3>
<p>DNS에 각 서비스에 대한 도메인을 CNAME으로 매칭합니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145525161-cbd77068-a467-4362-9f6c-759a2b0f0b14.png" alt="image"></p>
<ul>
<li>Type : CNAME 선택</li>
<li>Host : 접속할 Subdomain 입력</li>
<li>Value : 노트한 Anti-DDoS의 CNAME 입력</li>
</ul>
<p>이후 테스트를 진행하면 정상적으로 서버 연결이 되는 것을 확인할 수 있습니다.</p>
<p><strong>2.14 Anti-DDoS Access Log 모니터링</strong></p>
<p>먼저 기본적으로 제공하는 Overview 화면 우측 하단에서 어느 지역에서 접속했는지 접속 추이를 확인할 수 있습니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/145525234-5a060ef3-bfb0-4ae7-aabd-8455e973ddd3.png" alt="image"></p>
<p>또한 로그 서비스를 활성화하면 Access Log 를 대시보드 형태로 확인할 수 있습니다.</p>
<p><em>Anti-DDoS 콘솔 &gt; Investigation &gt; Log Analysis &gt; Log Reports &gt; Access Center</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145525289-3c9c853d-c063-49c1-a160-69fe63072d8c.png" alt="image"></p>
<p>만약, 특정 지역에서 몇 건의 Access가 발생했는지 확인하고 싶으면 아래의 가이드를 따라 쿼리를 확인할 수 있습니다.</p>
<p><em>Anti-DDoS 콘솔 &gt; Investigation &gt; Log Analysis &gt; 입력창에 아래 쿼리 입력</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/145525348-f1c16cee-cd8f-4754-9094-b14dff9f4413.png" alt="image"></p>
<pre><code>host: aliyun.com |select ip_to_city(real_client_ip) ,ip_to_country(real_client_ip),ip_to_city(real_client_ip),ip_to_geo(real_client_ip),ip_to_province(real_client_ip),ip_to_provider(real_client_ip) ,COUNT(*) as c group by ip_to_city(real_client_ip) ,ip_to_country(real_client_ip),ip_to_city(real_client_ip),ip_to_geo(real_client_ip),ip_to_province(real_client_ip),ip_to_provider(real_client_ip) order by c desc limit 99999
</code></pre>
<h2 id="결과-확인">3. 결과 확인</h2>
<h3 id="curl-이용">3.1 Curl 이용</h3>
<pre><code>curl -o /dev/null -s -w "time_connect: %{time_connect}\ntime_starttransfer: %{time_starttransfer}\ntime_total: %{time_total}\n" "&lt;domain_name&gt;"
</code></pre>
<ul>
<li>time_connect: the period of time to establish a TCP connection.</li>
<li>time_starttransfer: the period of time for the backend server to send the first byte after the client sends a request.</li>
<li>time_total: the period of time for the backend server to respond to the session after the client sends a request.</li>
</ul>
<h3 id="mtr-리포트-수행">3.2 MTR 리포트 수행</h3>
<pre><code>mtr -n -T -c 200 &lt;domain_name&gt; --report
</code></pre>
<h2 id="마무리">4. 마무리</h2>
<p>지금까지 Anti-DDoS와 WAF를 이용하여 보안을 강화하고 GA로 중국-글로벌 간 네트워크를 가속화하는 구조를 구성하는 방법에 대해서 알아보았습니다.</p>
<p>GA를 이용하면 기존 Latency가 많고 Jitter가 많이 생기는 국가간 네트워크 구간에서 안정화를 가지게 되며 서비스의 퀄리티를 높일 수 있게 됩니다.</p>
<p>만약 해당 구성 사항이나 테스트에 대해 문의가 있으시면 아래 메일로 연락주시기 바랍니다.</p>
<p><a href="mailto:cloudkorea@list.alibaba-inc.com">cloudkorea@list.alibaba-inc.com</a></p>

