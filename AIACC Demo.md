---


---

<h1 id="alibaba-cloud-aiacc로-k8s-환경에서의-tensorflow-워크로드-가속화하기">Alibaba Cloud AIACC로 k8s 환경에서의 TensorFlow 워크로드 가속화하기</h1>
<p><em>이 블로그는 Alibaba Cloud의 Cloud Native AI PD Team의 가이드를 기반으로 테스트를 경험한 내용으로 작성하였습니다. 또한 블로그 편의상 존대를 사용하지 않았음을 양해 부탁드립니다.</em></p>
<h2 id="배경-설명">1. 배경 설명</h2>
<p>우리는 최근 뉴스를 보면 IT, 제조, 금융, 공공, 의료 등 분야를 가리지 않고 Machine Learning 기술이 활용되고 있음을 확인할 수 있다.<br>
Machine Learning이라고 불리는 이 기술은 빅데이터를 이용하여 가장 적합한 해답을 찾아가는 과정을 지속적으로 반복 학습시키는 것을 의미한다.</p>
<p>실질적인 기술로 보면, CPU보다 단순 연산이 빠른 GPU를 이용하여 Tensorflow, Pythorch 등의 라이브러리로 다양한 형태의 데이터를 Machine에게 학습시킨다.<br>
기업이 이 기술을 이용하기 위해 가장 중요한 수치는 <strong>속도, 정확도</strong>이다. 기업은 최소한의 리소스와 비용으로 최대한의 가치를 얻고자한다.</p>
<p>오늘 소개할 AIACC(AI Acceleration)라고 불리는 기술은 하드웨어 리소스가 아닌 소프트웨어 로직처리를 이용하여 Machine Learning을 가속화하는 기술이다.<br>
이 블로그에서 AIACC를 사용하는 방법과 기존 환경 대비 얼마나 가속화되는지에 대한 수치를 확인할 수 있다. 특히 이번에 구현한 데모는 Instance Level이 아닌, Machine Learning 기술의 핫 트렌드인 Kubernetes 환경에서 테스트를 진행할 것이다.</p>
<h2 id="solution-overview">2. Solution Overview</h2>
<p>AIACC는  <strong>저비용, 고성능, 고효율</strong>의 AI 가속기다. Apsara AIACC는 안정적이고 사용이 편리한 알리바바 클라우드 리소스에 기반한다. FastGPU와 함께 교육 작업을 구축해 리소스를 만들고 구성하는 시간을 단축시키며 GPU 리소스 활용도를 향상시켜 비용을 절감한다. Apsara AIACC는 다양한 프레임워크에 대한 통합 가속을 지원해 보다 적은 적응 워크로드를 제공하고 훈련 및 추론 성능을 향상시킵니다.</p>
<ul>
<li>
<p><strong>통합적인 가속</strong><br>
TensorFlow, Caffe, MXNet 및 PyTorch에서 다양한 AI 프레임워크의 통합 가속을 제공</p>
</li>
<li>
<p><strong>심층적인 성능 최적화</strong><br>
GPU, CPU, 네트워크, I/O 등 Alibaba Cloud의 기본 IaaS 리소스를 기반으로 심층적인 성능 최적화를 제공</p>
</li>
<li>
<p><strong>자동 스케일링</strong><br>
기본 IaaS 리소스를 기반으로 빠른 구축 및 자동 확장을 지원</p>
</li>
<li>
<p><strong>오픈 소스 프레임워크와의 호환성</strong><br>
가볍고 편리하며 오픈 소스 프레임워크와 호환. 오픈 소스 프레임워크를 기반으로 작성하는 알고리즘 코드 또는 모델 코드는 수정이 불필요</p>
</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/34003729/139192643-a1fa6735-0f7f-4414-bc7e-bdedc864f202.png" alt="image"></p>
<h2 id="사전--준비">3. 사전  준비</h2>
<ul>
<li><a href="https://www.alibabacloud.com/help/product/85222.htm?spm=a2c63.m28257.a1.105.26125922Axipl7">ACK (Alibaba Cloud Container service for Kubernetes)</a></li>
<li><a href="https://www.alibabacloud.com/help/product/25365.htm?spm=a2c63.m28257.a1.1.26125922OCKW6p">ECS (Elastic Computing Service)</a></li>
<li><a href="https://www.alibabacloud.com/help/product/27516.htm?spm=a2c63.m28257.a1.20.26125922PfxVke">NAS (Network Attached Storage Service)</a></li>
<li><a href="https://www.alibabacloud.com/help/doc-detail/201994.htm?spm=a2c63.p38356.b99.824.72a87570dYdFmh">AIACC (AI Acceleration)</a></li>
<li><a href="https://www.tensorflow.org/?hl=ko">Tensorflow</a></li>
<li><a href="https://image-net.org/index.php">Imagenet</a></li>
</ul>
<h2 id="main-steps">4. Main Steps</h2>
<h3 id="prepare-data">4.1 Prepare Data</h3>
<p>이 단계에서는 Imagenet에서 학습시킬 이미지 데이터를 가져와 NAS Volume에 삽입하고 학습 가능한 형태로 파일 형태를 변경하는 작업을 수행한다.</p>
<p>먼저, 우리는 데이터를 저장할 NAS와 마운트할 인스턴스인 ECS를 배포한다.</p>
<h4 id="ecs-배포">4.1.1 ECS 배포</h4>
<p><em>ECS Console - Create instance</em>에서 다음과 같이 만든다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139355839-8b0c5be9-d575-428a-8569-3726d122d5b9.png" alt="image"><br>
<img src="https://user-images.githubusercontent.com/34003729/139355887-c2c9e768-0fda-47ef-8843-6b44db46a189.png" alt="image"></p>
<h4 id="nas-배포-및-mount">4.1.2 NAS 배포 및 Mount</h4>
<p>NAS Console - Create a General Purpose NAS File System을 눌러 NAS를 배포한다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139355931-c25072aa-4c85-45a3-8134-177f8f2da906.png" alt="image"><br>
<img src="https://user-images.githubusercontent.com/34003729/139355975-420ef269-65ba-4c46-921f-ec3140c921a1.png" alt="image"></p>
<p>NAS Mount를 위해 생성된 NAS를 클릭하고 Mount Target을 우리의 VPC group으로 지정한다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139356032-e7a7c4d3-aefd-496b-a613-79b387d83227.png" alt="image"><br>
<img src="https://user-images.githubusercontent.com/34003729/139356098-f73a24e1-5e72-4956-8d8a-169ff7bb3fc7.png" alt="image"></p>
<p>4.1.1 단계에서 생성한 인스턴스에 Volume을 마운트하기 위해 아래 설명서를 참조할 수 있다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139356138-3e9b027e-4bd0-46ee-a35e-2194eb31adea.png" alt="image"></p>
<h4 id="training-data와-valuation-data를-imagenet에서-다운로드">4.1.3 Training data와 Valuation data를 Imagenet에서 다운로드</h4>
<p>데이터를 다운로드하기 위해 <a href="https://image-net.org/challenges/LSVRC/2012/2012-downloads.php#images">ImageNet</a>에 가입하고 이미지 다운로드를 준비한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/139356203-5a8bfe0a-326a-470d-8494-6a0c3559dd97.png" alt="image"><br>
<img src="https://user-images.githubusercontent.com/34003729/139356344-26e7ad63-910e-41b3-9ef2-1fa97f7b92d2.png" alt="image"></p>
<p><strong>"Training images (Task 1 &amp; 2), Training images (Task3), Validation images (all tasks)"</strong> 와 **“Training bounding box annotations (Task1 &amp; 2 only)”**를 NAS에 다운받는다. 보통, NAS는 ECS의 <strong>/mnt</strong> 에 마운트되어있다.</p>
<blockquote>
<p>큰 파일은 커넥션 끊김을 방지하기 위해 nohup을 이용한다.</p>
</blockquote>
<pre><code>cd /mnt
nohup wget https://image-net.org/data/ILSVRC/2012/ILSVRC2012_img_train.tar
wget https://image-net.org/data/ILSVRC/2012/ILSVRC2012_img_train_t3.tar
nohup wget https://image-net.org/data/ILSVRC/2012/ILSVRC2012_img_val.tar
wget https://image-net.org/data/ILSVRC/2012/ILSVRC2012_bbox_train_v2.tar.gz
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139356400-71655314-04d0-4eb2-aa13-76a358e623b3.png" alt="image"></p>
<h4 id="tranfer-data-tool-download">4.1.4 Tranfer data tool download</h4>
<p>위에서 받은 파일은 tensorflow framework에서 사용할 수 없는 파일이다. 우리는 트레이닝을 위해 이 파일을 TFRecords로 바꿀 필요가 있다.</p>
<p>먼저, 데이터 변환을 위한 툴을 아래 링크에서 다운받고 압축을 해제한다<br>
<a href="https://code.aliyun.com/best-practice/013/blob/master/imagenet-dp.tgz">https://code.aliyun.com/best-practice/013/blob/master/imagenet-dp.tgz</a></p>
<h4 id="validation-images-데이터-변환">4.1.5 “Validation images” 데이터 변환</h4>
<p>검증된 이미지를 트레이닝에 활용하기위해 데이터를 변환한다.</p>
<p><strong>a) Unzip <em>ILSVRC2012_img_val.tar</em></strong></p>
<pre><code>cd /mnt  
mkdir -p /mnt/val  
tar xvf ILSVRC2012_img_val.tar -C val  
ls /mnt/val  
ILSVRC2012_val_00000001.JPEG ILSVRC2012_val_00016668.JPEG ILSVRC2012_val_00033335.JPEG
</code></pre>
<blockquote>
<p>꽤 오랜 시간이 걸리니 시간을 감안해야한다.</p>
</blockquote>
<p><strong>b) 데이터 변환</strong></p>
<pre><code>cd /mnt/imagenet-dp  
python3 preprocess_imagenet_validation_data.py /mnt/val imagenet_2012_validation_synset_labels.txt
ls /mnt/val
n01440764 n01860187 n02097474 n02165105 n02669723 n03042490
</code></pre>
<h4 id="bound-boxes-데이터-변환">4.1.6 “Bound boxes” 데이터 변환</h4>
<p><strong>a) Unzip bbox</strong></p>
<pre><code>mkdir -p /mnt/bbox
tar xzvf ILSVRC2012_bbox_train_v2.tar.gz -C bbox
</code></pre>
<blockquote>
<p>이 또한 매우 오랜 시간이 걸린다</p>
</blockquote>
<p><strong>b) 데이터 변환</strong></p>
<pre><code>python3 process_bounding_boxes.py /mnt/bbox imagenet_lsvrc_2015_synsets.txt |sort &gt;bbox.csv
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139356466-1bd6bcb7-eb96-467d-b5bf-10f0cbd5f6c0.png" alt="image"></p>
<h4 id="training-images-task-1--2-데이터-변환">4.1.7 “Training images (Task 1 &amp; 2)” 데이터 변환</h4>
<p><strong>a) Unzip <em>ILSVRC2012_img_train.tar</em></strong></p>
<pre><code>cd /mnt  
mkdir -p /mnt/train  
tar xvf ILSVRC2012_img_train.tar -C train  
ls /mnt/train
</code></pre>
<p><strong>b) 한번 더 unzip을 해준다.</strong></p>
<pre><code>cd /mnt/train
vim unzip.sh
</code></pre>
<p>아래 코드를 <em><a href="http://unzip.sh">unzip.sh</a></em>에 입력한다.</p>
<pre><code>/bin/bash
for file in `ls *.tar`; do
subdir=${file:0:9}
mkdir -p ${subdir}
tar -C ${subdir} -xvf ${file} &amp;
done
</code></pre>
<p>입력한 코드를 실행하고 압축파일을 지운다.</p>
<pre><code> chmod +x unzip.sh
 ./unzip.sh
 rm -rf *.tar
