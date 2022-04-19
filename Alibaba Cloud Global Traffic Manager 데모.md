---


---

<h1 id="알리바바-클라우드-global-traffic-managergtm-설정-데모">알리바바 클라우드 Global Traffic Manager(GTM) 설정 데모</h1>
<h2 id="feature">1. Feature</h2>
<p>알리바바 클라우드의 GTM 서비스는 비즈니스의 요구사항에 따른 트래픽의 분기를 구현할 수 있는 서비스이다.</p>
<p>GTM을 사용하는 기업은 각 지역별 유저, 트래픽의 양, 애플리케이션의 특성에 따라 하나의 도메인으로 서비스 애플리케이션을 분기할 수 있다.</p>
<p>Alibaba Cloud의 GTM서비스는 크게 아래 4가지의 기능을 제공한다.</p>
<p><strong>- Address Pool</strong></p>
<p>애플리케이션 서비스의 주소(IP 주소 혹은 도메인)를 관리하기 위해 Address Pool 기능을 이용할 수 있다. 하나의 Address Pool은 IP 주소나 도메인의 그룹으로 이루어진다.</p>
<p><strong>- Access Policy</strong></p>
<p>Access Policy 는 총 2가지의 설정으로 관리할 수 있다.</p>
<p><em>a. 지역 위치에 따른 접근 관리</em> : 다양한 지역에서 접근하는 유저들을 가장 가까운 Access Point로 접근하도록 설정할 수 있다. 이 기능으로 전체적인 서비스의 네트워크 품질을 가속화할 수 있다.</p>
<p><em>b. Latency에 따른 접근 관리</em> : GTM은 유저와 애플리케이션간 구간별 Latency를 실시간으로 감시한다. 이 기능을 통해 유저는 실시간으로 각 Latency에 따른 애플리케이션간 부하 분산을 구현할 수 있다.</p>
<p><strong>- Health Check</strong></p>
<p>Health Check를 이용하면 실시간으로 애플리케이션의 Availability를 모니터링할 수 있다. 이 기능은 ping, tcp, http(s) 프로토콜을 이용한다.</p>
<p><strong>- FailOver</strong></p>
<p>만약 Health Check 기능에서 Primary address pool이 unavailable상태로 감지된다면 GTM은 자동으로 Secondary address pool로 스위치 오버를 작동한다. 이 기능이 작동되면 Secondary address pool에 해당하는 서버들은 유저의 DNS 요청에 리다이렉션을 보내게 된다.</p>
<h2 id="구성-절차">2. 구성 절차</h2>
<p>데모에서 구성할 절차는 아래와 같다.</p>
<p><strong>접속용 인스턴스 생성 &gt; Authorize GTM &gt; GTM 인스턴스 생성 &gt; Basic Configuration &gt; DNS 설정 &gt; Address Pool 생성 &gt; 로케이션 기반 접근 제어 설정 &gt; Latency 기반 접근 제어 설정 &gt; Health Check 설정 &gt; 결과 테스트</strong></p>
<h3 id="접속용-인스턴스-생성">2.1 접속용 인스턴스 생성</h3>
<p>본 시나리오에서 테스트할 인스턴스들을 생성한다.</p>
<p>지역 기반 접근 제어에서 접속되는 서비스들을 구분하기 위해 각 4가지 종류(Skull, Bowow, Bear, Meow)의 서비스를 만들었다.</p>
<img width="548" alt="image" src="https://user-images.githubusercontent.com/34003729/163909297-9b68f166-8908-4e22-8536-49eb696f8bb7.png">
<h3 id="authorize-gtm">2.2 Authorize GTM</h3>
<p>만약 GTM을 처음 사용한다면 본 서비스의 사용을 위해 Authorize 작업이 필요하다.</p>
<p>이 작업은 <em>Alibaba Cloud DNS 콘솔</em>의 <em>Global Traffic Manager 메뉴</em>를 선택하면 확인할 수 있다.</p>
<img width="651" alt="image" src="https://user-images.githubusercontent.com/34003729/163909381-196a549d-0c69-4992-ad7e-3c8520bdee3c.png">
<blockquote>
<p>만약 본 화면이 나오지 않는다면 이미 인증이 되어있는 계정일 것이다.</p>
</blockquote>
<h3 id="gtm-인스턴스-생성">2.3 GTM 인스턴스 생성</h3>
<p>GTM은 2가지 종류의 인스턴스 Edition을 제공한다. 두 가지 Edition의 차이점은 아래와 같다.</p>
<p><strong>Standard Edition</strong></p>
<ul>
<li>Health check: 최소 헬스체크 인터벌 시간 1분</li>
<li>Minimum Global TTL: 60초</li>
<li>Access policy: 지역 기반 접근 제어 제공</li>
<li>Access policy based on geographical locations: 중국 내의 주요 ISP 및 지역 / 대륙 기반 인터네셔널 지역 기준 분기 제공</li>
<li>Edition limit: 인스턴스당 Address Pool 최대 10개, 인스턴스당 Access policy 최대 10개</li>
</ul>
<p><strong>Ultimate Edition</strong></p>
<ul>
<li>Health check: 최소 헬스체크 인터벌 시간 15초</li>
<li>Minimum Global TTL: 1초</li>
<li>Access policy: 지역 기반 접근 제어 / 레이턴시 기반 접근 제어 제공</li>
<li>Access policy based on geographical locations: 전세계 대부분 ISP 및 지역 기준 분기 제공</li>
<li>Edition limit: 인스턴스당 Address Pool 최대 20개, 인스턴스당 Access policy 최대 20개</li>
</ul>
<img width="657" alt="image" src="https://user-images.githubusercontent.com/34003729/163909485-4dc0e261-e156-49ed-9434-77d8df9fcb23.png">
<ul>
<li>비즈니스 요구사항에 맞게 Edition 및 추가 Task 개수를 선택</li>
</ul>
<h3 id="basic-configuration">2.4 Basic Configuration</h3>
<p>구매한 GTM 인스턴스를 사용하기 위한 가장 기본적인 설정이다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Instance 선택 &gt; Advanced Settings</em></p>
<img width="708" alt="image" src="https://user-images.githubusercontent.com/34003729/163909569-4ef48529-0f1e-4c11-a56b-0e8e4d5d50c8.png">
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Instance 선택 &gt; Advanced Settings &gt; Basic Configuration &gt; Edit</em></p>
<img width="566" alt="image" src="https://user-images.githubusercontent.com/34003729/163909643-9a19f974-6013-4d50-ab90-b02976a72e88.png">
<p><img src="https://user-images.githubusercontent.com/34003729/163909714-bc9c945e-511e-455c-831a-fa8b653afa29.png" alt="image"></p>
<ul>
<li><strong>Instance Name</strong> : 사용할 Instance Name 입력</li>
<li><strong>Domain Name</strong> : 실제로 서비스에서 사용할 Domain Name 입력</li>
<li><strong>CNAME</strong> : CNAME 타입 선택, Assigned는 알리바바에서 제공하는 CNAME이며 Custom은 자체적으로 사용할 CNAME을 입력</li>
<li><strong>Global TTL</strong> : TTL 인터벌 시간 설정</li>
</ul>
<p>설정이 완료된 후 Confirm을 누르면 우리가 설정한 값들이 Basic Configuration에 나타남을 확인한다.</p>
<img width="597" alt="image" src="https://user-images.githubusercontent.com/34003729/163909960-04c8d7c4-522f-40ca-a680-b3dd71d5a3a2.png">
<p>여기서 나오는 CNAME을 노트한다</p>
<h3 id="dns설정">2.5 DNS설정</h3>
<p>노트한 CNAME을 위에서 설정한 실사용 도메인에 추가를 한다.</p>
<p><em>Alibaba DNS 콘솔 &gt; Manage DNS &gt; DNS Setting &gt; 사용할 DNS 선택 &gt; Add Record</em></p>
<img width="609" alt="image" src="https://user-images.githubusercontent.com/34003729/163910042-8c3639a7-0e1d-4868-a8f5-4dc1e5e83d4b.png">
<p>아래 옵션을 참조하여 CNAME을 설정한다.</p>
<img width="555" alt="image" src="https://user-images.githubusercontent.com/34003729/163910103-046821b7-0c7b-452b-a9ca-c2514b35e0a5.png">
<ul>
<li><strong>Type</strong> : CNAME 선택</li>
<li><strong>Host</strong> : 서비스에서 사용할 Sub-domain 입력</li>
<li><strong>Value</strong> : 위에서 노트한 CNAME 입력</li>
</ul>
<h3 id="address-pool-설정">2.6 Address Pool 설정</h3>
<p>애플리케이션의 서버 혹은 서버 그룹을 Address Pool로 설정한다. 이 Address Pool은 이후 접근 제어에서 사용한다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager / Address Pool Configurations &gt; Create Address Pool</em></p>
<img width="567" alt="image" src="https://user-images.githubusercontent.com/34003729/163910187-3546e5f6-9010-4aab-a90d-6eb21c93678f.png">
<p>아래 옵션을 참고하여 Address Pool을 설정한다.</p>
<img width="488" alt="image" src="https://user-images.githubusercontent.com/34003729/163910265-4476b59f-c791-4b69-b4aa-469310cf6df9.png">
<ul>
<li><strong>Address Pool Name</strong> : 사용할 Address Pool Name을 입력</li>
<li><strong>Address Pool Type</strong> : 서버 접속에 사용할 프로토콜 선택 (IPv4 / IPv6 / Domain)</li>
<li><strong>Addresses</strong> : Address Pool로 정의한 서버의 접속 정보 입력</li>
</ul>
<p>데모에서는 총 4개의 Address Pool을 사용하여 분기를 설정할 것이다.</p>
<img width="614" alt="image" src="https://user-images.githubusercontent.com/34003729/163910324-9998a4ff-2d8c-4615-85d2-b7b14304c2e6.png">
<h3 id="로케이션-기반-접근-제어-설정">2.7 로케이션 기반 접근 제어 설정</h3>
<p>설정된 Address Pool을 GTM 이용하여 로케이션 기반 접근 제어를 설정할 수 있다. 본 데모에서는 각 한국, 일본, 홍콩, 싱가포르에서 접속하는 유저에 따라 서로 다른 서비스를 연결하도록 설정할 것이다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Basic Configuration &gt; Access Policy Based on Geo Location &gt; Set Access Policy</em></p>
<img width="619" alt="image" src="https://user-images.githubusercontent.com/34003729/163910392-140e1afd-40f1-4aa8-b934-84aff5b8bd60.png">
<p>접속된 화면에서 <em>Add Access Policy</em>를 선택한다.</p>
<img width="490" alt="image" src="https://user-images.githubusercontent.com/34003729/163910585-cdedf069-b6ee-44e8-90b6-fd9d0cf179cc.png">
<p>아래 옵션을 참고하여 지역 기반 접근 제어를 설정한다.</p>
<img width="400" alt="image" src="https://user-images.githubusercontent.com/34003729/163910473-dea3537b-ffee-4f80-9854-d4714b5e7e66.png">
<ul>
<li><strong>Policy Name</strong> : 사용한 정책의 이름 입력</li>
<li><strong>DNS Request Sources</strong> : 접근 정책을 설정할 지역 기반 ISP/대륙/위치 등 설정</li>
<li><strong>Primary Address Pool Set</strong> : 해당 접근 정책으로 이용할 서비스의 Address Pool 선택 (복수 가능)</li>
<li><strong>Secondary Address Pool Set</strong> : Failover를 위한 백업 서버의 Address Pool 선택</li>
</ul>
<p>입력 이후 설정된 접근 제어를 확인한다. 본 데모에서는 <strong>싱가포르-Skull / 홍콩-Bear / 일본-Bowow / 한국-Meow</strong>로 설정하였다.</p>
<img width="505" alt="image" src="https://user-images.githubusercontent.com/34003729/163910687-ec8ec9a3-d58a-4252-8121-85acfc197ec6.png">
<h3 id="latency-기반-접근-제어-설정">2.8 Latency 기반 접근 제어 설정</h3>
<p>레이턴시 기반으로 부하 분산을 수행할 접근 제어 정책을 설정한다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Basic Configuration &gt; Access Policy Based on Latency &gt; Set Access Policy</em></p>
<img width="519" alt="image" src="https://user-images.githubusercontent.com/34003729/163910782-0abf9642-0bae-4d70-aa01-4648364238ed.png">
<p>접속된 화면에서 <em>Add Access Policy</em>를 선택한다.</p>
<img width="466" alt="image" src="https://user-images.githubusercontent.com/34003729/163910857-19a831f1-9c44-460e-a562-f950d541c5ba.png">
<p>아래 옵션을 참고하여 Latency 기반 접근 제어 정책을 설정한다.</p>
<img width="381" alt="image" src="https://user-images.githubusercontent.com/34003729/163910910-469b3069-6bee-4c03-8509-a3b8a3050998.png">
<ul>
<li><strong>Policy Name</strong> : 사용할 정책의 이름 설정</li>
<li><strong>Primary Address Pool</strong> : 서비스에 사용할 Address Pool 설정</li>
<li><strong>Min Number</strong> : 최소로 사용할 Address Pool</li>
<li><strong>Max Number</strong> : 최대로 사용할 Address Pool</li>
<li><strong>Latency Resolution Scheduling Optimization</strong> : 해당 옵션을 설정하며 Max Number의 값 내에서 최적의 주소를 반환</li>
</ul>
<p>입력 후 설정된 값을 확인한다.</p>
<img width="463" alt="image" src="https://user-images.githubusercontent.com/34003729/163915241-a4a7589a-93f2-4870-9d7d-d137b7336356.png">
<h3 id="health-check-설정">2.9 Health Check 설정</h3>
<p>각 Address Pool에 설정된 서버들의 상태를 실시간으로 체크하기 위해 Health Check를 설정한다.</p>
<p><em>Alibaba Cloud DNS 콘솔 &gt; Global Traffic Manager &gt; Address Pool Configurations &gt; 설정할 Address Pool Name 선택 &gt; Health Check &gt; Add</em></p>
<img width="521" alt="image" src="https://user-images.githubusercontent.com/34003729/163915306-362cb314-7756-4bd9-8f84-5471ec4e186f.png">
<img width="515" alt="image" src="https://user-images.githubusercontent.com/34003729/163915394-5c172803-13e9-4047-a041-5b103247bab3.png">
<p>아래 옵션을 참조하여 Health Check를 설정한다.</p>
<img width="396" alt="image" src="https://user-images.githubusercontent.com/34003729/163915474-aaacf28f-acf2-476b-b1c2-67f10ed6a669.png">
<ul>
<li><strong>Health Check Protocol</strong> : Health Check에 사용할 Protocol 선택 (Ping / TCP / HTTP / HTTPS)</li>
<li><strong>Check Interval</strong> : Health Check를 수행할 인터벌 타임 설정</li>
<li><strong>서브 옵션은 각 프로토콜 설정에 맞게 입력</strong></li>
<li><strong>Time Out</strong> : Health Check를 수행한 후 리다이렉션이 없을 시의 타임아웃 설정</li>
<li><strong>Continuous Failed Attempts</strong> : 실패시 재시도 횟수</li>
<li><strong>Monitoring Node</strong> : 상태 체크를 수행할 노드의 지역 혹은 ISP 입력</li>
</ul>
<h3 id="결과-확인">2.10 결과 확인</h3>
<p>설정된 접근 제어 정책 중, 지역 기반의 정책을 테스트하기 위하여 각 싱가포르, 홍콩, 한국, 일본 지역에 인스턴스를 배포했다. 배포된 각 지역의 인스턴스에서 설정된 도메인으로 curl 명령어를 수행하여 정상적으로 기능이 동작하는지 확인한다.</p>
<p>한국 리전에서 접속 – <strong>Meow 확인</strong></p>
<img width="400" alt="image" src="https://user-images.githubusercontent.com/34003729/163915537-7fadd8f6-13ad-48ff-9262-da6397cde130.png">
<p>싱가포르 리전에서 접속 – <strong>Skull 확인</strong></p>
<img width="397" alt="image" src="https://user-images.githubusercontent.com/34003729/163915613-a3378e78-8bc5-4824-bd9d-abfea26b4680.png">
<p>일본 리전에서 접속 – <strong>Bowow 확인</strong></p>
<img width="390" alt="image" src="https://user-images.githubusercontent.com/34003729/163915684-6a85cc3e-db8d-4b2b-b729-6d436b9d0545.png">
<p>홍콩 리전에서 접속 – <strong>Bear 확인</strong></p>
<img width="396" alt="image" src="https://user-images.githubusercontent.com/34003729/163915757-cd906d71-f4ec-447e-bfe8-cdc0e504f961.png">
<h2 id="사용-가능한-시나리오">3. 사용 가능한 시나리오</h2>
<h3 id="disaster-recovery">3.1 Disaster recovery</h3>
<p>애플리케이션 서비스에 1.1.1.1 및 2.2.2.2를 포함하여 두 개의 IP 주소가 있다고 가정한다. 정상적인 조건에서 사용자는 IP 주소 1.1.1.1을 사용하여 서비스에 접근한다. IP 주소 1.1.1.1에 접속 실패하면 접근 트래픽을 IP 주소 2.2.2.2로 전환해야 한다.</p>
<p>GTM을 사용하여 Pool A와 Pool B를 생성한다. 두 개의 주소 풀에 IP 주소 1.1.1.1 및 2.2.2.2를 추가한 다음 상태 확인 기능을 구성한다. 액세스 정책을 구성할 때 Primary를 Pool A로 설정하고 Secondary를 Pool B로 설정한다. 이렇게 재해 복구 시나리오를 수행할 수 있다.</p>
<h3 id="active-redundancy">3.2 Active redundancy</h3>
<p>애플리케이션 서비스에 1.1.1.1, 2.2.2.2 및 3.3.3.3을 포함하여 3개의 IP 주소가 있다고 가정한다. 사용자는 세 개의 IP 주소를 동시에 접속에 사용할 수 있다. 정상적인 조건에서 DNS는 동시에 세 개의 IP 주소를 반환한다. 세 개의 IP 주소 중 하나가 접속에 실패하면 해당 주소는 DNS 주소 목록에서 일시적으로 제거되고 사용자에게 반환되지 않는다. IP 주소를 사용할 수 있게 되면 주소 목록에 추가된다.</p>
<p>GTM을 사용하여 IP 주소 1.1.1.1, 2.2.2.2 및 3.3.3.3을 포함하는 Address Pool A를 만들 수 있다. Primary Address Pool Set을 Pool A로 설정한 다음 Health Check를 활성화하고 구성한다. 이러한 방식으로 여러 IP 주소에 대한 Redundancy를 설정할 수 있다.</p>
<h3 id="accelerated-access">3.3 Accelerated access</h3>
<p>대기업은 국가 또는 전 세계의 지역에서 네트워크 서비스를 제공해야한다. 네트워크 액세스는 지역마다 다른 네트워크 조건의 영향을 받는다. 따라서 기업은 해당 지역의 핵심 위치에 액세스 포인트를 설정해야한다. 각각 다른 지역의 사용자는 가장 가까운 액세스 포인트에서 서비스에 액세스할 수 있다.</p>
<p>GTM은 다음 두 가지 액세스 정책을 제공한다.</p>
<p>• <strong>위치 기반 액세스 정책</strong>: GTM은 지정된 Address Pool Set 의 주소를 각각 다른 지역의 사용자에게 반환한다. 사용자는 가장 가까운 지점을 사용하여 네트워크 액세스를 가속화할 수 있다.</p>
<p>• <strong>레이턴시 기반 액세스 정책</strong>: GTM은 사용자 위치와 애플리케이션이 배포된 지역 간의 액세스 대기 시간을 감지한다. 그 다음 GTM은 대기 시간이 가장 짧은 애플리케이션 서버 클러스터로 사용자 요청을 라우팅한다. 이러한 방식으로 네트워크 액세스를 가속화 할 수 있다.</p>
<h2 id="결론">4. 결론</h2>
<p>지금까지 GTM의 기능과 설정 방법, 그리고 사용할 수 있는 비즈니스 시나리오에 대해 알아보았다. 알리바바 클라우드의 GTM을 이용한다면, 비교적 저렴한 비용으로 서비스를 이용하여 비즈니스 서비스의 HA와 퍼포먼스를 강화할 수 있을 것이다.</p>
<p><strong>만약 해당 솔루션에 대해 추가적으로 궁금한 점이 있거나 테스트 가이드를 원하신다면 아래 주소로 메일을 보내주시기 바랍니다.<br>
<a href="mailto:cloud-korea@alibaba-inc.com">cloud-korea@alibaba-inc.com</a></strong></p>

