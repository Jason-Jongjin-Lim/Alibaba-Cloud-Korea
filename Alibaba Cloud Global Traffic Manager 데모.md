---


---

<h1 id="알리바바-클라우드-global-traffic-managergtm-설정-데모">알리바바 클라우드 Global Traffic Manager(GTM) 설정 데모</h1>
<p>해당 글을 통해 알리바바 클라우드의 GTM 서비스에 대해 알아볼 수 있으며, 비즈니스 상황에 맞게 설정하는 방법을 알 수 있습니다.</p>
<h2 id="feature">1. Feature</h2>
<p>알리바바 클라우드의 GTM 서비스는 비즈니스의 요구사항에 따른 트래픽의 분기를 구현할 수 있는 서비스이다.</p>
<p>GTM을 사용하는 기업은 각 지역별 유저, 트래픽의 양, 애플리케이션의 특성에 따라 하나의 도메인으로 서비스 애플리케이션을 분기할 수 있다.</p>
<p>Alibaba Cloud의 GTM서비스는 크게 아래 4가지의 기능을 제공한다.</p>
<p><strong>- Address Pool</strong></p>
<p>애플리케이션 서비스의 주소(IP 주소 혹은 도메인)를 관리하기 위해 Address Pool 기능을 이용할 수 있다. 하나의 Address Pool은 IP 주소나 도메인의 그룹으로 이루어진다.</p>
<p><strong>- Access Policy</strong></p>
<p>Access Policy 는 총 2가지의 설정으로 관리할 수 있다.</p>
<p>a. 지역 위치에 따른 접근 관리 : 다양한 지역에서 접근하는 유저들을 가장 가까운 Access Point로 접근하도록 설정할 수 있다. 이 기능으로 전체적인 서비스의 네트워크 품질을 가속화할 수 있다.</p>
<p>b. Latency에 따른 접근 관리 : GTM은 유저와 애플리케이션간 구간별 Latency를 실시간으로 감시한다. 이 기능을 통해 유저는 실시간으로 각 Latency에 따른 애플리케이션간 부하 분산을 구현할 수 있다.</p>
<ul>
<li>Health Check</li>
</ul>
<p>Health Check를 이용하면 실시간으로 애플리케이션의 Availability를 모니터링할 수 있다. 이 기능은 ping, tcp, http(s) 프로토콜을 이용한다.</p>
<ul>
<li>FailOver</li>
</ul>
<p>만약 Health Check 기능에서 Primary address pool이 unavailable상태로 감지된다면 GTM은 자동으로 Secondary address pool로 스위치 오버를 작동한다. 이 기능이 작동되면 Secondary address pool에 해당하는 서버들은 유저의 DNS 요청에 리다이렉션을 보내게 된다.</p>
<h2 id="구성-절차">2. 구성 절차</h2>
<p>데모에서 구성할 절차는 아래와 같다.</p>
<p>접속용 인스턴스 생성 &gt; Authorize GTM &gt; GTM 인스턴스 생성 &gt; Basic Configuration &gt; DNS 설정 &gt; Address Pool 생성 &gt; 로케이션 기반 접근 제어 설정 &gt; Latency 기반 접근 제어 설정 &gt; Health Check 설정 &gt; 결과 테스트</p>
<h3 id="접속용-인스턴스-생성">2.1 접속용 인스턴스 생성</h3>
<p>본 시나리오에서 테스트할 인스턴스들을 생성한다.</p>
<p>지역 기반 접근 제어에서 서비스들을 구분하기 위해 각 4가지 종류(Skull, Bowow, Bear, Meow) 서비스를 만들었다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image001.png)&lt;![endif]&gt;</p>
<h3 id="authorize-gtm">2.2 Authorize GTM</h3>
<p>만약 GTM을 처음 사용한다면 본 서비스의 사용을 위해 Authorize 작업이 필요하다.</p>
<p>이 작업은 _Alibaba Cloud DNS 콘솔_의 GTM 메뉴를 선택하면 확인할 수 있다.</p>
<p>&lt;![if !vml]&gt;![1](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image002.png)&lt;![endif]&gt;</p>
<ul>
<li>만약 본 화면이 나오지 않는다면 이미 인증이 되어있는 계정일 것이다.</li>
</ul>
<h3 id="gtm-인스턴스-생성">2.3 GTM 인스턴스 생성</h3>
<p>GTM은 2가지 종류의 인스턴스 Edition을 제공한다. 두 가지 Edition의 차이점은 아래와 같다.</p>
<ul>
<li>Standard Edition</li>
</ul>
<p><strong>Health check:</strong> 최소 헬스체크 인터벌 시간 1분</p>
<p>Minimum Global TTL: 60초<br>
<strong>Access policy:</strong> 지역 기반 접근 제어 제공<br>
<strong>Access policy based on geographical locations:</strong> 중국 내의 주요 ISP 및 지역 / 대륙 기반 인터네셔널 지역 기준 분기 제공<br>
<strong>Edition limit:</strong> 인스턴스당 Address Pool 최대 10개, 인스턴스당 Access policy 최대 10개</p>
<ul>
<li>Ultimate Edition</li>
</ul>
<p><strong>Health check:</strong> 최소 헬스체크 인터벌 시간 15초</p>
<p>Minimum Global TTL: 1초<br>
<strong>Access policy:</strong> 지역 기반 접근 제어 / 레이턴시 기반 접근 제어 제공<br>
<strong>Access policy based on geographical locations:</strong> 전세계 대부분 ISP 및 지역 기준 분기 제공<br>
<strong>Edition limit:</strong> 인스턴스당 Address Pool 최대 20개, 인스턴스당 Access policy 최대 20개</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image003.png)&lt;![endif]&gt;</p>
<ul>
<li>비즈니스 요구사항에 맞게 Edition 및 추가 Task 개수를 선택</li>
</ul>
<h3 id="basic-configuration">2.4 Basic Configuration</h3>
<p>구매한 GTM 인스턴스를 사용하기 위한 가장 기본적인 설정이다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Instance 선택 &gt; Advanced Settings</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image004.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image005.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image006.png)&lt;![endif]&gt;</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Instance 선택 &gt; Advanced Settings &gt; Basic Configuration &gt; Edit</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image007.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image009.png)&lt;![endif]&gt;</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image010.png)&lt;![endif]&gt;</p>
<ul>
<li>
<p>Instance Name : 사용할 Instance Name 입력</p>
</li>
<li>
<p>Domain Name : 실제로 서비스에서 사용할 Domain Name 입력</p>
</li>
<li>
<p>CNAME : CNAME 타입 선택, Assigned는 알리바바에서 제공하는 CNAME이며 Custom은 자체적으로 사용할 CNAME을 입력</p>
</li>
<li>
<p>Global TTL : TTL 인터벌 시간 설정</p>
</li>
</ul>
<p>설정이 완료된 후 Confirm을 누르면 우리가 설정한 값들이 Basic Configuration에 나타남을 확인한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image011.png)&lt;![endif]&gt;</p>
<p>여기서 나오는 CNAME을 노트한다</p>
<h3 id="dns설정">2.5 DNS설정</h3>
<p>노트한 CNAME을 위에서 설정한 실사용 도메인에 추가를 한다.</p>
<p><em>Alibaba DNS 콘솔 &gt; Manage DNS &gt; DNS Setting &gt; 사용할 DNS 선택 &gt; Add Record</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image012.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image013.png)&lt;![endif]&gt;</p>
<p>아래 옵션을 참조하여 CNAME을 설정한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image014.png)&lt;![endif]&gt;</p>
<ul>
<li>
<p>Type : CNAME 선택</p>
</li>
<li>
<p>Host : 서비스에서 사용할 Sub-domain 입력</p>
</li>
<li>
<p>Value : 위에서 노트한 CNAME 입력</p>
</li>
</ul>
<h3 id="address-pool-설정">2.6 Address Pool 설정</h3>
<p>애플리케이션의 서버 혹은 서버 그룹을 Address Pool로 설정한다. 이 Address Pool은 이후 접근 제어에서 사용한다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager / Address Pool Configurations &gt; Create Address Pool</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image015.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image016.png)&lt;![endif]&gt;</p>
<p>아래 옵션을 참고하여 Address Pool을 설정한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image017.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image018.png)&lt;![endif]&gt;</p>
<ul>
<li>
<p>Address Pool Name : 사용할 Address Pool Name을 입력</p>
</li>
<li>
<p>Address Pool Type : 서버 접속에 사용할 프로토콜 선택 (IPv4 / IPv6 / Domain)</p>
</li>
<li>
<p>Addresses : Address Pool로 정의한 서버의 접속 정보 입력</p>
</li>
</ul>
<p>데모에서는 총 4개의 Address Pool을 사용하여 분기를 설정할 것이다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image019.png)&lt;![endif]&gt;</p>
<h3 id="로케이션-기반-접근-제어-설정">2.7 로케이션 기반 접근 제어 설정</h3>
<p>설정된 Address Pool을 GTM 이용하여 로케이션 기반 접근 제어를 설정할 수 있다. 본 데모에서는 각 한국, 일본, 홍콩, 싱가포르에서 접속하는 유저에 따라 서로 다른 서비스를 연결하도록 설정할 것이다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Basic Configuration &gt; Access Policy Based on Geo Location &gt; Set Access Policy</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image020.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image021.png)&lt;![endif]&gt;</p>
<p>접속된 화면에서 _Add Access Policy_를 선택한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image022.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image023.png)&lt;![endif]&gt;</p>
<p>아래 옵션을 참고하여 지역 기반 접근 제어를 설정한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image024.png)&lt;![endif]&gt;</p>
<ul>
<li>
<p>Policy Name : 사용한 정책의 이름 입력</p>
</li>
<li>
<p>DNS Request Sources : 접근 정책을 설정할 지역 기반 ISP/대륙/위치 등 설정</p>
</li>
<li>
<p>Primary Address Pool Set : 해당 접근 정책으로 이용할 서비스의 Address Pool 선택 (복수 가능)</p>
</li>
<li>
<p>Secondary Address Pool Set : Failover를 위한 백업 서버의 Address Pool 선택</p>
</li>
</ul>
<p>입력 이후 설정된 접근 제어를 확인한다. 본 데모에서는 싱가포르-Skull / 홍콩-Bear / 일본-Bowow / 한국-Meow로 설정하였다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image025.png)&lt;![endif]&gt;</p>
<h3 id="latency-기반-접근-제어-설정">2.8 Latency 기반 접근 제어 설정</h3>
<p>레이턴시 기반으로 부하 분산을 수행할 접근 제어 정책을 설정한다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Basic Configuration &gt; Access Policy Based on Latency &gt; Set Access Policy</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image026.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image027.png)&lt;![endif]&gt;</p>
<p>접속된 화면에서 _Add Access Policy_를 선택한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image028.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image029.png)&lt;![endif]&gt;</p>
<p>아래 옵션을 참고하여 Latency 기반 접근 제어 정책을 설정한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image030.png)&lt;![endif]&gt;</p>
<ul>
<li>
<p>Policy Name : 사용할 정책의 이름 설정</p>
</li>
<li>
<p>Primary Address Pool : 서비스에 사용할 Address Pool 설정</p>
</li>
<li>
<p>Min Number : 최소로 사용할 Address Pool</p>
</li>
<li>
<p>Max Number : 최대로 사용할 Address Pool</p>
</li>
<li>
<p>Latency Resolution Scheduling Optimization : 해당 옵션을 설정하며 Max Number의 값 내에서 최적의 주소를 반환</p>
</li>
</ul>
<p>입력 후 설정된 값을 확인한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image032.png)&lt;![endif]&gt;</p>
<h3 id="health-check-설정">2.9 Health Check 설정</h3>
<p>각 Address Pool에 설정된 서버들의 상태를 실시간으로 체크하기 위해 Health Check를 설정한다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Address Pool Configurations &gt; 설정할 Address Pool Name 선택 &gt; Health Check &gt; Add</em></p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image033.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image034.png)&lt;![endif]&gt;</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image033.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image035.png)&lt;![endif]&gt;</p>
<p>아래 옵션을 참조하여 Health Check를 설정한다.</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image036.png)&lt;![endif]&gt;</p>
<ul>
<li>
<p>Health Check Protocol : Health Check에 사용할 Protocol 선택 (Ping / TCP / HTTP / HTTPS)</p>
</li>
<li>
<p>Check Interval : Health Check를 수행할 인터벌 타임 설정</p>
</li>
<li>
<p>서브 옵션은 각 프로토콜 설정에 맞게 입력</p>
</li>
<li>
<p>Time Out : Health Check를 수행한 후 리다이렉션이 없을 시의 타임아웃 설정</p>
</li>
<li>
<p>Continuous Failed Attempts : 실패시 재시도 횟수</p>
</li>
<li>
<p>Monitoring Node : 상태 체크를 수행할 노드의 지역 혹은 ISP 입력</p>
</li>
</ul>
<h3 id="결과-확인">2.10 결과 확인</h3>
<p>설정된 접근 제어 정책 중, 지역 기반의 정책을 테스트하기 위하여 각 싱가포르, 홍콩, 한국, 일본 지역에 인스턴스를 배포했다. 배포된 각 지역의 인스턴스에서 설정된 도메인으로 curl 명령어를 수행하여 정상적으로 기능이 동작하는지 확인한다.</p>
<p>한국 리전에서 접속 – Meow 확인</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image037.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image038.png)&lt;![endif]&gt;</p>
<p>싱가포르 리전에서 접속 – Skull 확인</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image037.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image039.png)&lt;![endif]&gt;</p>
<p>일본 리전에서 접속 – Bowow 확인</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image037.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image040.png)&lt;![endif]&gt;</p>
<p>홍콩 리전에서 접속 – Bear 확인</p>
<p>&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image037.png)&lt;![endif]&gt;&lt;![if !vml]&gt;![](file:////Users/jongjin/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image041.png)&lt;![endif]&gt;</p>
<h2 id="사용-가능한-시나리오">3. 사용 가능한 시나리오</h2>
<h3 id="disaster-recovery">3.1 Disaster recovery</h3>
<p>애플리케이션 서비스에 1.1.1.1 및 2.2.2.2를 포함하여 두 개의 IP 주소가 있다고 가정한다. 정상적인 조건에서 사용자는 IP 주소 1.1.1.1을 사용하여 서비스에 접근한다. IP 주소 1.1.1.1에 접속 실패하면 접근 트래픽을 IP 주소 2.2.2.2로 전환해야 한다.</p>
<p>GTM을 사용하여 Pool A와 Pool B를 생성한다. 두 개의 주소 풀에 IP 주소 1.1.1.1 및 2.2.2.2를 추가한 다음 상태 확인 기능을 구성한다. 액세스 정책을 구성할 때 Primary를 Pool A로 설정하고 Secondary를 Pool B로 설정합니다. 이렇게 재해 복구 시나리오를 수행할 수 있다.</p>
<h3 id="active-redundancy">3.2 Active redundancy</h3>
<p>애플리케이션 서비스에 1.1.1.1, 2.2.2.2 및 3.3.3.3을 포함하여 3개의 IP 주소가 있다고 가정한다. 사용자는 세 개의 IP 주소를 동시에 접속에 사용할 수 있다. 정상적인 조건에서 DNS는 동시에 세 개의 IP 주소를 반환한다. 세 개의 IP 주소 중 하나가 접속에 실패하면 해당 주소는 DNS 주소 목록에서 일시적으로 제거되고 사용자에게 반환되지 않는다. IP 주소를 사용할 수 있게 되면 주소 목록에 추가된다.</p>
<p>GTM을 사용하여 IP 주소 1.1.1.1, 2.2.2.2 및 3.3.3.3을 포함하는 Address Pool A를 만들 수 있다. Primary Address Pool Set을 Pool A로 설정한 다음 Health Check를 활성화하고 구성한다. 이러한 방식으로 여러 IP 주소에 대한 Redundancy를 설정할 수 있다.</p>
<h3 id="accelerated-access">3.3 Accelerated access</h3>
<p>대기업은 국가 또는 전 세계의 지역에서 네트워크 서비스를 제공해야한다. 네트워크 액세스는 지역마다 다른 네트워크 조건의 영향을 받는다. 따라서 기업은 해당 지역의 핵심 위치에 액세스 포인트를 설정해야한다. 각각 다른 지역의 사용자는 가장 가까운 액세스 포인트에서 서비스에 액세스할 수 있다.</p>
<p>GTM은 다음 두 가지 액세스 정책을 제공한다.</p>
<p>• 위치 기반 액세스 정책: GTM은 지정된 Address Pool Set 의 주소를 각각 다른 지역의 사용자에게 반환한다. 사용자는 가장 가까운 지점을 사용하여 네트워크 액세스를 가속화할 수 있다.</p>
<p>• 레이턴시 기반 액세스 정책: GTM은 사용자 위치와 애플리케이션이 배포된 지역 간의 액세스 대기 시간을 감지한다. 그 다음 GTM은 대기 시간이 가장 짧은 애플리케이션 서버 클러스터로 사용자 요청을 라우팅한다. 이러한 방식으로 네트워크 액세스를 가속화 할 수 있다.</p>
<p>##4. 결론</p>
<p>지금까지 GTM의 기능과 설정 방법, 그리고 사용할 수 있는 비즈니스 시나리오에 대해 알아보았다. 알리바바 클라우드의 GTM을 이용한다면, 비교적 저렴한 비용으로 서비스를 이용하여 비즈니스 서비스의 HA와 퍼포먼스를 강화할 수 있을 것이다.</p>
<p>만약 해당 솔루션에 대해 추가적으로 궁금한 점이 있거나 테스트 가이드를 원하신다면 아래 주소로 메일을 보내주시기 바랍니다.</p>
<p><a href="mailto:cloud-korea@alibaba-inc.com">cloud-korea@alibaba-inc.com</a></p>

