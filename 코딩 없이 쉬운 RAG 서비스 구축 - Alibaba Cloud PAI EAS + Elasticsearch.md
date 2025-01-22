---


---

<h1 id="코딩-없이-쉬운-rag-서비스-구축---alibaba-cloud-pai-eas--elasticsearch">코딩 없이 쉬운 RAG 서비스 구축 - Alibaba Cloud PAI EAS + Elasticsearch</h1>
<h2 id="개요">1. 개요</h2>
<p>AI 기술의 급속한 발전과 함께 생성형 AI는 텍스트 생성, 이미지 생성 등 다양한 분야에서 주목할 만한 성과를 이루어냈습니다. 그러나 대규모 언어 모델(LLM)이 광범위하게 사용되면서 다음과 같은 근본적인 한계점들이 점차 드러나고 있습니다:</p>
<ul>
<li>
<p>전문 분야 지식의 한계: 대부분의 경우 LLM은 대규모 범용 데이터셋으로 학습되기 때문에, 전문적인 특정 분야에 대한 심층적이고 targeted된 처리를 제공하는 데 어려움을 겪습니다.</p>
</li>
<li>
<p>정보 업데이트 지연: 학습 데이터셋의 정적인 특성으로 인해 LLM은 실시간 정보와 최신 지식을 접근하고 통합하는 데 제약이 있습니다.</p>
</li>
<li>
<p>잘못된 출력: LLM은 환각 현상을 일으키기 쉬워, 그럴듯해 보이지만 사실과 다른 출력을 생성할 수 있습니다. 이는 데이터 편향성과 모델의 내재적 한계와 같은 요인들로 인한 것입니다.</p>
</li>
</ul>
<p>이러한 과제들을 해결하고 LLM의 성능과 정확도를 향상시키기 위해 RAG가 개발되었습니다. RAG는 외부 지식 베이스를 통합하여 LLM의 환각 현상을 크게 완화하고, 최신 지식에 대한 접근과 활용 능력을 강화합니다. 이를 통해 LLM의 개인화와 정확도를 높이는 맞춤형 구성이 가능해졌습니다. 하지만 이 RAG 시스템은 기본적인 AI 및 데이터에 대한 이해와 라이브러리 사용 등 전문적인 지식이 필수입니다.</p>
<p>하지만 알리바바 클라우드의 Platform for AI(PAI) 서비스와 Elasticsearch를 활용하면 RAG 시스템을 손쉽게 배포할 수 있습니다. 이번 블로그에서는 어떻게 RAG 챗봇 시스템을 손쉽게 구성할 수 있을지에 대해 알아보겠습니다.</p>
<h2 id="구성-요소">2. 구성 요소</h2>
<h3 id="pai-eas-elastic-algorithm-service">PAI EAS (Elastic Algorithm Service)</h3>
<p>PAI의 온라인 모델 서비스 플랫폼으로, 모델을 온라인 추론 서비스 또는 AI 기반 웹 애플리케이션으로 배포할 수 있습니다.</p>
<ul>
<li>자동 확장 및 블루-그린 배포</li>
<li>리소스 그룹 관리</li>
<li>모델 버전 관리</li>
<li>종합적인 운영 및 모니터링</li>
</ul>
<h3 id="elasticsearch-vector-db">Elasticsearch (Vector DB)</h3>
<p>알리바바 클라우드의 매니지드 서비스로, 오픈소스 Elasticsearch를 기반으로 개발되었습니다.</p>
<ul>
<li>Elastic Stack 구성 요소 제공 (Elasticsearch, Logstash, Kibana, Beats)</li>
<li>X-Pack 상용 플러그인 무료 제공</li>
<li>실시간 로그 분석, 정보 검색, 다차원 데이터 쿼리 지원</li>
</ul>
<h2 id="구성-방법">3. 구성 방법</h2>
<h3 id="알리바바-클라우드-elasticsearch-클러스터-배포">3.1 알리바바 클라우드 Elasticsearch 클러스터 배포</h3>
<h4 id="콘솔-접속">3.1.1 콘솔 접속</h4>
<p>Elasticsearch 콘솔에 접속하여 클러스터를 배포할 준비를 합니다.</p>
<img width="1724" alt="Image" src="https://github.com/user-attachments/assets/cb8254d0-11ff-495b-b525-abb26e4d0f94">
<h4 id="클러스터-생성">3.1.2 클러스터 생성</h4>
<p>Elasticsearch 메뉴의 Create Cluster를 클릭하여 제품 구매페이지로 넘어갑니다.</p>
<img width="1051" alt="Image" src="https://github.com/user-attachments/assets/a2b608ba-2173-4052-8469-ca3af56fd8c2">
<h4 id="elasticsearch-구독-옵션-상세">3.1.3 Elasticsearch 구독 옵션 상세</h4>
<p>제품 구매 페이지에는 다양한 옵션이 있으며, 아래 상세에서 옵션에 대한 설명을 확인할 수 있습니다.</p>
<img width="1723" alt="Image" src="https://github.com/user-attachments/assets/41b019f3-2548-47b7-ac38-b4a468125cce">
<img width="1726" alt="Image" src="https://github.com/user-attachments/assets/db330f91-0a78-49d1-8e47-a7acb56351cd">
<img width="1728" alt="Image" src="https://github.com/user-attachments/assets/a1a0877b-70f1-411a-900f-7f9c0aa5e312">
<ul>
<li>
<p>Instance Type : 시나리오에 따라 적절한 Instance Type을 선택할 수 있으며 AI의 RAG 등의 시나리오일 때는 <strong>Vector Enhanced Edition</strong> / 일반 로깅과 같은 시나리오일 때는 <strong>Standard Edition</strong> / 실시간 로깅이나 성능이 필요한 시나리오일 때는 <strong>Kernal Enhanced Edition</strong>을 선택할 수 있습니다. 여기서는 <strong>Standard Edition</strong>을 선택합니다.</p>
</li>
<li>
<p>Data Node Type : 하나의 Data Node에서 사용할 리소스의 크기를 설정합니다.</p>
</li>
<li>
<p>Data Nodes : Data Node의 갯수를 선택합니다.</p>
</li>
<li>
<p>Data Node Disk Type : Data Node에서 활용될 스토리지의 타입을 결정합니다.</p>
</li>
<li>
<p>Performance Level : 선택한 스토리지 타입의 퍼포먼스 레벨을 설정합니다. 숫자가 높아질수록 높은 IOPS를 제공합니다.</p>
</li>
<li>
<p>Data Node Disk Encryption : Data Node에 저장될 데이터에 암호화가 적용될 지에 대한 기능입니다.</p>
</li>
<li>
<p>Data Node Storage Space : Data Node의 Storage 용량을 설정합니다.</p>
</li>
<li>
<p>Kibana Node Type : ES와 함께 배포될 Kibana 노드의 사이즈를 결정합니다.</p>
</li>
</ul>
<h4 id="설정된-옵션-확인">3.1.4 설정된 옵션 확인</h4>
<p>Buy Now를 클릭하면 배포될 Elasticsearch 클러스터의 옵션을 확인합니다.</p>
<img width="1726" alt="Image" src="https://github.com/user-attachments/assets/c1f9bf10-92b6-4bf8-b549-2c8ec96733e3">
<h4 id="배포된-클러스터-확인">3.1.5 배포된 클러스터 확인</h4>
<p>좌측 메뉴의 Elasticsearch Cluster에서 배포된 인스턴스의 상태를 확인합니다.</p>
<img width="1726" alt="Image" src="https://github.com/user-attachments/assets/81a5ae18-b2c1-4180-a01a-fd2551bb242f">
<h4 id="설정-준비">3.1.6 설정 준비</h4>
<p>배포된 Elasticsearch 클러스터 상세에서 아래 정보를 확인하여 메모를 합니다.<br>
<img src="https://github.com/user-attachments/assets/a12f50b6-bcad-49cc-9d39-0ba577397eef" alt="Image"></p>
<p>Format: <code>http://&lt;Internal endpoint&gt;:&lt;Port number&gt;</code>.</p>
<h4 id="index-name-준비">3.1.7 Index Name 준비</h4>
<p>해당 클러스터가 Indexing을 할 수 있도록 설정합니다. 좌측 네비게이션에서 Configuration and Management &gt; Cluster Configuration을 선택하고 우측의 Modify Configuration을 선택하여 Auto Indexing을 Enable합니다.<br>
<img src="https://github.com/user-attachments/assets/ce4b548b-216f-42df-b6ce-2c6be5680a27" alt="Image"></p>
<p>설정이 완료되면 우리의 RAG 서비스의 Vector DB를 Elasticsearch로 활용할 준비가 되었습니다.</p>
<h3 id="rag-based-llm-챗봇-배포---elasticsearch-활용">3.2 RAG-based LLM 챗봇 배포 - Elasticsearch 활용</h3>
<h4 id="pai-console에서-eas-배포-확인">3.2.1 PAI Console에서 EAS 배포 확인</h4>
<p>PAI Console에 로그온하여, 좌특의 Model Training &gt; Elastic Algorithm Service (EAS)에 접속합니다.<br>
<img src="https://github.com/user-attachments/assets/894463cc-ad0d-4c20-b86d-f932289c6ee4" alt="Image"></p>
<h4 id="rag-서비스-배포를-위한-템플릿-선택">3.2.2 RAG 서비스 배포를 위한 템플릿 선택</h4>
<p>Elastic Algorithm Service (EAS) 페이지에서 Deploy Service를 선택하면 배포할 수 있는 서비스 템플릿을 확인할 수 있습니다. 여기서 Scenario-based Model Deployment &gt; <strong>RAG-based Smart Dialogue Deployment</strong>를 선택합니다.<br>
<img src="https://github.com/user-attachments/assets/0cbfc152-f492-4e5a-ab47-f4a87a9d9821" alt="Image"></p>
<h4 id="rag-서비스-설정">3.2.3 RAG 서비스 설정</h4>
<p>배포된 RAG 서비스의 설정 값을 확인하여 배포합니다.<br>
<img width="1430" alt="Image" src="https://github.com/user-attachments/assets/83bc2961-64d0-4b59-b6be-25e33d080e18"><br>
<img width="1416" alt="Image" src="https://github.com/user-attachments/assets/cc99ee81-593e-4bf5-85ae-c983e88cc16a"></p>
<ul>
<li>Service Name : 배포될 서비스의 이름을 입력합니다</li>
<li>Model Source : RAG 서비스에서 활용할 LLM을 선택합니다. Opensource 모델 혹은 Fine-tuned Model 중 선택할 수 있으며, Qwen 1.5, 2 / llama 2, 3 / chatglm 3 / baichuan 2 / falcon / yi / mixtral / gemma / deepseek 등을 활용할 수 있습니다.</li>
<li>Resource Configuration : 서비스가 배포될 인스턴스를 정의합니다. 기본적으로 LLM 모델에 적합한 인스턴스를 추천합니다.</li>
<li>Vector Database Setting : Elasticsearch를 선택하고 3.1에서 설정한 Elasticsearch의 접속 정보를 입력할 수 있습니다.</li>
<li>Private Endpoint and Port : 3.1에서 설정한 ES의 정보를 입력합니다. 포멧은  <code>http://&lt;Internal endpoint&gt;:&lt;Port number&gt;</code>형태입니다.</li>
<li>Index Name : 새로운 Index Name이나 기존에 존재하는 Index Name을 입력합니다. 만약 기존의 Index를 사용한다면, 스키마는 반드시 RAG-chatbot의 요구사항과 맞아야합니다. 만약 새로운 index 이름을 넣으면 자동으로 스키마가 생성됩니다.</li>
<li>Account : elastic을 입력합니다</li>
<li>Password : 3.1에서 설정한 Password를 입력합니다.</li>
</ul>
<p>Deploy를 누르면 RAG 서비스가 자동으로 배포됩니다.</p>
<h3 id="배포된-rag-서비스-확인-및-활용">3.3 배포된 RAG 서비스 확인 및 활용</h3>
<p>서비스 배포가 완료되면 Service Type 항목의 View Web App을 클릭하여 서비스에 접속합니다.<br>
<img width="1403" alt="Image" src="https://github.com/user-attachments/assets/fc5cb274-5f00-4e75-b7f3-32b34eb78daf"></p>
<h4 id="vector-database-연결-elasticsearch-확인">3.3.1 Vector database 연결 (Elasticsearch) 확인</h4>
<p>Web App을 확인하면 Elasticsearch가 연결되어있는 것을 확인합니다.<br>
<img width="1527" alt="Image" src="https://github.com/user-attachments/assets/a32b713c-29b3-4c9c-8c40-db5c16d0b419"></p>
<h4 id="비즈니스-데이터-파일-업로드">3.3.2 비즈니스 데이터 파일 업로드</h4>
<p>Upload 탭에는 chunk parameter를 업데이트할 수 있는 옵션이 있습니다. 여기에 chunk Size, Overlap을 설정할수 있습니다.<br>
또한 아래에는 Process with QA Extraction Model이 존재하는데, 이 옵션은 Q&amp;A 정보 추출 여부를 지정합니다. <strong>Yes</strong>를 선택하면 Knowleage 파일이 업로드된 후 시스템이 자동으로 질문과 그에 해당하는 답변을 쌍으로 추출합니다. 이를 통해 데이터 쿼리 시 더욱 정확한 답변을 얻을 수 있습니다.</p>
<p>여기에 아래 샘플 Knowleage File을 업로드합니다. 업로드가 가능한 파일 형태는 txt, pdf, excel, csv, word, markdown, html이 있습니다.<br>
<img width="1565" alt="Image" src="https://github.com/user-attachments/assets/1e55d30b-f077-4c9f-9107-05751de77774"><br>
샘플 Knowleage File : <a href="https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/en-US/20241030/unewhk/rag_chatbot_test_doc.txt">rag_chatbot_test_doc.txt</a></p>
<h4 id="rag--llm-챗봇-결과-확인">3.3.3 RAG + LLM 챗봇 결과 확인</h4>
<p>chat 영역에 원하는 질의를 입력하고 결과값을 확인합니다.<br>
<img src="https://github.com/user-attachments/assets/22c3a8ce-6ea1-4031-9be2-ac40b6d7c55f" alt="Image"></p>
<h4 id="api-확인">3.3.4 API 확인</h4>
<p>만약 해당 서비스를 API를 활용하여 다른 서비스에 연결하고 싶을 때는, RAG 서비스의 최하단에 use via API 버튼을 클릭하여 API 명세를 확인할 수 있습니다. (Python, Javascrip)<br>
<img width="1148" alt="Image" src="https://github.com/user-attachments/assets/a6727ae4-ca66-4591-bcfc-632bcf5a0023"></p>
<h2 id="참고-자료">4. 참고 자료</h2>
<ol>
<li><a href="https://www.alibabacloud.com/help/en/pai">Alibaba Cloud PAI (Platform for AI) 공식 문서</a></li>
<li><a href="https://www.alibabacloud.com/help/en/elasticsearch">Alibaba Cloud Elasticsearch 서비스 가이드</a></li>
<li><a href="https://www.alibabacloud.com/help/en/pai/user-guide/eas-overview">EAS (Elastic Algorithm Service) 개요</a></li>
</ol>
<h2 id="마치며">5. 마치며</h2>
<p>지금까지 PAI와 Elasticsearch 서비스를 활용하여 쉽게 RAG 챗봇을 배포하는 방법에 대해서 알아보았습니다. 많은 기업들은 자신들의 KMS에 존재하는 정보들을 참조하는 챗봇을 배포하고 싶어합니다. 이러한 요구사항에서 이 블로그를 참조한다면 보다 쉽고 빠른 비즈니스 혁신을 이룰 수 있을 것이라고 생각합니다.</p>
<p>추가적인 질의사항은 알리바바 클라우드 코리아팀에 연락 주시기 바랍니다.</p>

