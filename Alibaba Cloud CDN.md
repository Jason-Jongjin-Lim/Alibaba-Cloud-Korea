---


---

<h1 id="alibaba-cloud-cdn-소개-및-구성-퍼포먼스-튜닝-튜토리얼-타사-cdn과의-퍼포먼스-비교">Alibaba Cloud CDN 소개 및 구성, 퍼포먼스 튜닝 튜토리얼 (타사 CDN과의 퍼포먼스 비교)</h1>
<h2 id="alibaba-cloud-cdn-소개">1. Alibaba Cloud CDN 소개</h2>
<h4 id="intro">Intro</h4>
<p>Alibaba Cloud CDN은  전  세계적으로  분산된  엣지  노드로  구성된  가상  네트워크입니다.</p>
<p>이  서비스를  사용하여  다양한  지역, 시나리오의  웹  사이트에  대한  콘텐츠  전달을  가속화할  수  있습니다.</p>
<p>Alibaba Cloud CDN은  전  세계적으로  분산된 <strong>2,800개  이상의  에지  노드</strong>를  제공합니다. 이  중 2,300개  이상의  노드가  중국  본토의 31개  성  지역에  분포되어  있으며 500개  이상의  노드가  홍콩(중국), 마카오(중국), 대만(중국)을  포함한 70개  이상의  국가  및  지역에  분포되어  있습니다. 또한  이  서비스의  <strong>대역폭  총량은  최대 150Tbit/s</strong>에  달합니다.</p>
<p>Alibaba Cloud CDN은  오리진  서버에서  전  세계적으로  분산된  엣지  노드로  리소스를  캐시합니다. 사용자는  오리진  서버  대신  가장  가까운  엣지  노드에서  리소스를  검색할  수  있습니다. 이  메커니즘으로  콘텐츠  전달을  가속화하고  오리진  서버의  로드를  줄일  수  있습니다.</p>
<h4 id="장점">장점</h4>
<p>Alibaba Cloud CDN서비스를  이용하여  Static, Dynamic 콘텐츠를  모두  가속화  할  수  있습니다. 또한  Alibaba Cloud CDN은  아래  같은  많은  장점을  지니고  있습니다.</p>
<ul>
<li><strong>광범위하게  분산된  노드</strong>: Alibaba Cloud CDN은  클라이언트와  동일한  인터넷  서비스  공급자(ISP)에  속하는  가장  가까운  노드로  요청을  리디렉션합니다. 이  메커니즘으로  장거리  콘텐츠  전송을  가속화하고 ISP 간  콘텐츠  전송을  위한  네트워크  대기  시간을  줄일  수  있습니다.</li>
<li><strong>확장이  용이한  리소스</strong>: Alibaba Cloud CDN은  리소스  확장성과  서비스  가용성을  보장하기  위해  전  세계적으로  분산된 2,800개  이상의  엣지  노드를  제공합니다.</li>
<li><strong>스케줄링</strong>: Alibaba Cloud CDN은  엣지  노드의  상태를  실시간으로  모니터링하고  클라이언트  위치  및 ISP를  기반으로  스케줄링  시스템이  선택한  최적의  노드로  요청을  리디렉션합니다. 이것은  네트워크의  가속도를  향상시킵니다.</li>
<li><strong>영리한  연결</strong>: Alibaba Cloud CDN은  프로토콜, 커넥션  최적화와  같은  최적화  전략을  사용하여  특히  불안정한  네트워크에서  네트워크  대기  시간을  줄이고  콘텐츠  전달을  가속화합니다.</li>
<li><strong>IT 비용  절감</strong>: 에지  노드는  컴퓨팅, 대역폭  및  연결  리소스를  제공합니다. 이를  통해 IT 비용을  대폭  절약할  수  있습니다.</li>
<li><strong>고대역폭  용량</strong>: Alibaba Cloud CDN서비스로  최대  150Tbit/s의  대역폭을  활용할  수  있습니다.</li>
<li><strong>표준화된 API</strong>: Alibaba Cloud CDN API를  이용하여  외부  시스템과의  쉬운  연결을  지원합니다.</li>
</ul>
<h4 id="cdn-dcdn-서비스--비교">CDN, DCDN 서비스  비교</h4>
<p><strong>CDN</strong> : Static  콘텐츠의  전달을  가속화하고  Dynamic  콘텐츠에  대한  요청을  오리진  서버로  리디렉션합니다. Alibaba Cloud CDN은  높은  대역폭이  필요하고  많은  양의  네트워크  트래픽을  처리해야  하는  시나리오에  적합합니다.</p>
<ul>
<li>Cache expiration  규칙에  따라  콘텐츠를  캐시할  수  있는 2,800개  이상의  엣지  노드를  제공합니다.</li>
<li>오리진  서버의  Load Balancing을  조정하고  가중치를  기반으로  오리진  서버에  요청을  분산합니다.</li>
<li>가장  가까운  노드에서  클라이언트로  콘텐츠를  전달하기  위해  엣지  노드에  이미지  및  비디오와  같은  Static  콘텐츠를  캐시합니다.</li>
<li>애플리케이션  계층: HTTP, HTTPS 및 QUIC(Quick UDP Internet Connections)</li>
<li>네트워크  계층: IPv4 및 IPv6</li>
</ul>
<p><strong>DCDN</strong> : Dynamic 콘텐츠  또는  Dynamic, Static  콘텐츠  두가지  동시의  전달을  가속화합니다.</p>
<ul>
<li>Dynamic  콘텐츠  전달  가속화 : POST 요청과  같은  일부  요청에서  요청한  콘텐츠가  엣지  노드에  캐시되지  않은  경우  엣지  노드는  요청을  오리진  서버로  리디렉션하여  지능형  스케줄링  시스템을  기반으로  콘텐츠를  검색합니다. 시스템은  리디렉션을  위한  최적의  경로를  검색합니다.</li>
<li>Dynamic  및  Static  콘텐츠  두가지  동시의  전달  가속화: Static  및  Dynamic  콘텐츠를  지능적으로  식별합니다. Static  콘텐츠는  엣지  노드에  캐시됩니다. 사용자는  가장  가까운  엣지  노드에서  캐시된  Static  콘텐츠를  직접  검색할  수  있습니다. Dynamic  콘텐츠에  대한  요청은  지능형  스케줄링  시스템이  선택한  최적의  경로를  통해  오리진  서버로  리디렉션됩니다.</li>
<li>애플리케니션  계층: HTTP, HTTPS, 및 WebSocket</li>
<li>전송  계층: TCP 및 UDP</li>
<li>네트워크  계층: IPv4 및 IPv6</li>
</ul>
<h2 id="architecture">2. Architecture</h2>
<p><img src="https://user-images.githubusercontent.com/34003729/140638414-bc08c65d-add0-4d43-88f0-17b50a71f451.png" alt="image"></p>
<ul>
<li><strong>Scheduling system</strong> : 스케줄링  시스템은  정책  센터를  제공하고 DNS(Domain Name System) 확인, HTTP DNS 및 302 리디렉션을  지원합니다. 클라이언트가  요청을  보낸  후  요청은 DNS에  의해  확인된  다음  스케줄링  시스템에서  처리됩니다.</li>
<li><strong>Link quality system</strong> : 링크  품질  시스템은  모든  노드와  링크의  부하  및  상태를  실시간으로  모니터링하고  모니터링  데이터를  스케줄링  시스템에  전송합니다. 그  이후  스케줄링  시스템은 ISP(Internet Service Provider) 및  요청  지역과  같은 IP 주소  정보를  기반으로  요청에  대한  최적의  액세스  포인트를  제공합니다.</li>
<li><strong>Caching system</strong> : 캐싱  시스템은  사용자  접속  요청을  엣지  노드로  리디렉션합니다. 요청한  리소스가  이미  엣지  노드에  캐시되어  있으면  리소스가  클라이언트에  직접  반환됩니다. 요청된  리소스가 L1 또는 L2 노드에  캐시되지  않은  경우  요청은  리소스를  검색하기  위해  오리진  서버로  리디렉션됩니다. 그런  다음  검색된  리소스가  클라이언트로  반환되고  엣지  노드에  캐시됩니다. 후속  요청은  엣지  노드에서  리소스를  직접  검색할  수  있습니다. Alibaba Cloud CDN은  다단계  캐시  계층  구조를  사용하여  콘텐츠  전달을  가속화하고, back to origin 프로세스에서  소비하는  대역폭  리소스의  양을  줄입니다.</li>
</ul>
<h2 id="cdn-구성을--위한--check-list">3. CDN 구성을  위한  Check List</h2>
<p>CDN을 구성할 때 현재 서비스에 대한 분석을 사전 진행해야합니다. 사전 분석은 아래 체크 리스트를 참조할 수 있습니다.</p>
<ol>
<li>연결  사이에서 LB나 Sticky Session을  사용하는가? 사용한다면  어떤  종류의 LB(Alteon, Foundry, etc.) 와  어떤  로직의 Sticky(round robin with IP, SSL session, or Cookie stickiness?)를  사용하는가?</li>
<li>연결하는  도메인은 HTTP/HTTPS를  사용하는가?</li>
<li>연결하는  도메인은  무엇이며 Origin Server의  지역은  어디인가?</li>
<li>Hostname은  다른  종류의 DNS record(such as MX)를  가지고  있는가?</li>
<li>SSL을  사용하지  않는  도메인과 SSL을  사용하는  도메인이  서로  다른가?</li>
<li>Top Level Domain을  연결하는가? (like <a href="http://example.com">example.com</a>)</li>
<li>Caching을  설정할 Contents에  비밀번호  같은  보호가  필요한  정보가  포함되는가?</li>
<li>연결하는  사이트에  별도의 Redirection 정책이  적용되는가?</li>
<li>연결하는  사이트에 authentication 혹은 authorization(cent-auth, WAF, origin white list etc.) 정책이  적용되는가?</li>
<li>연결하는  사이트에  개인정보가  저장되는가? 혹은  개인정보가  포함되면  안되는  서비스인가?</li>
<li>연결되는 Contents의  형태는 Static(이미지, 텍스트, 비디오  등)인가? Dynamic(Script, POST/GET Method 등)인가? 혹은  둘  다인가?</li>
</ol>
<h2 id="alibaba-cloud-cdn-설정">4. Alibaba Cloud CDN 설정</h2>
<p><img src="https://user-images.githubusercontent.com/34003729/140640318-0fe36ab5-8d93-44bc-bda7-72417fd81152.png" alt="image"></p>
<p>Alibaba Cloud CDN 설정을  위한  단계는  크게  아래의 5단계로  이루어집니다.</p>
<ol>
<li>Alibaba Cloud CDN 사용을  위해  활성화</li>
<li>가속화할 Domain name을  활성화된 Alibaba Cloud CDN에  추가</li>
<li>Cache Expiration에  대한  설정  추가</li>
<li>설정된 Domain name이  정상적으로  가속화  되는지  확인</li>
<li>Domain을  위한 CNAME 추가</li>
</ol>
<h3 id="가입한-international-계정에-cdn-activate-설정">4.1 가입한 International 계정에 CDN Activate 설정</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/140640369-1f97dfe2-a8aa-4d4b-b648-4d4107129926.png" alt="image"></p>
<ul>
<li>좌측  상단의  메뉴  패널  선택 -&gt; <em>Alibaba Cloud CDN</em> 클릭</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/140640383-2b675b35-0ea5-4c16-a7b9-44591516d0d4.png" alt="image"></p>
<ul>
<li>Terms of Service 확인  후 <em>Activate Now</em> 클릭</li>
</ul>
<h3 id="cdn-콘솔--접속">4.2 CDN 콘솔  접속</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/140640402-a518f570-b480-4466-8b51-1aef7d181028.png" alt="image"></p>
<ul>
<li>콘솔에  정상적으로  접근이  되는지  확인  후  좌측  패널의 Domain Name 클릭</li>
</ul>
<h3 id="테스트할-domain-name-추가">4.3 테스트할 Domain Name 추가</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/140640419-94c1e65f-9655-4133-9faa-c1b888ea69aa.png" alt="image"></p>
<ul>
<li>Domain Names 화면에서 <em>Add Domain Name</em> 클릭</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/140640469-90ebe327-689c-4ffd-8d3e-62f3da00f1d7.png" alt="image"></p>
<ul>
<li><strong>Domain Name to Accelerate</strong> : 테스트할 Domain Name을  입력합니다. 이  곳에는 Subdomain이나 wildcard domain을  사용할  수  있습니다.  (Wildcard Entry를  사용하실  경우  몇가지  추가  설정이  필요합니다. 해당  내용은  링크에서  확인하실  수  있습니다. <a href="https://www.alibabacloud.com/help/doc-detail/40103.htm?spm=a2c63.p38356.879954.9.2a8763ee2EaylY#trouble-1779952">https://www.alibabacloud.com/help/doc-detail/40103.htm?spm=a2c63.p38356.879954.9.2a8763ee2EaylY#trouble-1779952</a></li>
<li><strong>Business Type</strong> : CDN으로  기본적으로  수행할  작업  타입을  설정합니다.
<ul>
<li><em>Image and small file distribution :</em> 작은  사이즈의  정적  컨텐츠를  가속화할  때  사용</li>
<li><em>Large file distribution</em> : 20MB가  넘는  큰  사이즈의  정적  컨텐츠를  가속화  할  때  사용</li>
<li><em>On-demand audio and video streaming</em> : 오디오나  비디오  컨텐츠를  가속화  할  때  사용</li>
<li><em>DCDN</em> : Dynamic 컨텐츠를  가속화할  때  주로  사용 (해당  기능을  사용하시려면  추가  설정이  필요합니다.)</li>
</ul>
</li>
<li><strong>Region</strong> : 가속화에  사용할 CDN Node의  리전  선택
<ul>
<li><em>Mainland China Only</em> : 중국  본토에  배포된 CDN Node 사용시  선택</li>
<li><em>Global</em> : 중국을  포함한  전  세계에  배포된  가장  가까운 CDN Node 사용시  선택 (ICP Filling 필요)</li>
<li><em>Global (Excluding Mainland China)</em> : 중국을  제외한  나라에  배포된  가까운 CDN Node 사용시  선택</li>
</ul>
</li>
<li><strong>Add Origin Server</strong> : 리소스가 CDN Node에  의해서 Cache가  안되었을  시, Redirect를  수행할 Origin Server 설정</li>
</ul>
<h3 id="origin-server-추가">4.4 Origin Server 추가</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/140640688-18b1a09a-3291-4e0d-91e0-311a61c23e62.png" alt="image"></p>
<ul>
<li><strong>Origin info</strong> : Origin server의  정보를  입력
<ul>
<li><em>OSS Domain</em> : Alibaba Cloud에서  제공하는 Object Storage service의 public endpoint를  입력</li>
<li><em>IP :</em> Origin server의 Public IP 입력</li>
<li><em>Site Domain</em> : Origin Server의 domain name 입력</li>
<li><em>Function Compute Domain</em> : 사용  중인 Alibaba Cloud 계정에  속해있는 Function Compute domain name 입력</li>
</ul>
</li>
<li><strong>Priority</strong> : 다수의 Origin server를  입력했을  시의 Primary/Secondary 선택</li>
<li><strong>Weight</strong> : Redirect할 Origin server들의 Weight 설정, 1-100까지  설정  가능하며  높은  숫자일  수록  많은  양의 request를  처리</li>
<li><strong>Port</strong> : Origin Server로  접속할 Port 입력, Custom port를  사용할  시 Ticket 처리  필요</li>
</ul>
<h3 id="cache-rule-설정">4.5 Cache Rule 설정</h3>
<p><em>Domain Name</em> 설정  후 <em>Next</em> 클릭, <em>Cache Rule</em> 설정  화면  접속<br>
<img src="https://user-images.githubusercontent.com/34003729/140640756-09afaf08-7f7d-400c-b320-1394cccfd734.png" alt="image"></p>
<ul>
<li><strong>Cache Expiration</strong> : CDN node에  캐싱될  정적  컨텐츠에  대한 TTL을  설정</li>
<li><strong>Parameter Filtering (Retain Specified Parameters) :</strong> CDN node의 Hash key를  생성할  때 URL에  포함된 (?)마크에  따라오는  파라미터를  유지할  때  사용</li>
<li><strong>Parameter Filtering (Delete Specified Parameters)</strong> : CDN node의 Hash key를  생성할  때 URL에  포함된 (?)마크에  따라오는  파라미터를  삭제할  때  사용</li>
<li><strong>Bandwidth Cap</strong> : Bandwidth의  상한선  설정시  사용, 본  기능은 CloudMonitor를  기반으로  사용하여  해당  기능에  대한 Authorize 필요</li>
</ul>
<h3 id="cdn-performance-증가를--위한--기본--optimization">4.6 CDN Performance 증가를  위한  기본  Optimization</h3>
<p><img src="https://user-images.githubusercontent.com/34003729/140640801-c44a1699-b827-4746-bc10-81055b056a86.png" alt="image"></p>
<ul>
<li><strong>HTML Optimization</strong> : HTML/CSS/JavaScript에  포함된 comment/duplicate white spaces 등에  대해 Optimization 수행</li>
<li><strong>Object Chunking</strong> : 주로  매우  큰  사이즈의  파일을  전송할  때  사용 (세부  내용은  링크  참조  가능 <a href="https://www.alibabacloud.com/help/doc-detail/27129.htm?spm=5176.11785003.domainAdd.7.59484a14hX1K33">https://www.alibabacloud.com/help/doc-detail/27129.htm?spm=5176.11785003.domainAdd.7.59484a14hX1K33</a>)</li>
<li><strong>Intelligent Compression</strong> : Gzip을  사용하여  정적  파일  컨텐츠를  압축 / 파일의  사이즈  축소</li>
</ul>
<h3 id="cdn에--설정된-domain-name-확인">4.7 CDN에  설정된 Domain Name 확인</h3>
<p>CDN 설정  이후  가속화된  URL인  CNAME을  확인할  수  있습니다. 이  CNAME을  다음  단계인  DNS 설정을  위해  Note합니다.<br>
<em>Domain Names &gt; copy the CNAME</em><br>
<img src="https://user-images.githubusercontent.com/34003729/140640880-b6c9c956-9313-4294-84c2-732b44bbd6de.png" alt="image"></p>
<blockquote>
<p>참조 : <a href="https://www.alibabacloud.com/help/doc-detail/208994.htm?spm=a2c63.p38356.b99.31.3c094c08NWGrFd">https://www.alibabacloud.com/help/doc-detail/208994.htm?spm=a2c63.p38356.b99.31.3c094c08NWGrFd</a></p>
</blockquote>
<h3 id="cname-설정---alibaba-cloud-dns를--사용할--경우">4.8 CName 설정  : Alibaba Cloud DNS를  사용할  경우</h3>
<p>만약, 서비스  접속을  위한  경로  중  DNS를  알리바바  클라우드의  DNS를  사용한다면  아래  절차대로  CNAME 설정이  필요합니다.</p>
<p><em>DNS 콘솔  &gt; Manage DNS (등록된  도메인  선택) &gt; DNS Setting &gt; Add Record</em></p>
<p><img src="https://user-images.githubusercontent.com/34003729/140640926-6211a042-39b0-47af-a07f-aac2fd1fa5db.png" alt="image"></p>
<ul>
<li><strong>Type</strong> : CNAME 선택</li>
<li><strong>Host</strong> : 실제  유저가  접속할  URL 입력</li>
<li><strong>ISP Line</strong> : 사용할  ISP Vendor 선택</li>
<li><strong>Value</strong> : 위의  CDN 설정에서  노트한  CNAME value 입력</li>
</ul>
<h2 id="alibaba-cloud-cdn-퍼포먼스--튜닝--가이드">5. Alibaba Cloud CDN 퍼포먼스  튜닝  가이드</h2>
<p>보통  기업에서  CDN을  사용할  때, 설정된  값  그대로를 사용하지  않습니다. 구성된  서비스에  따라  퍼포먼스를  증가시키기  위해  아래  여러가지  퍼포먼스  튜닝  가이드를  참조할  수  있습니다.</p>
<h3 id="cache-expiration-rule-설정">5.1 Cache expiration rule 설정</h3>
<p>CDN에 Caching될 Contents의 TTL을  설정합니다. 이  값이  너무  짧으면  back-to-origin 프로세스가  자주  발생하여  퍼포먼스가  떨어질  수  있으며  너무  길면  CDN의  캐시가  낭비될  수  있습니다.</p>
<ul>
<li><em>Alibaba Cloud CDN Console &gt; Domain Names &gt; Manage &gt; Cache &gt; Cache Expiration &gt; Cache Rule 선택</em>
<ul>
<li><strong>Type</strong> : Directory</li>
<li><strong>Object</strong> : / (Root에 Contents를  설정하지  않을  경우  변경이  필요합니다)</li>
<li><strong>Expire in</strong> : 보통 3600s나  그  이상으로  설정합니다. (요구사항에  맞게  설정  부탁드립니다.)</li>
</ul>
</li>
</ul>
<blockquote>
<p>참조 : <a href="https://www.alibabacloud.com/help/doc-detail/27136.htm?spm=a2c63.p38356.b99.64.47291e3cW9cqvE">https://www.alibabacloud.com/help/doc-detail/27136.htm?spm=a2c63.p38356.b99.64.47291e3cW9cqvE</a></p>
</blockquote>
<p>만약  테스트  중  아래와  같이  일정한  주기로  Jitter가  발생한다면  거의  TTL이  너무  짧은  이슈일  가능성이  큽니다. 이럴  경우  Cache Rule에서  Expire time을  늘려주면  이슈를  해결할  수  있습니다.<br>
<img src="https://user-images.githubusercontent.com/34003729/140641073-ad765ce5-ddd1-4f77-a236-b8c19bf56b32.png" alt="image"></p>
<p><em>Alibaba Cloud CDN Console &gt; Domain Names &gt; Manage &gt; Cache &gt; Cache Expiration &gt; Cache Rule</em> -  <strong>Expire in</strong> : 31536000s</p>
<h3 id="url-parameter-삭제--설정">5.2 URL Parameter 삭제  설정</h3>
<p>접속할  URL에 Parameter나 cache key를  제거/유지하여Hit rate와 back-to-origin에  대한  트래픽을  줄이기  위한  설정입니다.</p>
<ul>
<li><em>Alibaba Cloud CDN Console &gt; Domain Names &gt; Manage &gt; Optimization &gt; Parameter Filtering (Delete Specified Parameters) &gt; Modify 선택</em>  후  아래  붉은색  가이드에  따라  설정</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/140641135-35234eeb-aabf-498d-9a0c-e7e7db8feb1a.png" alt="image"></p>
<blockquote>
<p>참조 : <a href="https://www.alibabacloud.com/help/doc-detail/210953.htm?spm=a2c63.l28256.b99.119.eee14290LXFqJf">https://www.alibabacloud.com/help/doc-detail/210953.htm?spm=a2c63.l28256.b99.119.eee14290LXFqJf</a></p>
</blockquote>
<h3 id="intelligent-compression">5.3 Intelligent compression</h3>
<p>지능형  압축  기능을  활성화하면 Alibaba Cloud CDN이  자동으로  텍스트  파일을 Gzip 파일로  압축합니다. 이렇게  하면  파일  크기가  줄어들고  콘텐츠  전달이  빨라지며  대역폭  사용량이  줄어듭니다.</p>
<ul>
<li><strong>지원  포멧</strong>: text/xml, text/plain, text/css, application/javascript, application/x-javascript, application/rss+xml, text/javascript, image/tiff, image/svg+xml, application/json, application/xml.</li>
</ul>
<p><em>Alibaba Cloud DNS 콘솔  &gt; Domain Names &gt; Domain 선택  &gt; Optimization &gt; Intelligent Compression True 선택</em><br>
<img src="https://user-images.githubusercontent.com/34003729/140641175-48a267c2-b3fc-4be7-847e-d109d1f6ec3c.png" alt="image"></p>
<h3 id="brotli-compression">5.4 Brotli compression</h3>
<p><a href="https://en.wikipedia.org/wiki/Brotli">Brotli</a>는  오픈  소스  압축  알고리즘입니다. Brotli 압축  기능을  활성화하면  텍스트  파일이  사용자에게  반환되기  전에 Brotli 파일로  압축됩니다. 이렇게  하면  파일  크기가  줄어들고  콘텐츠  전달이  빨라지며  대역폭  사용량이  줄어듭니다.</p>
<p><em>Alibaba Cloud DNS 콘솔  &gt; Domain Names &gt; Domain 선택  &gt; Optimization &gt; Brotli Compression True 선택</em><br>
<img src="https://user-images.githubusercontent.com/34003729/140641230-621bb650-b24a-4b53-89c6-8cc348c2c3d5.png" alt="image"></p>
<p><strong>그  밖에  Image editing, SSD priority 설정, protocol stack 최적화  등의  튜닝  작업을  진행할  수  있습니다.</strong></p>
<h2 id="alibaba-cloud-cdn-테스트--결과">6. Alibaba Cloud CDN 테스트  결과</h2>
<p>약 <strong>450K, 2.7M 크기의 2개의  이미지</strong>를 <strong>Alibaba Cloud CDN(Green), A CSP CDN(Red), B vendor(Blue) 3개의  존</strong>에  올려두고 cacti 를  통해  CDN 퍼포먼스  테스트를  진행했습니다.</p>
<blockquote>
<p>튜닝은  기본  TTL 설정만  적용한  값입니다.<br>
<img src="https://user-images.githubusercontent.com/34003729/140641270-1e70a8b6-8837-4fb4-ab69-52f91c0078aa.png" alt="image"></p>
</blockquote>
<p><strong>B vendor의  CDN</strong>은  Connection_time과  Delivery_time에서  매우  느린  수치를  확인할  수  있으며  <strong>A CSP의  CDN과  Alibaba Cloud CDN</strong>은  두  metric에서  매우  빠른 수치를  기록했습니다. 또한  용량이  작은  이미지에서는  타  벤더의  CDN보다  안정적인  퍼포먼스를  제공했다는  것을  확인할  수  있습니다.</p>
<blockquote>
<p>Compression, Parameter 튜닝을  진행하면  더  빠른  퍼포먼스를  기록할  수  있으나  테스트의  공정성  관점에서  기본  Cache TTL 설정만  진행했습니다.</p>
</blockquote>
<h2 id="결론">7. 결론</h2>
<p><strong>B2C서비스를  하는  기업에서  고객에게  제공하는  서비스의  품질  향상을  고민하는  것은  그  어떤  과제보다도  최우선</strong>일  것입니다.</p>
<p>그  과제  중  웹  환경의  서비스를  제공하는  기업에서는  CDN은  필수  구성  요소입니다.</p>
<p>Alibaba Cloud에서는  <strong>뛰어난  퍼포먼스와  안정성</strong>을  보장하는  CDN 서비스를  세계  각  기업에게  제공하고  있습니다. 또한  타  벤더에  비해서  <strong>비용  효율적인  구조</strong>를  지니고  있어  고객에게  <strong>높은  수준의  가성비  제품</strong>으로서  만족의  피드백을  받고있습니다.</p>
<p>만약  사내에서  CDN을  검토하고  계시다면, Alibaba Cloud CDN의  높은  성능과  안정성을  경험해보시기  바랍니다.</p>

