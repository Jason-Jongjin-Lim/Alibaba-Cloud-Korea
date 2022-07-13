---


---

<h1 id="alibaba-cloud-live-commerce-platform">Alibaba Cloud Live Commerce Platform</h1>
<h2 id="overview">1. Overview</h2>
<p><strong>Commerce 3.0</strong> has arrived.</p>
<p>In <strong>Commerce 1.0</strong>, large commerce companies such as Alibaba, Amazon, and eBay sold subdivided categories, respectively, and <strong>Commerce 2.0</strong> consisted of selling subdivided categories in specialized markets. Accordingly, <strong>Commerce 3.0</strong> is changing into a form in which customers can deliver indirect experiences and specifications of products through live commerce centered on content, rather than finding and purchasing products.</p>
<p>Alibaba has been operating large online shopping malls since 1999. In the past, when the purchasing experience shifted to home shopping, Alibaba Group jumped over that stage and developed mobile commerce, that is, live commerce, and provided an experience to customers. As a result, it has been able to become the most leading platform in the current live commerce market.</p>
<p>Alibaba Group’s live commerce service is built on the technology of Alibaba Cloud. As such, Alibaba Cloud’s live commerce-related services can be easily used with guaranteed stability, performance, and security.</p>
<h2 id="introduce-live-commerce-services">2. Introduce Live Commerce Services</h2>
<p>There are various services for configuring a live commerce platform in Alibaba Cloud. Among them, I will briefly introduce the services that provide the functions desired by the market.</p>
<h3 id="apsara-video-live">2.1 Apsara Video Live</h3>
<p>Apsara Video Live is a live audio/video processing platform based on Alibaba’s massively distributed real-time video processing technology. In particular, this platform has advantages such as ease of use, low latency, high concurrency processing, and smooth screen through HD.</p>
<p>The platform operates live centers in countries around the world (especially in Asia) to provide optimal performance to viewers, and provides almost all functions required for a live platform, such as screenshots, transcoding, time shifting, recording, and monitoring.</p>
<img width="803" alt="image" src="https://user-images.githubusercontent.com/34003729/178655996-95b87f33-95aa-4944-8560-684b118d311d.png">
<p>In addition, CDN connecting and GA connecting, a network acceleration solution, can be organically configured to enhance the viewer’s user experience.</p>
<p>The core of this video solution is Alibaba Cloud’s proprietary S265 transcoding technology and Narrowband HD transcoding technology with Human Vision Model applied.</p>
<p>By applying this technology, it intelligently analyzes and transforms video scenes, motions, content and textures to produce high-quality output with low bandwidth.</p>
<img width="809" alt="image" src="https://user-images.githubusercontent.com/34003729/178656048-da55b112-2983-43e2-8f16-36ac673ce46b.png">
<p>2.2 Intelligent Speech Interaction, Machine Translator</p>
<p>Major e-commerce companies have a requirement to simultaneously broadcast live commerce broadcasts not only in local, but also in countries around the world, especially in Asia.</p>
<p>There are two solutions for implementing this technique. It is a VTT (Voice to Text) technology that can convert the show host’s voice into real-time text and an automated translation technology that can translate text converted into a local language into each language in real time. These two technologies are provided by Intelligent Speech Interaction / Machine Translator on Alibaba Cloud.</p>
<p>Both of these services are provided as APIs. In other words, if there is an application for video transmission that is being used, it has the advantage of ease of use that it can be used immediately through simple OpenAPI.</p>
<p>Currently, Alibaba Cloud provides 22 translation functions, including Korean, and has the advantage of providing special languages ​​in each Asian country.</p>
<img width="692" alt="image" src="https://user-images.githubusercontent.com/34003729/178656254-7feedbc1-ce8c-4031-a8f5-08e69c8dcfd7.png">
<p>The logic of this real-time translation is very simple. First, it converts the source audio to natural text and cleans the syntax. After that, you can translate it into the target language through Machine Translator.</p>
<img width="794" alt="image" src="https://user-images.githubusercontent.com/34003729/178656352-46558009-c8da-48d7-8f5a-5324cc231823.png">
<p>Alibaba Group’s large e-commerce sites such as Tmall, Taobao, and Lazada are already using this technology to implement and broadcast live services for buyers around the world.</p>
<p>2.3 Content Moderation, DDoS Protection, WAF</p>
<p>Live commerce broadcasting can be said to be a form of B2C among B2C targeting anonymous viewers. This means that you are always open to malicious attacks.</p>
<p>If live broadcasting is interrupted due to a DDoS attack or bot attack during live broadcasting, the reliability of the transmitting platform may be severely damaged, and there will be many losses that may occur while broadcasting is interrupted.</p>
<p>In order to prepare for such a potential security risk, various security solutions of Alibaba Cloud can be applied.</p>
<p>In particular, malicious DDoS attacks can be prevented through DDoS Protection linkage, and patterned web attacks such as various bot attacks and XSS attacks can be easily defended by linking WAF.</p>
<img width="801" alt="image" src="https://user-images.githubusercontent.com/34003729/178656416-84243caa-84f1-43dc-a410-717bbb2d2381.png">
<p>In addition, we can prevent inappropriate images, videos, and texts from being transmitted live by linking Content Moderation to prevent the transmission of potentially harmful contents.</p>
<p>2.4 Global Accelerator</p>
<p>When examining each element of live broadcasting technically, the most important thing to pay attention to is network quality. This is because, no matter how 4K the video is delivered to the viewer, the network bandwidth is limited. That is, in order to transmit high-quality live broadcasting, consideration of sufficient bandwidth should be prioritized.</p>
<p>However, in this part, the requirement of global simultaneous transmission becomes a very difficult condition. A network line that crosses each border is difficult over 10G, that is, it has a network line that is difficult to transmit even 1080P let alone 4K.</p>
<p>To solve this problem, Alibaba Cloud’s Global Accelerator technology can be utilized. Global Accelerator leverages Alibaba Cloud’s internal network spread across the globe to rapidly deliver content from servers to viewers.</p>
<img width="812" alt="image" src="https://user-images.githubusercontent.com/34003729/178656511-f1d7d50f-3661-4e14-955c-a290a70d7d45.png">
<p>2.5 Vision AI, Video Tag</p>
<p>The services we have looked at so far have been the most basic functions for composing a live commerce platform, and this introduction is a service that can develop a commerce platform.</p>
<p>We can generate more sales by using live broadcasting as content. In other words, it is necessary to come up with a way to utilize the stored video.</p>
<p>Using Alibaba Cloud’s Video Tag, you can generate sales for additional products.</p>
<img width="797" alt="image" src="https://user-images.githubusercontent.com/34003729/178656581-d4e996ae-e283-4c6f-96c1-44fcdd4ee65a.png">
<p>In addition, using Vision AI, it is possible to create new business for product detail information, categorization, and personalized product provision.</p>
<img width="792" alt="image" src="https://user-images.githubusercontent.com/34003729/178656654-33b3a600-c35e-4563-9361-df2095cf7261.png">
<ol start="3">
<li>Summary</li>
</ol>
<p>So far, we have looked at the services of Alibaba Cloud to implement a live commerce platform. Even now, many customers around the world, including Korea, are using the above services to broadcast countless live broadcasts, and business is growing.</p>
<p>Currently, the live commerce market in Korea accounts for only 2% of the total commerce market. (More than 12% in China) As such, this market can be said to be a blue ocean, and the growth of the market can be viewed positively.</p>
<p>This expectation is Commerce 3.0, and we can expect business growth by preoccupying this market.</p>

