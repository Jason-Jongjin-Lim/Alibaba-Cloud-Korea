---


---

<h1 id="alibaba-cloud-massage-queue-for-rocketmq-rabbitmq-mqtt-kafka-비교-정리">Alibaba Cloud Massage Queue for RocketMQ, RabbitMQ, MQTT, Kafka 비교 정리</h1>
<h2 id="introduction">Introduction</h2>
<h3 id="message-queue란">1. Message Queue란?</h3>
<p>Message Oriented Middleware(MOM)는 비동기 메시지를 사용하는 다른 애플리케이션 간의 데이터 송수신을 말합니다. 또한 이 아키텍쳐를 사용하는 Message Queue(MQ)는 프로그래밍에서 프로세스 또는 프로그램 인스턴스가 데이터를 서로 교환할 때 사용하는 방법입니다.</p>
<p>MOM은 MQ을 포괄하고 있고 MQ에는 Kafka, RabbitMQ, RocketMQ, ActiveMQ 등이 있습니다.</p>
<h3 id="message-queue의-장점-및-단점">2. Message Queue의 장점 및 단점</h3>
<p>MQ의 장점과 단점은 다음과 같습니다.</p>
<p><strong>MQ의 장점</strong></p>
<ul>
<li>비동기(Asynchronous) : Queue에 넣기 때문에 나중에 처리 할 수 있다.</li>
<li>비동조(Decoupling) : 애플리케이션과 분리 할 수 있다.</li>
<li>탄력성(Resilience) : 일부가 실패 시 전체에 영향을 받지 않는다.</li>
<li>과잉(Redundancy) : 실패 할 경우 재실행 가능</li>
<li>보증(Guarantees) : 작업이 처리된 걸 확인 할 수 있다.</li>
<li>확장성(Scalable) : 다수의 프로세스들이 큐에 메시지를 보낼 수 있다.</li>
</ul>
<p><strong>MQ의 단점</strong></p>
<ul>
<li>시스템의 가용성 감소 : 다이렉트로 통신하던 각 시스템에서 MQ가 개입함으로써 MQ의 이슈가 발생되면 전체 시스템에 문제가 생길 수 있다.</li>
<li>시스템의 복잡성 증가 : 아키텍쳐에 MQ가 추가되면 메시지 딜리버리 순서, 메시지 손실에 대한 복구, 재시도 등 MQ에 대한 추가 프로세스가 고려되어야 한다.</li>
<li>데이터 일관성 문제 : 1:1 메시지 통신 구조가 아닌 N:N 통신 구조에서는 각 서비스간 데이터 보상 문제와 동기화 문제가 필히 발생한다. 이 데이터 일관성을 보장하기 위해서는 추가적인 기술 솔루션과 아키텍처를 구성해야 한다.</li>
</ul>
<h3 id="mq의-대표적인-통신-방식-jms-vs-amqp">3. MQ의 대표적인 통신 방식, JMS vs AMQP</h3>
<ul>
<li>AMQP는 ISO 애플리케이션 계층의 MOM 표준이다.</li>
<li>JMS는 MOM를 자바 에서 지원하는 표준 API 이다.</li>
<li>JMS는 다른 자바 애플리케이션들끼리 통신이 가능하지만 AMQP나 SMTP같은 다른 MOM의 통신은 불가능하다.</li>
<li>같은 MQ 시스템의 JMS라이브러리를 사용한 자바 애플리케이션들 끼리 통신이 가능하다 그러나 다른 MQ 시스템을 사용하는 자바 애플리케이션의 JMS와는 통신 할 수 없다.</li>
<li>AMQP는 프로토콜만 맞다면 다른 AMQP를 사용한 애플리케이션 끼리 통신이 가능하다 .</li>
<li>JMS 라이브러리에선 AMQP를 지원하지 않는다.</li>
</ul>
<h2 id="alibaba-cloud-mq-서비스-설명">Alibaba Cloud MQ 서비스 설명</h2>
<h3 id="message-queue-for-apache-rocketmq">1. Message Queue for Apache RocketMQ</h3>
<p>Message Queue for Apache RocketMQ는 Apache RocketMQ를 기반으로 Alibaba Cloud에서 구축한 분산형 MOM 서비스로 짧은 대기 시간, 높은 동시성, 고가용성 및 높은 안정성을 제공합니다. Message Queue for Apache RocketMQ는 분산 애플리케이션에 대한 비동기식 분리 및 로드 이동을 제공하고 다량의 메시지 저장, 높은 처리량 및 안정적인 재시도를 포함하여 인터넷 기반 애플리케이션을 위한 기능을 지원합니다.</p>
<p><strong>아키텍쳐</strong></p>
<p><img src="https://user-images.githubusercontent.com/34003729/148867456-b058ae1f-54c0-4658-8c48-f190aa38e191.png" alt="image"></p>
<ul>
<li>
<p><strong>Producer Cluster</strong> : Producer Cluster는 메시지를 전송하는 애플리케이션을 나타냅니다. Producer Cluster에는 여러 개의 Producer 인스턴스가 포함되거나 하나의 Producer 프로세스 또는 한 프로세스의 여러 Producer 개체가 될 수 있습니다. Producer Cluster는 여러 주제의 메시지를 보낼 수 있습니다. 만약 분산 트랜잭션 메시지가 전송될 때 Producer가 실패하는 케이스가 생기면 Message Queue for Apache RocketMQ Broker는 트랜잭션 상태를 확인하기 위해 Producer Cluster의 시스템을 지속적으로 호출합니다.</p>
</li>
<li>
<p><strong>Consumer Group</strong> : Consumer Group은 메시지를 Subscribe/Consuming하는 애플리케이션을 나타냅니다. Consumer Group에는 여러가지 컴퓨터, 프로세스 또는 한 프로세스의 여러 Consumer 객체가 될 수 있는 다수의Consumer 인스턴스 그룹이 포함될 수 있습니다.</p>
</li>
</ul>
<h3 id="message-queue-for-apache-kafka">2. Message Queue for Apache Kafka</h3>
<p>Message Queue for Apache Kafka는 Alibaba Cloud에서 제공하는 분산 Message Queue 서비스입니다. 이 서비스는 높은 처리량과 확장성을 제공합니다. Message Queue for Apache Kafka는 로그 수집, 데이터 집계 모니터링, 스트리밍 데이터 처리, 온라인 및 오프라인 분석과 같은 빅 데이터 분야에서 주로 사용됩니다.</p>
<p>Message Queue for Apache Kafka서비스는 다음과 같은 케이스에서 사용될 수 있습니다.</p>
<ul>
<li>
<p><strong>빅 데이터</strong>  : Message Queue for Apache Kafka는 웹 사이트 동작 분석, 로그 집계, 애플리케이션 모니터링, 스트리밍 데이터 처리, 온라인 및 오프라인 데이터 분석과 같은 분야에서 널리 사용됩니다.</p>
</li>
<li>
<p><strong>데이터 통합</strong>  : Message Queue for Apache Kafka를 사용하면 MaxCompute, OSS(Object Storage Service), ApsaraDB RDS, Hadoop 및 HBase와 같은 데이터 웨어하우스의 메시지를 쉽게 통합할 수 있습니다.</p>
</li>
<li>
<p><strong>스트림 컴퓨팅 통합</strong>  : Message Queue for Apache Kafka는 Realtime Compute, E-MapReduce, Spark 및 Storm과 같은 스트림 컴퓨팅 엔진을 통합할 수 있습니다.</p>
</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/148867520-57c8f329-8ff1-481d-83b0-f7f0888709e7.png" alt="image"></p>
<h3 id="message-queue-for-rabbitmq">3. Message Queue for RabbitMQ</h3>
<p>Message Queue for RabbitMQ는 AMQP(Advanced Message Queuing Protocol) 0-9-1을 기반으로Alibaba Cloud 팀에서 개발한 Message Queue서비스입니다. 이 서비스는 높은 처리량, 짧은 대기 시간 및 높은 확장성을 제공합니다.</p>
<p>Message Queue for RabbitMQ는 모든 오픈 소스 RabbitMQ 클라이언트와 완벽하게 호환됩니다.따라서 서비스를 배포하고 관리를 할 때 별도의 커스터마이징을 수행할 필요가 없습니다.</p>
<p>Message Queue for RabbitMQ에서 사용하는 용어들은 아래와 같습니다.</p>
<ul>
<li><strong>Producer</strong>: 메시지를 보내는 애플리케이션</li>
<li><strong>Consumer</strong>: 메시지를 받는 애플리케이션</li>
<li><strong>Exchange</strong>: 메시지를 큐로 라우트하는 컴포넌트</li>
<li><strong>Queue</strong>: 메시지가 저장되는 버퍼</li>
</ul>
<p>Message Queue for RabbitMQ에서는 다음 단계로 메시지가 라우트됩니다.</p>
<ol>
<li>
<p>Producer는 Exchange로 메시지를 전송합니다.</p>
</li>
<li>
<p>Exchange는 메시지를 알맞은 장소에 저장하기위해 메시지의 속성 값을 기반으로 라우트하여 Queue로 전송합니다.</p>
</li>
<li>
<p>Consumer는 메시지를 사용하기 위해 Queue에서 메시지를 가져옵니다.</p>
</li>
</ol>
<p><img src="https://user-images.githubusercontent.com/34003729/148867592-24987a0d-56ec-46a3-b1f4-d4ce8d67da35.png" alt="image"></p>
<h3 id="message-queue-for-mqtt">4. Message Queue for MQTT</h3>
<p>Message Queue for MQTT는 모바일 인터넷 및 IoT 시나리오를 위해 Alibaba Cloud에서 제공하는 경량 메시징 미들웨어입니다. 전통적인 메시징 미들웨어는 일반적으로 마이크로 서비스 간에 사용됩니다. 그러나 IoT 시나리오용으로 설계된 Message Queue for MQTT는 디바이스와 클라우드 간에 메시지를 전송하는 IoT를 구현하기 위한 전용 미들웨어입니다.</p>
<ul>
<li><strong>Topic</strong> : level-1 message를 처리하는 컴포넌트를 의미한다. Producer는 메시지를 토픽으로 전송한다.</li>
<li><strong>Producer</strong> : Producer는 메시지를 만들고Topic으로 토픽으로 전송하는 주체를 의미한다.</li>
<li><strong>Consumer</strong> : Consumer는 Topic으로 부터 메시지를 구독하고 소비하는 주체를 의미한다.</li>
<li><strong>Message</strong> : Producer가 Topic으로 보내고 최종적으로 Consumer가 사용하는 데이터를 의미한다.</li>
<li><strong>Rule</strong> : Message Queue for MQTT 와 다른 Alibaba Cloud service간의 데이터 교환을 위한 리소스를 의미한다.</li>
</ul>
<p><strong><em>USE CASE : IoT 디바이스와 백엔드 서비스 애플리케이션 간의 상호작용 모델</em></strong></p>
<p>이 모델에서 Message Queue for MQTT는 디바이스를 백엔드 서비스 애플리케이션에 연결하여 디바이스와 백엔드 애플리케이션 간의 양방향 통신을 구현합니다. 디바이스는 Message Queue for MQTT를 사용하여 백엔드 서비스 애플리케이션과 직접 통신할 수 있습니다.</p>
<p>이 모델의 일반적인 시나리오는 스마트 디바이스의 상태 데이터를 클라우드에 전달하거나 백엔드 서비스 애플리케이션에서 스마트 디바이스로의 명령을 전달하는 과정에서 이용합니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/148867632-4f1653ce-f681-4029-b748-6862bf9de8c3.png" alt="image"></p>
<h2 id="mq-service-비교">MQ Service 비교</h2>
<p>전부 비동기 통신을 제공하고, 보낸 사람과 받는 사람을 분리합니다. 하지만 각각 다른 특성을 지니고 있어 업무에 특성에 맞게 서비스를 선택할 수 있습니다.</p>
<p><strong>1. RocketMQ</strong> : 알리바바에서 오픈소스로 배포한 고성능 MQ 미들웨어</p>
<ul>
<li>다양한 언어(Java, C, C++, Python, Go, Node.JS)와 프로토콜(TCP, JMS, OpenMessaging)을 지원</li>
<li>지연 속도가 낮고, 성능, 안전성을 고려한 기술</li>
<li>특히 이커머스 분야에 최적화 되었으며 알리바바 그룹의 알리페이, 타오바오에 기술이 적용되어 안정성을 검증함</li>
<li>구현이 간단하고 아키텍쳐 이해가 용이</li>
</ul>
<p><strong>2. Kafka</strong> : 확장성, 고성능 및 높은 처리량에 집중한 MQ 미들웨어</p>
<ul>
<li>대용량 실시간 로그 처리에 특화, 단순한 메시지 헤더를 지닌 TCP 기반의 프로토콜 사용으로 오버헤드 감소</li>
<li>분산 시스템으로 인해 분산 및 복제 구성에 장점</li>
<li>프로듀서는 각 메시지를 배치 방식으로 broker에게 전달하여 TCP/IP 라운드 트립을 줄임</li>
<li>기본적으로는 파일시스템에 저장을 통해 영속성을 보장, 오류 지점부터 복구가 가능</li>
</ul>
<p><strong>3. RabbitMQ</strong> : 빠르고 쉽게 구성 할 수 있으며 직관적인 MQ 미들웨어</p>
<ul>
<li>AMQT 프로토콜을 구현해 놓은 프로그램, 신뢰성, 유연한 라우팅, 관리 UI의 편리성</li>
<li>MOM 및 P2P 데이터 전송에 사용할 수 있는 범용적인 메시징 프로토콜로 설계</li>
<li>메시지 라우팅을 지원. 특정 서버, 서버 그룹 또는 모든 서버에서 동일한 작업을 실행해야 할 때 유용</li>
<li>P2P 및 pub-sub 메시징 기술을 모두 지원</li>
<li>추가적인 프로토콜과 기술은 별도 3rd party로 구현 필요</li>
</ul>
<p><strong>4. MQTT</strong> : IoT에 적합한 MQ 미들웨어</p>
<ul>
<li>복잡한 메시지 라우팅을 지원하지 않음</li>
<li>IOT 장치용으로 설계. 대역폭이 제한된 네트워크를 통해 메시지를 보내는 원격 저전력 장치에 이상적</li>
<li>효율적이며 클라이언트에서 구현하기 위한 노력이 적음</li>
<li>pub-sub 메시징 기술만 지원</li>
<li>LVQ(Last-Value-Queues)를 지원하여 새로운 소비자가 이전 메시지를 건너뛰고 최신 메시지를 가져온 다음 동일한 메시지에 대한 업데이트 수신 가능</li>
</ul>
<h2 id="결론">결론</h2>
<p>정리하자면 4개의 Message Queue 서비스는 다음과 같은 특성을 지니고 있습니다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/148867681-3d0295db-8cde-4836-92c0-9e695f353185.png" alt="image"></p>
<p>위에서 명시한 기능, 성능, 특성의 이유로 각 Message Queue 서비스는 아래와 같은 케이스에서 가장 적합하게 운영될 수 있습니다.</p>
<ul>
<li>
<p><strong>Message Queue for RabbitMQ</strong> : 소규모의 프로젝트나 MQ를 제약적으로 사용할 때</p>
</li>
<li>
<p><strong>Message Queue for RocketMQ</strong> : 대규모의 프로젝트나 다른 종류의 서비스 통합 용도로 MQ를 범용적으로 사용할 때</p>
</li>
<li>
<p><strong>Message Queue for Kafka</strong> : 빅데이터의 시나리오에서 MQ를 사용할 때</p>
</li>
<li>
<p><strong>Message Queue for MQTT</strong> : IoT 시나리오나 소규모 디바이스 통신에서 MQ를 사용할 때</p>
</li>
</ul>
<p>지금까지 알리바바 클라우드에서 제공하는 대표적인 4가지 Message Queue 서비스에 대해서 알아보았습니다. 만약 각 서비스에 대한 Use-case 혹은 정확한 사용법, PoC 절차에 대해 궁금하시다면 <a href="mailto:cloudkorea@list.alibaba-inc.com">cloudkorea@list.alibaba-inc.com</a>으로 메일 주시기 바랍니다.</p>

