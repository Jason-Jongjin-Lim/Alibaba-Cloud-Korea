---


---

<h1 id="alibaba-cloud-gaming-architecture-for-infrastructure-whitepaper">Alibaba Cloud Gaming Architecture for Infrastructure Whitepaper</h1>
<p>This document presents a reference architecture for gaming using services of Alibaba Cloud.</p>
<h2 id="introduction-about-gaming-industry">Introduction about Gaming industry</h2>
<p>This solution presents an overview of common components and design patterns used to host game infrastructure on Alibaba Cloud Platform.</p>
<p>The game industry has evolved into a successful entertainment business over the past few decades. Also, with the proliferation of broadband Internet, a major pattern in the game industry has become online play.</p>
<p>Online play comes in several forms, including session-based multiplayer matches, massively multiplayer virtual worlds, and intertwined single-player experiences.</p>
<p>In the past, after configuring a data center for infrastructure operation in the form of a client-server model, it was necessary to purchase and manage a dedicated on-premises server, so only large studios and publishers could handle it. Extensive forecasting was also required to handle all customer requests using pre-configured hardware.</p>
<p>In today’s cloud-based computing resource environment, game planners and developers of all sizes can request and receive all necessary resources, thereby reducing excessive initial expenses and helping to reduce the issue of infrastructure not accepting when requests explode. can give This is a business change from CAPEX-based operation to OPEX-based operation, so risks can be greatly reduced.</p>
<p>In general, the process until a game is operated is as follows.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/144422294-e8a233ec-8872-475b-a497-5e9dd9488e23.png" alt="image"></p>
<p>In the past, we maintained a waterfall-type process that proceeds from planning-development to operation in a series of time flows. An agile-type process for judging the quality of a game in a business way is being operated.</p>
<p>Recently, using various types of clients (PC, Mobile, Console), users maintain online games while presenting very strict requirements and challenges for the infrastructure environment as follows.</p>
<p><strong>1. Lag, Latency</strong></p>
<p>Korean users say this when they experience delays while enjoying the game. “I got a lag.” If we interpret this “Lag” phenomenon in our IT terms, it can be seen as “Latency”.</p>
<p>The latency of the game cannot be interpreted as a network-only problem. For example, even offline single-player games that do not use a relatively heavy network environment may experience lag.</p>
<p>Some potential causes are:</p>
<ul>
<li>Defective or aging mechanical hard drives (disk latency)</li>
<li>Monitor with slow response time, video card (video latency)</li>
<li>Mismatched or misconfigured memory (RAM latency)</li>
<li>Slow CPU operation (CPU latency)</li>
<li>Unoptimized games with inefficient code (software latency)</li>
<li>Long and complex communication routes (network latency)</li>
</ul>
<p>This problem can be solved by performing cloud server hardware maintenance, management, and upgrade.</p>
<p>Cloud services provide customers with various types of infrastructure services and provide more edge servers to provide customers with shorter and more efficient network paths. In addition, the cloud provides a hardware lineup of various vendors to provide client hardware optimized for service or to provide hardware in the form that customers want.</p>
<p><strong>2. Network Performance</strong></p>
<p>In an online gaming environment, network quality is the most important factor. Basically, in order for the user to enjoy the game without trouble, the user’s brain can tolerate only a network delay that is not perceived by the user.</p>
<p>Research papers suggest that the maximum network latency for many games should not exceed 200ms.</p>
<p>It is assumed that shooting games and fighting games should have a latency of less than 100 ms because they require fast reflexes and quick action.</p>
<p>For role-playing games like World of Warcraft, latency should not exceed 500ms. These games do not require immediate trigger reaction times, such as firing bullets, but do require network quality enhancements to reliably perform tasks such as casting skills or spells and reacting to events within the game universe.</p>
<p>Asynchronous single-player gameplay, most forms of mobile gaming, allows for latencies of up to 1000ms. Users are generally more tolerant than other game types in terms of network quality because they are only interested in the local environment in changing and maintaining Status.</p>
<p>However, this is general and some people may be more sensitive to increased latency.</p>
<p><strong>3. Quality of Service</strong></p>
<p>The user always judges the quality of the service in order to experience the good service of the game. This quality of service cannot be improved by simply increasing network or hardware performance.</p>
<p>Here are some key factors that cloud gaming service providers should consider:</p>
<p><strong>- Graphical quality</strong></p>
<p>Modern games offer very good graphical interfaces. Even in a 4K environment, it requires a graphics infrastructure environment that can play smoothly without breaking pixels. To satisfy this requirement, it is necessary to provide a powerful instance node with a built-in graphics card with high performance and a robust network backbone to ensure the consistency of the video stream service.</p>
<p><strong>- Game server response time</strong></p>
<p>As I mentioned just before, the most important quality is the response time of the game server. If it is a global game service that deals with users distributed in multiple regions, it is necessary to reduce latency and add redundancy by distributing game servers all over the world. In addition, in order to prevent the degradation of service quality from malicious attacks, a logic is required to verify the behavior of the game client on the server and protect it from abuse or fraud.</p>
<p><strong>- Zero downtime</strong></p>
<p>Game users expect game downtime to approach zero. This means that the individual server instances where user sessions are stored must be protected from external influences.</p>
<p>For this, it is recommended that the game maintain player state in one game-server process. This is expressed as “Sticky Session”. This has many advantages, such as failover in the event of a server process crash and effective load balancing.</p>
<h2 id="prerequisite">Prerequisite</h2>
<p>As mentioned in the previous chapter, it is difficult to satisfy the requirements of the current game industry without the cloud.</p>
<p>Cloud service providers can use many resources by using vast infrastructure and do not place restrictions on users.</p>
<p>Also, this resource is elastic. You can use only a portion of the resource, or you can overflow it as needed. Instances configured on the cloud platform automatically scale up or down to meet your needs. You don’t have to constantly upgrade your hardware every time you need more processing power or storage.</p>
<p>Major cloud service providers usually have many servers around the world, so they are not tied to a single region.</p>
<p>In particular, the cloud provides game service players with the following advantages:</p>
<p><strong>- Network Acceleration</strong></p>
<p>Provides accelerated networks across multiple regions and accelerated content distribution for content such as textures, UI, audio, sound, and special effects</p>
<p><strong>- High Concurrence</strong></p>
<p>High clock speed CPU instances or GPU instances to allow multiple concurrent users while ensuring minimal latency</p>
<p><strong>- Multi-Level Game Security</strong></p>
<p>Ensure data security with multi-layered security (network, application, session, hardware) and secure connections</p>
<p><strong>- Dedicated Regional Databases</strong></p>
<p>Eliminates single point of failure (SPOF) by providing high-performance databases, automatic failover and disaster recovery</p>
<p>Alibaba Cloud, which has the above advantages, provides a standard architecture optimized for the game industry by using the following services.</p>
<ul>
<li><strong>ECS</strong>: A basic virtualization server that provides various types of instances such as CPU, GPU, FPGA (GPU from Alibaba Cloud), High Memory, etc.</li>
<li><strong>ACK</strong>: Kubernetes service, an orchestrator for container management</li>
<li><strong>Function Compute</strong>: A service for running workloads in a serverless environment</li>
<li><strong>SLB</strong>: Provides both L4/L7 layer load balancing as an LB service for load balancing of instance traffic</li>
<li><strong>DCDN</strong>: CDN service that can accelerate both static and dynamic content</li>
<li><strong>OSS</strong>: As a service that provides object storage, it is possible to use high-performance storage cost-effectively</li>
<li><strong>Anti-DDoS</strong>: A security service that defends against DDoS attacks</li>
<li><strong>WAF</strong>: A service that defends against application layer attacks, intelligently detects and defends attacks by embedding AI bots</li>
<li><strong>Cloud Firewall</strong>: A security service that defends against network layer attacks</li>
<li><strong>API Gateway</strong>: A service that controls and routes API communication</li>
<li><strong>NAT</strong>: A gateway service that provides a function to communicate with the public network in an environment where the network is blocked.</li>
<li><strong>Cloud Monitor</strong>: An integrated monitoring service that can monitor all services and workloads configured in Alibaba’s cloud environment.</li>
<li><strong>ACR</strong> : Image registry service for image management used in container environment</li>
<li><strong>RAM</strong>: Service for managing users, groups, and permissions used in Alibaba Cloud environment</li>
<li><strong>SLS</strong>: A service that manages application and infrastructure logs generated by each service</li>
<li><strong>Redis</strong>: A service that provides cache in object units</li>
<li><strong>SMS</strong>: A service that can send a short message to users from all over the world</li>
<li><strong>PolarDB</strong>: A DBMS engineered by Alibaba Cloud itself, providing efficient and stable database service</li>
<li><strong>RDS</strong>: DBMS of Alibaba Cloud that redesigned open source databases</li>
<li><strong>MongoDB</strong>: A service for storing NoSQL type data</li>
<li><strong>DTS</strong>: A service that helps migrate between data stores such as RDBMS, NoSQL databases, and data warehouses.</li>
<li><strong>AnalyticDB</strong>: Cloud-native data warehousing service that integrates database and big data technology</li>
<li><strong>DMS</strong>: A service that provides functions to centrally manage multiple RDBMS</li>
<li><strong>Dataworks</strong>: PaaS that controls big data services of Alibaba Cloud</li>
<li><strong>QuickBI</strong>: Cloud-native business intelligence service</li>
<li><strong>DataV</strong>: Dashboard service for big data visualization</li>
</ul>
<p>In the next chapter, we will introduce a reference architecture that allows easy planning, development, and operation of gaming services using Alibaba Cloud’s services.</p>
<h2 id="reference-architecture">Reference Architecture</h2>
<p><img src="https://user-images.githubusercontent.com/34003729/144424442-caeb5d0a-500e-49da-b680-0639447fd057.png" alt="image"></p>
<h2 id="solution">Solution</h2>
<h3 id="frontend">1. Frontend</h3>
<p>A common name for a frontend is a platform service or online service. The platform service provides an interface for essential game functions, either allowing players to join the same game server instance or containing a ‘friend list’ social graph in the game. In general, this service is often used as a client when accessing online games. This service includes the following elements:</p>
<ul>
<li>Leaderboard / Match History / Online Lobby / Chat / Inventory / Profile / Guild / Status</li>
</ul>
<p>In general, it is common for users to communicate with the backend through the frontend service.</p>
<p>However, because the front-end service can communicate with the Internet, it has the potential to be exposed to various attacks. To help solve these security issues, it must be strengthened by using security services against DDoS and various network attack patterns.</p>
<p><strong>Architecture Description</strong></p>
<p>a. I divided the VPC into development / test / operation. For a stable development-deployment process, the stability can be strengthened by dividing the process according to each development stage.</p>
<p>b. You can load balance service ingress traffic using SLB. This SLB service can perform both ALB for application load balancing and CLB for network load balancing.</p>
<p>c. Each game service is deployed as an ECS. Alibaba Cloud ECS is characterized by being very light and fast to deploy. We can achieve both stability and scalability by using instances to maintain sessions of users accessing game services.</p>
<h3 id="backend">2. Backend</h3>
<p>Backend services usually have no externally exposed network routes and IP addresses, and only provide interfaces to frontend and other backend services. For that reason, external clients cannot communicate directly with the backend service.</p>
<p>On the backend, you typically place services that connect directly with data such as game state data in a database or data warehouse and analytics events.</p>
<p>As games grow in popularity, they can leverage stateless protocols to make it easy to scale. Creating HTTP/JSON APIs for most game functions (running the game, exiting, etc.) allows you to dynamically add instances and get away with temporary network issues.</p>
<p><strong>Architecture Description</strong></p>
<p>a. In the above architecture, the instance type is not indicated. However, in most game industries, GPU instances are mainly used for graphics rendering. Alibaba Cloud offers GPU instances that perform well and are relatively inexpensive compared to other platforms. In addition, any game company that uses ML to implement functions such as NPCs and virtual user AI can use AIACC, which can accelerate ML learning by 20% or more with software logic.</p>
<p>b. Depending on the type of service, the service located in the backend may also communicate with the outside. To prepare for such a case, secure Internet communication using a NAT gateway can be implemented by placing a public subnet in the backend VPC.</p>
<p>c. To implement the stateless protocol mentioned briefly above, you can use Function Compute, a serverless service of Alibaba Cloud. To create a more flexible and scalable environment, one-time workloads such as game access, release, execution, and termination can be implemented as logic connected to DB and OSS using FC.</p>
<p>d. Among many games implemented recently, most of the modules that emphasize scalability are implemented in a container environment. In Alibaba Cloud, the ACK service provides a Kubernetes environment that can efficiently manage containers. We can efficiently configure the basis of ACK using various types of instances such as CPU, GPU, and High Memory.</p>
<p>e. We need to create an OSS bucket in our backend environment. This OSS bucket stores binary game content such as patches, levels, and inventory. OSS uses APIs to upload and download data. In addition, OSS can be used as file storage or shared storage, providing cost-effective handling of the stateful storage required by a variety of workloads.</p>
<h3 id="network-security">3. Network, Security</h3>
<p>Network and security are one of the most important factors in a cloud environment.</p>
<p>When operating in the existing on-premises environment, it was easy to maintain the network separation architecture, and it was easy to maintain the network topology and use multi-layered security.</p>
<p>However, in the current cloud environment, cloud-native networks and security must be considered by default. In particular, the following points should be considered.</p>
<ul>
<li>
<p><strong>VPC Separation</strong>: In terms of security, the Dev / Test / Prod process to be used in the front end and back end must be separated by a VPC. In addition, to implement integrated monitoring and logging, a management VPC can be configured separately to implement centralized management with each Management service.</p>
</li>
<li>
<p><strong>Account and permission management</strong>: By allocating different game projects to separate RAM accounts that are divided into VPCs, costs and metrics are automatically separated, making analysis easier. You can also grant multiple accounts and permissions per game to manage production and non-production VPCs.</p>
</li>
<li>
<p><strong>Multi-region</strong>: Distributing your game to multiple regions guarantees the lowest latency for users worldwide. For example. For example, if a service is implemented in the Philippines and Singapore regions for users in Asia, user accessibility is enhanced. Furthermore, you can see that the user experience is further improved by adding the game application for that user to the VPC in that region.</p>
</li>
</ul>
<p>In particular, in terms of security, Alibaba Cloud provides services for multi-layered security. A list of services can be found in the figure below.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/144424929-583a8cff-1e3b-4826-a0ab-0a3bf9c8f6b5.png" alt="image"></p>
<p><strong>Architecture Description</strong></p>
<p>a. We can use three services for network security.</p>
<p>First, most online game services are subject to more DDoS attacks than other industries. In order to defend against large-scale traffic attacks without affecting the service, Anti-DDoS that can handle a lot of traffic is an essential component.</p>
<p>Next, even if we prevent DDoS, we are still exposed to the risk of attack at the application layer. In particular, the OWASP Top 10 attacks are changing every year and evolving into more threatening attacks. WAFs can be configured to escape these threats. WAF not only attacks the top level of existing applications, but also has an AI function, so it can successfully defend against continuously changing attack types.</p>
<p>Finally, even with successful defense against the most daunting DDoS and application attacks, threats to the most basic network level still exist. We can configure North-South / West-East bound network firewall using Cloud Firewall, which is a basic L4 firewall.</p>
<p>b. Among the workloads configured in the backend, to configure the stateless protocol, we implemented a serverless workload using Function Compute. This Function Compute is configured to be called in the form of HTML/JSON API, so it is necessary to manage the open API.</p>
<p>We can use the API Gateway service to manage our API. API Gateway can manage version and permission for API, and can monitor all traffic.</p>
<p>c. As the graphic quality of the game improves and the content increases, the capacity for installation is becoming more and more huge. Currently, most PC games have a capacity of more than 50 GB, and users spend a lot of time to download and install them.</p>
<p>In particular, downloadable content (DLC) that can be expressed as “expansion packs” has become a major source of revenue in recent years. Users continue to expect new characters, levels, and quests for months if not years after the game is released. DLC, which can deliver this content quickly and cost-effectively, is having a major impact on the profitability of the gaming industry.</p>
<p>Game clients themselves are usually distributed through app stores on specific platforms, but updating new versions of games to deliver new levels can be cumbersome and time consuming.</p>
<p>When distributing such content to a large number of clients (game patches, extensions, or betas), it is more efficient to use DCDN than to access the story directly. DCDN is very useful because it can easily accelerate not only static binary files but also dynamic contents. We can cost-effectively deliver binary content to clients by creating an OSS bucket on the backend of this DCDN.</p>
<h3 id="databases">4. Databases</h3>
<p>The database that stores the game world state and player’s game progress data is the most important element of the game infrastructure.</p>
<p>The database should plan not only the capacity to handle the expected workload under normal circumstances, but also the workload required when the game is a huge success. It was designed and tested based on the expected number of players, but when the database suddenly crashes under a much higher load, the player game becomes unplayable and the entire business collapses. Therefore, database design in the game industry should be treated as the most important.</p>
<p>The advent of horizontally scaling applications has changed the traditional approach of application tiers and relational databases. Many new databases have become popular, avoiding the existing ACID (Atomity, Consistency, Isolation, Durability) concept and favoring lightweight instances and distributed storage. Such a NoSQL database would be suitable for instant games with simple character characteristics, items, and game structures, rather than complex relational data with a data structure.</p>
<p>In general, the biggest bottleneck for online games is database performance. A typical web-based app reads more and writes less. But the game is just the opposite. Reads and writes to the database frequently crash due to the game’s constant state changes. Considering this characteristic, the database type and form must be designed.</p>
<p><strong>Architecture Description</strong></p>
<p>a. Games such as MMORPGs with a large worldview and a large amount of data to deal with should use a relational database as the main stream. Relational databases can provide traditional forms of reliable data management.</p>
<p>Traditional relational databases focus on vertical scaling. However, it can be difficult to add schemas to a running database without downtime. We can use PolarDB and RDS from Alibaba Cloud to achieve all these stability, ease of use, and scalability. This can be a great choice when you want to keep your existing data structures but still take advantage of cloud-native.</p>
<p>b. NoSQL can provide a solution for scalable operations for write-heavy workloads. However, you need to understand the NoSQL data model, access patterns, and transaction guarantees.</p>
<p>In particular, this NoSQL is designed with horizontal scaling in mind. Resizing a cluster is usually something that can be done with no downtime, but sometimes there is some performance loss until the additional nodes are fully consolidated.</p>
<p>Alibaba Cloud’s MongoDB service is a document-oriented database. That is, data is stored in nested data structures similar to structures used in general programming.</p>
<p>MongoDB is widely used as the primary data store for games and is often used with Redis. Transient game sessions are kept in Redis, and then progress is stored in MongoDB at certain logical points (auto-save, checkpoint). Redis provides high-speed access to latency-sensitive game data, while MongoDB provides simplified persistence.</p>
<h3 id="analysis">5. Analysis</h3>
<p>Analytics has evolved into an important component of today’s games. Both online services and game clients can store analytics events in a centralized database. Then, anyone from programmers and designers to business intelligence analysts and service representatives can query these events. As the complexity of the analytics data collected increases, it is necessary to maintain an architecture that allows quick and easy querying of these events.</p>
<p>Alibaba Cloud provides analysis, management, and monitoring using each service data in the following topology.</p>
<p><strong>Architecture Description</strong></p>
<p><img src="https://user-images.githubusercontent.com/34003729/144425244-ffa2aa91-564e-4036-ab0f-0ccf804e36cc.png" alt="image"></p>
<p>a. Data stored in PolarDB and RDS can be quickly migrated to AnalyticDB Data Warehouse as DTS.</p>
<p>b. Log data can be stored in OSS as Log Service of Alibaba Cloud. The stored data can use the DLA service for ETL and data analysis. You can take advantage of organizing and maintaining this data into a data lake.</p>
<p>c. Data lake and DB migration data can be analyzed in a low latency and high concurrency environment with an AnalyticDB service suitable for each type.</p>
<p>d. AnalyticDB, where data is stored, can be efficiently managed using DMS and DataWorks. In addition, using QuickBI and DataV, you can configure a dashboard for data or perform control operation.</p>
<h3 id="management-security-cost-optimization">6. Management, Security, Cost Optimization</h3>
<p>The entire architecture configured using the Alibaba cloud service configured above is managed and operated in the cloud environment. We need to take full advantage of this cloud in terms of operations.</p>
<p><strong>Architecture Description</strong></p>
<p>a. In the past, monitoring and logging of our on-premises environment had to simply provide a function so that the operator can intuitively see the fixed environment. However, the number of instances in the cloud environment continues to increase and decrease, and there may be no externally exposed IP, and even the server may be created according to an event and then disappear immediately. For cloud monitoring and logging in this environment, Dynamic Configuration became the most important function.</p>
<p>Alibaba Cloud is a Cloud Monitor service that can perform infrastructure and application monitoring for the entire workload. It can also send alarms for operators to react immediately when a problem arises.</p>
<p>In addition, by using the SLS service, logs for all services can be collected, analyzed, and monitored.</p>
<p>b. If you look at the backend area, most of the core game functions consist of containers. Containers must be created from container images, and companies must manage these important images.</p>
<p>Alibaba Cloud provides ACR service for container image management. Operators can use ACR to manage container image versions and control access rights. In addition, the security of the base image can be strengthened by using the image vulnerability scanning function.</p>
<p>c. Looking at the environment configured so far, it is necessary to manage a multi-layered access right to access a VPC configured differently for each Dev/Test/Prod and various services.</p>
<p>Alibaba Cloud RAM Service provides account access rights, role, and group management, and performs access control in connection between each API and service. An operator who operates a game system using this service can efficiently manage access to services and connections between services by using RAM.</p>
<p>d. If we were to pick the most important topic in game service, we could of course talk about performance.</p>
<p>If you check all the network steps from when the player clicks on his character’s information view to check the data, you can see that database access takes the most time.</p>
<p>We can use Alibaba Cloud’s Redis service to accelerate the data reading process. Redis provides basic data types such as counters, lists, sets, and hashes that are accessed using high-speed, text-based protocols. These unique data types make Redis an ideal choice for rankings, lists, player counts, stats, inventory, and simple data.</p>
<p>e. When providing online game services, there are cases where it is necessary to send text messages such as OTP authentication for registration and mobile push for events to players around the world. Of course, as in the past, SMS can be sent by contract with a telecommunication service provider that provides text messages in each country, but this method requires a complicated process.</p>
<p>Alibaba Cloud uses SMS service to provide a function to easily send SMS using communication channels already established with telecommunication operators in various countries around the world.</p>
<p>f. With Alibaba Cloud, you no longer have to waste resources on building expensive infrastructure, such as purchasing hardware and software licenses. Alibaba Cloud translates initial costs into lower operating costs and allows you to pay only for what you use resources.</p>
<p>When purchasing an instance, an operator can save money by purchasing Reserved Instance or Spot Instance, and can reduce the usage fee for each service by using a Saving Plan.</p>
<h2 id="how-to-acquire-the-advantage-from-alibaba-cloud">How to acquire the advantage from Alibaba Cloud</h2>
<p>So far, we have introduced the Reference Architecture that can be applied to the game industry using Alibaba Cloud’s services and solutions.</p>
<p>Alibaba Cloud provides a stable and fast infrastructure platform for services in many countries around the world. In addition, it is contributing a lot to the development of the game industry by using the following advantages.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/144425804-94fc8910-cdbe-43fd-b112-575656f532df.png" alt="image"></p>
<p><strong>If the person reading this is in charge of developing and operating a cloud-native game service, I hope this Whitepaper will help to compose an efficient architecture.</strong></p>
<p><em>If you have any questions about this architecture or would like consulting, please send an email to <a href="mailto:cloudkorea@list.alibaba-inc.com">cloudkorea@list.alibaba-inc.com</a>.</em></p>