</code></pre>
<p><strong>c) 데이터를 변환한다.</strong></p>
<pre><code>cd /mnt
mkdir -p /mnt/imagenet/imagenet_data/
cd /mnt/imagenet-dp
python3 build_imagenet_data.py --train_directory=/mnt/train/ \
--validation_directory=/mnt/val \
--output_directory=/mnt/imagenet/imagenet_data/ \
--imagenet_metadata_file=imagenet_metadata.txt \
--labels_file=imagenet_lsvrc_2015_synsets.txt \
--bounding_box_file=bbox.csv
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139356629-f6cbe090-5647-4bf7-924f-7173d50603f0.png" alt="image"></p>
<p>d) 모든 변환 데이터는 <em>/mnt/imagenet/imagenet_data</em>에 있다. 이 곳에 포함되는 파일은 train을 위한 "<strong>train-0xxxx-of-01024</strong>"와 validation을 위한 "<strong>validation-00xxx-of-00128</strong>"이 있다.</p>
<p>e) 모든 train 파일과 validation 파일을 train 폴더로 옮긴다.</p>
<pre><code>mkdir train
mv train*01024 train
mv validation*00128 train
</code></pre>
<h4 id="tensorflow-imagenet-수행을-위한-소스코드-clone">4.1.8 tensorflow-imagenet 수행을 위한 소스코드 Clone</h4>
<p>다음 단계에서 진행할 arena test를 수행하기 위한 스크립트가 저장되어있는 소스코드를 github에서 clone한다.</p>
<pre><code>git clone https://github.com/Jason-Jongjin-Lim/tensorflow-imagenet.git
</code></pre>
<h3 id="kubernetes-클러스터-배포-및-cloud-native-ai-component-세트-설치aiacc-with-k8s--arena">4.2 Kubernetes 클러스터 배포 및 Cloud-Native AI Component 세트 설치(AIACC with k8s / Arena)</h3>
<p>Cloud Native AI Suite는 GPU 활용 모니터링, 데이터 액세스 성능 가속화 등의 기능을 지원하는 AIOps 기능을 제공한다. 이러한 기능은 Professional Managed Kubernetes Cluster에 있으므로 이번 데모에서는 모두 Professional Managed Kubernetes Cluster를 사용할 것이다.</p>
<h4 id="ack-배포">4.2.1 ACK 배포</h4>
<p><em>ACK 콘솔 &gt; Select Cluster Template &gt; Heterogeneous Compution Cluster</em> 선택<br>
<img src="https://user-images.githubusercontent.com/34003729/139358334-5b1a39d7-aea7-47fd-862f-3efa0f14351f.png" alt="image"></p>
<p>ACK 클러스터 배포를 위한 설정을 할 때, 정확한 테스트를 위해 4개의 GPU를 가진 인스턴스 4개의 노드를 배포한다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139358462-22911036-5c2a-4eb3-83d7-e5aa0994592b.png" alt="image"></p>
<ul>
<li><em>K8s version</em> : 1.18.8-aliyun.1 (21년 10월 기준 K8S 1.18만 AIACC native workload를 지원한다)</li>
<li><em>Instance type</em> : ecs.gn6e-c12g1.12xlarge 48 vCPU 368 GiB (4x * NVIDIA V100)</li>
<li><em>Instance Quantity</em> : 4</li>
</ul>
<p>배포를 하게되면 K8S 풀 세트를 구성하기 위한 Progress를 콘솔에서 확인할 수 있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/139358662-d3311b8e-61dc-4165-af11-502f151d4ffd.png" alt="image"></p>
<p>ACK 클러스터가 배포되면 <em>Application &gt; Ai Acceleration</em> 메뉴에서 AIACC를 클러스터에 설치한다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139359157-9f06ed2c-169c-41c1-a663-76f582ab88e4.png" alt="image"></p>
<h4 id="클러스터에-arena-설치">4.2.2 클러스터에 Arena 설치</h4>
<p>우리는 Tensorflow를 이용해 학습시키기 위한 툴로 Arena를 이용할 것이다. 아래 링크에서 서버에 구성할 아레나 설치 방법을 확인할 수 있다.</p>
<p><a href="https://www.alibabacloud.com/help/doc-detail/212117.htm?spm=a2c63.p38356.b99.807.56a178e5whjjVj">https://www.alibabacloud.com/help/doc-detail/212117.htm?spm=a2c63.p38356.b99.807.56a178e5whjjVj</a></p>
<p><em>ACK 콘솔 &gt; Clusters &gt; More &gt; Manage System Components</em> 선택<br>
<img src="https://user-images.githubusercontent.com/34003729/139359460-b56ee63f-694e-4d97-8c93-84acf0e24cf2.png" alt="image"></p>
<p>항목 중 ack-arena를 설치한다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139359617-403f6c40-0f2d-467f-8695-bafb35ebb2a1.png" alt="image"></p>
<blockquote>
<p>테스트하는 날짜에 따라 업데이트가 되어 이미 설치가 되어있을 수 있다.</p>
</blockquote>
<h4 id="arena-client-설치">4.2.3 Arena client 설치</h4>
<p>서버에 설치된 Arena와 연결하기 위해 클라이언트 인스턴스에 Areana 클라이언트를 설치해야한다.</p>
<blockquote>
<p>본 데모에서는 클라이언트 인스턴스로 맥북을 사용했다.</p>
</blockquote>
<p><strong>a) 클라이언트 kubectl 설치</strong></p>
<pre><code>curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"

chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
sudo chown root: /usr/local/bin/kubectl

kubectl version --client
</code></pre>
<p><strong>b) 배포된 ACK 클러스터와 연결하기 위해 kubeconfig 수정</strong></p>
<p><em>ACK 콘솔 &gt; 클러스터 선택 &gt; Cluster information &gt; Connection information &gt; Public Access</em> 접속<br>
<img src="https://user-images.githubusercontent.com/34003729/139362219-78d31320-2394-4d61-a852-d4a9e38d5b4b.png" alt="image"></p>
<ul>
<li>Public Access의 접속 configuration을 복사</li>
</ul>
<pre><code>vi $HOME/.kube/config
(복사된 configuration을 붙여넣기)

Kubectl get namespaces
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139362468-3fccb582-5512-417b-9be5-f11f14b7eb10.png" alt="image"></p>
<p><strong>c) Arena client 다운로드 및 설치</strong><br>
arena client를 아래 링크에서 다운받는다.</p>
<p>download the package for MAC: <a href="https://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.8.8-d06b92d-darwin-amd64.tar.gz">https://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.8.8-d06b92d-darwin-amd64.tar.gz</a><br>
or for linux: <a href="https://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.8.8-d06b92d-linux-amd64.tar.gz">https://kubeflow.oss-cn-beijing.aliyuncs.com/arena-installer-0.8.8-d06b92d-linux-amd64.tar.gz</a></p>
<p>다운받은 패키지의 압축을 해제한다.</p>
<pre><code>cd arena-installer
bash install.sh --only-binary
</code></pre>
<p><strong>d) Arena client 테스트</strong></p>
<p>설치가 완료되면, 서버와 정상적으로 연결되었는지 확인하기 위해 GPU resoruce를 사용하기 위한 test query를 사용해본다.</p>
<pre><code>arena submit tf \
--name=firstjob \
--gpus=1 \
--image=registry.cn-hangzhou.aliyuncs.com/tensorflow-samples/tf-mnist-standalone:gpu \
"python /app/main.py"
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139363167-39f18a2e-2162-47bc-bc18-12d5b222f251.png" alt="image"></p>
<p>Submit된 Job을 확인하기 위해 아래 커맨드를 수행한다.</p>
<pre><code>Arena get firstjob
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139363478-43654498-d026-47d1-9162-61fe325b70a1.png" alt="image"></p>
<ul>
<li>Status가 Runinng이고, Node가 정상적으로 Attach 되었다면 서버연결 설정이 완료된 것이다.</li>
</ul>
<h3 id="kubernetes-클러스터에-nas-pv-pvc-attach">4.3 Kubernetes 클러스터에 NAS PV, PVC Attach</h3>
<h4 id="pv-생성">4.3.1 PV 생성</h4>
<p>먼저, NAS Console에 접속해서, 위 단계에서 사용한 NAS의 ID를 확인한다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/139364640-1df4f9a1-e161-4d77-abba-5f78ad25307b.png" alt="image"></p>
<p><em>ACK 콘솔 &gt; Volumes &gt; Persistent Volume &gt; Create PV</em> 선택<br>
<img src="https://user-images.githubusercontent.com/34003729/139364855-e047d003-25b8-468a-88a3-650122da3d94.png" alt="image"></p>
<ul>
<li><em>PV Type</em> : NAS</li>
<li><em>Volume Name</em> : imagenet-data</li>
<li><em>Volume Plug-in</em> : CSI</li>
<li><em>Capacity</em> : 1Pi (NAS에 배속된 용량을 정확히 기입)</li>
<li><em>Access Mode</em> : ReadWriteMany</li>
<li><em>Mount target Domain Names</em> : 위에서 확인한 NAS ID 선택</li>
</ul>
<h4 id="pvc-생성">4.3.1 PVC 생성</h4>
<p><em>ACK 콘솔 &gt; Volumes &gt; Persistent Volume Claim &gt; Create PVC</em> 선택<br>
<img src="https://user-images.githubusercontent.com/34003729/139365374-0bf5451c-0b7e-4489-b18e-ddd516120a79.png" alt="image"></p>
<ul>
<li><em>PVC Type</em> : NAS</li>
<li><em>Name</em> : imagenet-data</li>
<li><em>Allocation Mode</em> : Existing Volumes</li>
<li><em>Existing Volumes</em> : imagenet-data, 1Pi 선택</li>
<li><em>Capacity</em> : 1 Pi</li>
</ul>
<h3 id="arena-커맨드를-이용한-imagenet-데이터-학습">4.4 Arena 커맨드를 이용한 Imagenet 데이터 학습</h3>
<p>AIACC가 설치된 Kubernetes 클러스터의 GPU를 이용하기 위해 Arena 커맨드를 수행한다.</p>
<pre><code>arena submit mpi \
--name imagenet-tensorflow \
--gpus=4 \
--workers=4 \
--data=imagenet-data:/data \
--working-dir=/data \
--mounts-on-launcher=true \  
--image=cuda11-registry.cn-beijing.cr.aliyuncs.com/aiacc/cuda11:v3.0 "sh /data/tensorflow-imagenet/launch_arena.sh"
</code></pre>
<p><strong>imagenet-tensorflow</strong> 워크로드가 정상적으로 실행이 되고 있는지 확인하기 위하여 아래 커맨드를 입력한다.</p>
<pre><code>arena get imagenet-tensorflow --type mpijob
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139366804-a8b12dca-f807-4f77-9e73-6dbfd7a3bc68.png" alt="image"><br>
시간이 조금 지나면 아래같이 상태가 변경된다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/139367031-feea1418-cc75-4279-a348-291437166ff5.png" alt="image"><br>
<strong>Status</strong>가 모두 <strong>Running</strong>이고 <strong>Node</strong>에 <strong>모든 인스턴스가 붙었다면</strong> 정상적으로 트레이닝이 수행중인 것이다.</p>
<p>시간이 지나고, 아래 커맨드를 수행한다.</p>
<pre><code>arena logs imagenet-tensorflow
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139367146-017ce18c-f4be-4db0-ad26-aa1ef12e9d18.png" alt="image"><br>
Done with training 문구를 확인하였다면 정상적으로 트레이닝이 완료된 것이다.</p>
<h3 id="aiacc가-설치되지-않은-kubernetes-클러스터에서-같은-워크로드-트레이닝">4.5 AIACC가 설치되지 않은 Kubernetes 클러스터에서 같은 워크로드 트레이닝</h3>
<p>이번 단계에서는 똑같은 리소스를 가진 Kubernetes 클러스터를 구현하고 똑같은 데이터로 똑같은 training 워크로드를 수행한다. 그 결과로 <strong>얼마나 AIACC가 학습을 가속화 할 수 있는지 확인</strong>할 수 있다.</p>
<h4 id="ack-클러스터-만들기">4.5.1 ACK 클러스터 만들기</h4>
<p>위 단계에서 만든 클러스터와 같은 인스턴스/인스턴스 갯수를 사용하는 클러스터를 만든다</p>
<p><img src="https://user-images.githubusercontent.com/34003729/139368527-d7d335ae-b63f-4317-b40c-90705dcc139e.png" alt="image"></p>
<h4 id="pvpvc-만들기">4.5.2 PV/PVC 만들기</h4>
<p>위 테스트와 공평한 프로세스를 사용하기 위하여 같은 NAS 데이터를 바라보도록 같은 형태의  PV/PVC를 만든다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139370114-790dc751-b51c-4ec1-a9ad-1920aeefa34d.png" alt="image"><br>
<img src="https://user-images.githubusercontent.com/34003729/139370491-de7b1adc-f253-4dc5-822a-e87f50db6ccd.png" alt="image"></p>
<p>구현된 서버에는 AI Acceleration을 설치하지 않고, arena만을 사용하기 위해 arena plug-in을 설치해준다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139370458-9ab03a79-0d74-4175-a532-4ca557baab91.png" alt="image"></p>
<h4 id="without-aiacc-클러스터와-연결하기-위한-클라이언트-생성-및-테스트">4.5.2 without AIACC 클러스터와 연결하기 위한 클라이언트 생성 및 테스트</h4>
<p>여기서, 새로운 K8S 클러스터와 연결하기 위해 새로운 클라이언트를 셋팅한다<br>
클라이언트 셋팅 방법은 <strong>4.2.3의 가이드</strong>를 참조할 수 있다.</p>
<h4 id="테스트-수행">4.5.3 테스트 수행</h4>
<p>이번 단계에서는 아래 명령어를 이용하여 AIACC가 설치되어있지 않은 환경에서 imagenet 데이터를 학습시킨다.</p>
<pre><code>arena submit mpi \
--name imagenet-tensorflow \
--gpus=4 \
--workers=4 \
--data=imagenet-data:/data \
--working-dir=/data \
--mounts-on-launcher=true \  --image=cuda11-registry.cn-beijing.cr.aliyuncs.com/aiacc/cuda11:v3.0 "sh /data/tensorflow-imagenet/launch_arena.sh"
</code></pre>
<p>그 이후 아래 커맨드로 결과를 확인한다.</p>
<pre><code>arena logs imagenet-tensorflow
</code></pre>
<p><img src="https://user-images.githubusercontent.com/34003729/139371665-a01608d4-f49c-4170-94c3-f838869a4ac1.png" alt="image"></p>
<h2 id="결과">5. 결과</h2>
<p>위 테스트에서 사용한 두개의 클러스터의 결과를 살펴보겠다.</p>
<p><strong>With AIACC Cluster</strong><br>
<img src="https://user-images.githubusercontent.com/34003729/139372961-a605230f-4087-49d4-ae19-9a9046a1d2ab.png" alt="image"></p>
<p><strong>Without AIACC Cluster</strong><br>
<img src="https://user-images.githubusercontent.com/34003729/139372915-4b43caf1-44f6-4e94-95d5-9cf5c6fcfe94.png" alt="image"></p>
<p>여기서 보이는 수치는 1500개의 이미지를 표본으로 학습할 때 초당 학습할 수 있는 이미지의 갯수를 의미한다.</p>
<p>결과적으로, <strong>본 테스트에서 사용한 Without AIACC Cluster에 비해서, With AIACC Cluster의 퍼포먼스가 12% 이상 증가한 수치를 확인할 수 있다.</strong></p>
<p>또한 알리바바 클라우드에서 자체적으로 테스트한 아래 차트를 살펴보면 리소스/데이터가 크면 클수록 이 퍼포먼스 가속화는 더욱더 효율이 큰 것을 확인할 수 있다.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/139373943-605822c4-1f36-45b8-ab84-213ed603fb9d.png" alt="image"></p>
<h2 id="trouble-shooting">6. Trouble shooting</h2>
<h3 id="b-에서-데이터를-변환할때의-에러-상황">6.1 (4.1.5-b) 에서 데이터를 변환할때의 에러 상황</h3>
<p><em>ModuleNotFoundError : No module named “numpy”</em><br>
<img src="https://user-images.githubusercontent.com/34003729/139374273-d90f6f11-2b13-41a3-94eb-9e1d431cd2ad.png" alt="image"></p>
<p>NAS를 마운트한 Instance에 numpy가 설치되어있지 않은 것으로, 아래 명령어로 설치할 수 있다.</p>
<pre><code>Pip3 intall numpy
</code></pre>
<p><em><strong>ModuleNotFoundError : No module named "tensorflow"</strong></em><br>
<img src="https://user-images.githubusercontent.com/34003729/139375460-30d8322f-772f-41c1-b060-46886782f549.png" alt="image"></p>
<p>NAS를 마운트한 Instance에 tensorflow가 설치되어있지 않은 것으로, 아래 명령어로 설치할 수 있다.</p>
<pre><code>Pip3 install tensorflow==1.13.1
</code></pre>
<p><em><strong>Could not load dynamic library ‘libcudart.so.11.0’; dlerror: libcudart.so.11.0: cannot open shared object file: No such file or directory</strong></em></p>
<p>NAS를 마운트한 Instance에 CUDA driver가 설치되어있지 않은 것으로, 아래 명령어로 설치할 수 있다.</p>
<pre><code>wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-rhel8-11-0-local-11.0.2_450.51.05-1.x86_64.rpm
sudo rpm -i cuda-repo-rhel8-11-0-local-11.0.2_450.51.05-1.x86_64.rpm
sudo dnf clean all
sudo dnf -y module install nvidia-driver:latest-dkms
sudo dnf -y install cuda
</code></pre>
<h3 id="d-의-arena-submit을-수행할-때의-에러-상황">6.1 (4.2.3-d) 의 arena submit을 수행할 때의 에러 상황</h3>
<p>submit 커맨드를 실행하고 arena get에서 아래와 같은 Node를 참조하지 못하는 상황일 때,<br>
<img src="https://user-images.githubusercontent.com/34003729/139376109-ef6afcc6-6764-481f-98c7-881eedb08ae3.png" alt="image"></p>
<p>아래 Submit 명령어에서 지정한 GPU와 Worker의 갯수가 배포한 클러스터에 동작시킬 수 있는지 리소스를 확인한다.<br>
<img src="https://user-images.githubusercontent.com/34003729/139376325-9af0de4a-e995-416d-8fad-9056525d085b.png" alt="image"></p>
<p><em><strong>Launcher에서 mount가 안될때 (arena v0.8.6에서 발생하는 버그)</strong></em><br>
기본적으로, 이 버그는 v0.8.8에서는 수정되었다. 업그레이드된 버전에서는 <em>–mounts-on-launche=true</em> 옵션을 추가하면 문제가 해결된다.</p>
<p>v0.8.6을 그대로 사용하고자 한다면 아래 2가지 Config를 고쳐야한다.</p>
<ul>
<li>~<em>/charts/mpijob/values.yaml</em> : "mountsOnLauncher: false"를 true로 변경</li>
<li><em>~/charts/mpijob/templates/mpijob.yaml</em>: "mountsOnLauncher: false"를 true로 변경</li>
</ul>
<h2 id="마치며">7. 마치며</h2>
<p>결과에서 나온 12% 수치가 적다고 생각될 수 있지만, 실제 기업의 ML 워크로드는 짧은 시간이 아닌 길게는 몇 일 단위의 학습을 시키는 것이 보통이다.</p>
<p>이런 관점에서 보면, 이 <strong>12%의 수치는 AIACC라는 알고리즘 적용만으로 기업의 시간/인력/비용적인 리소스가 대폭 절감될 수 있음을 암시한다.</strong></p>
<p>이렇게 우리는 이 데모를 통해 최근 핫 트렌드인 ML을 가속화할 수 있는 간단한 방법에 대해서 알아보았다. 만약 대량의 데이터를 비싼 리소스에서 사용을 하고 있는 기업에 속해있다면, 충분히 AIACC를 고려해보는 것이 좋을 것이라고 생각한다.</p>
<p><em>이 테스트를 진행하는데 도움을 주신 Li Shan(<a href="mailto:shuwei.yin@alibaba-inc.com">shuwei.yin@alibaba-inc.com</a>), Frey(<a href="mailto:frey.yjf@alibaba-inc.com">frey.yjf@alibaba-inc.com</a>), 张尉东(灵丹)(<a href="mailto:lingdan.zwd@alibaba-inc.com">lingdan.zwd@alibaba-inc.com</a>) 세분에게 감사의 말씀을 전합니다.</em></p>

