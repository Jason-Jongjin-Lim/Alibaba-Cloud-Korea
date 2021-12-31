---


---

<h1 id="alibaba-cloud-isv-on-cloud-architecture">Alibaba Cloud ISV on Cloud Architecture</h1>
<h2 id="isv-introduction-saas">ISV Introduction (SaaS)</h2>
<p>The term ISV ​​refers to expanding sales channels by moving a solution configured on-premises to the cloud. In other words, ISV on Cloud is equivalent to creating SaaS.</p>
<p>Software as a Service (SaaS) is licensed and centrally hosted on a subscription basis, rather than the traditional all-in-one licensing method of selling solutions. This form can be a delivery model for many business applications, including ERP and messaging software, management software, virtualization, and more.</p>
<p>Early Internet-based software had features similar to on-premises applications, unlike SaaS applications. Because the software was originally built as a single-tenant application, it had limited data sharing capabilities. However, SaaS applications contain a number of features that make them competitive compared to on-premises applications, and they can all be configured as single-instance, multi-tenant architectures.</p>
<p>SaaS providers centrally host applications and data. Patches, extensions, and upgrades are transparently operated in the application environment, and business users do not feel any inconvenience when using them. This SaaS provider also provides an OpenAPI to allow business users to easily extend its functionality. Business users can customize SaaS functions to fit their business form through OpenAPI.</p>
<p><strong>Advantages of SaaS Architecture</strong></p>
<p>• SaaS vendor manages all backend infrastructure for you, so you don’t have to worry about operations.</p>
<p>• SaaS is designed to store data on remote servers, so there is no data loss due to hardware failure in the local data center. Automatic data backup, DR is built into the SaaS architecture.</p>
<p>• SaaS vendors release fully tested versions, so you don’t have to worry about bugs, complex deployment procedures, or issues that cause service downtime.</p>
<p>• SaaS architecture provides a flexible platform to scale computing resources on-demand. You don’t have to buy more powerful hardware to serve more customers. SaaS architecture components are built with scalability in mind.</p>
<p>• Most SaaS solutions provide compliance natively for the industry involved. This eliminates the need for additional resources to ensure compliance.</p>
<p>• SaaS eliminates the need to build complex and expensive technology and tool stacks to support short-term projects. You can save time and money by using hosted applications.</p>
<h2 id="prerequisite">Prerequisite</h2>
<p>First, it can be said that migrating existing on-premises applications to SaaS is the evolution of applications to Cloud Native. The following considerations are necessary to properly use cloud services with many advantages.</p>
<h3 id="microservices-vs.-monolithic-architecture">1. microservices vs. monolithic architecture</h3>
<p>Still, many companies use a monolithic architecture approach. Even monolithic applications can be built, patched, or changed without affecting the overall application by tiering them to a 3-tier, etc.</p>
<p>Unless you plan to create a large production environment, we recommend a monolithic approach that is easy to develop/manage. However, this architecture is difficult to change, so if scaling or a lot of change is expected, a microservices architecture should be chosen.</p>
<p>Microservices architecture breaks down services into units to create independent and isolated processes and architectures, each service can be independently developed, deployed, tested, and patched.</p>
<p>You can also focus each microservice on a single business offering. Take, for example, streaming services, the most successful microservices architecture. Netflix uses various microservices for billing, analyzing your watch history for movie recommendations, identifying devices to optimize your viewing experience, and adding copyright notices to all your files. Netflix has even made it open source how they develop and operate, making it easy for other companies to run microservices.</p>
<p>Microservices allow multiple teams to manage independent services, each coded in different languages, and deployed on different infrastructures. For this reason, a microservices architecture allows for scalability, CI/CD operations, and troubleshooting without having to disrupt the entire service to change or troubleshoot the entire application.</p>
<h3 id="self-service-and-customizing">2. Self service and customizing</h3>
<p>Enterprise users running SaaS solutions can manage these applications themselves and do not need to hire experts. It should also allow operators to customize the SaaS solution according to their own needs without writing any code.</p>
<p>SaaS provides an easy-to-use API in its architecture to give users more flexibility in customizing the platform. Also, you must provide a manual to use this API. You can get more value from your SaaS architecture by allowing you to integrate 3rd party tools that you already use or want to use.</p>
<h3 id="multi-tenant-implementation">3. Multi-tenant implementation</h3>
<p>Multi-tenant architecture allows efficient use of resources when multiple users run the application.</p>
<p>• Using an application with multiple databases ensures that every user entering the environment accesses the other database at the same time. As a result, applications can scale faster, provide more resources to users at the same time, and become more responsive. However, this approach is expensive because it requires allocating more resources.</p>
<p>• Using an application with one database allows all users to access one database until it is filled before redirecting to a new database. Although this method is fast and inexpensive to deploy, it can cause problems with SPOF and performance degradation due to single connection section.</p>
<p>Assume you have heavy users whose workloads take up most of their resources. These users can degrade the user experience of other tenants in a multi-tenant environment.</p>
<p>Monitoring and logging systems must be configured so that resources can be controlled in the SaaS environment before such a bad situation occurs.</p>
<h3 id="data-security">4. Data Security</h3>
<p>Most enterprises choose an on-premises architecture because they are concerned about the security of their data. Data security is one of the most expensive areas for businesses to invest in with a number of recent incidents. We must provide a tight service to protect this data.</p>
<p>Making role-based access control (RBAC) a key component of your SaaS architecture can help increase data security. RBAC can be used as a feature to prevent other users from accessing and changing data that is not directly related to their role in the organization.</p>
<h3 id="saas-configuration-compliant-with-each-countrys-regulations">5. SaaS configuration compliant with each country’s regulations</h3>
<p>If you’re delivering applications for a specific industry, you need to build SaaS applications with out-of-the-box compliance. Compliance varies by industry, but policies such as the General Data Protection Regulation (GDPR) apply across the board.</p>
<p>When we configure our applications, we must choose an infrastructure that considers compliance, including GDPR.</p>
<h3 id="start-developing-with-a-saas-architecture-that-considers-scalability">6. Start developing with a SaaS architecture that considers scalability</h3>
<p>If business applications are innovated with SaaS, our business channels can become closer to customer contact points compared to the existing single offline channels. We need to be able to scale the environment of SaaS as applications grow in popularity.</p>
<p>This requires designing a SaaS architecture to easily autoscale and handle increasing loads without compromising performance. You can achieve this by ensuring that your SaaS architecture supports seamless horizontal and vertical scaling.</p>
<h3 id="guaranteed-minimal-downtime">7. Guaranteed minimal downtime</h3>
<p>We need to configure a SaaS solution with high availability. SaaS users have very little tolerance for service downtime. We need to know that prolonged service outages reduce customer satisfaction, resulting in loss of customers, business and competitive advantage.</p>
<p>To achieve this high availability, we need to have a multiplexed configuration all at the network, instance, and database level.</p>
<h3 id="monitoring-the-cost-of-saas-applications">8. Monitoring the cost of SaaS applications</h3>
<p>Transforming applications to SaaS means changing our business model to subscription or pay as you go.</p>
<p>We need to drill down into monitoring and logging systems for our resources to understand how much our customers are using our applications. In addition, if you anticipate the time when customer access will increase rapidly and prepare for expansion in advance, you can enhance the quality of service.</p>
<h3 id="enhanced-network-quality-between-regions-and-centers">9. Enhanced network quality between regions and centers</h3>
<p>Since applications configured in the existing on-premises environment communicate data through a private network, there is no need to worry about network quality.</p>
<p>However, when the service is configured in the cloud environment, there are many factors that will increase network latency, such as communication with the main server, communication with the DR server, and connection of overseas users.</p>
<p>We must consider accelerating network communication in order to improve the quality of service.</p>
<p>Alibaba Cloud provides various services to satisfy the above 9 SaaS Architecture requirements.</p>
<ul>
<li>
<p><strong>Function Compute</strong> : Services for running workloads in a serverless environment</p>
</li>
<li>
<p><strong>ACK</strong> : Kubernetes service, an orchestrator for managing containers</p>
</li>
<li>
<p><strong>ECS</strong> : Provides various types of instances such as CPU, GPU, FPGA (Alibaba Cloud GPU), and High Memory as a basic virtualization server</p>
</li>
<li>
<p><strong>NAT</strong> : A gateway service that provides a function to communicate with the public network in an environment where the network is blocked.</p>
</li>
<li>
<p><strong>SLB</strong> : Provides both L4/L7 layer load balancing as an LB service for load balancing of instance traffic</p>
</li>
<li>
<p><strong>PolarDB</strong> : Alibaba Cloud provides efficient and stable database service with its own engineered DBMS</p>
</li>
<li>
<p><strong>RDS</strong> : Alibaba Cloud’s DBMS redesigned open source databases</p>
</li>
<li>
<p><strong>OSS</strong> : As a service that provides Object Storage, you can use high-performance storage cost-effectively</p>
</li>
<li>
<p><strong>NAS</strong> : A network-based storage service, primarily used to configure shared file systems.</p>
</li>
<li>
<p><strong>Global Accelerator</strong> : Network communication acceleration solutions to eliminate network latency issues across borders, especially in mainland China and beyond.</p>
</li>
<li>
<p><strong>API Gateway</strong> : Services that control and route API communications</p>
</li>
<li>
<p><strong>VPN</strong> : A protected network service that provides secure access to an isolated network environment (VPC)</p>
</li>
<li>
<p><strong>DCDN</strong> : A CDN service that can accelerate both static and dynamic content</p>
</li>
<li>
<p><strong>Express Connect</strong> : A network service that provides a fast and reliable dedicated line between a local data center and a VPC.</p>
</li>
<li>
<p><strong>CEN</strong> : A network service that can be connected as a private network between VPCs in Alibaba Cloud</p>
</li>
<li>
<p><strong>Anti-DDoS</strong> : Security service to defend against DDoS attacks</p>
</li>
<li>
<p><strong>WAF</strong> : It is a service that protects against application layer attacks and intelligently detects and defends against attacks by embedding AI bots.</p>
</li>
<li>
<p><strong>Cloud Firewall</strong> : Security service that defends against network layer attacks</p>
</li>
<li>
<p><strong>Security Center</strong> : A centralized security management system that dynamically analyzes and protects security threats to cloud resources and servers</p>
</li>
<li>
<p><strong>SSL Certificates</strong> : A service that manages certificates for SSL communication</p>
</li>
<li>
<p><strong>RAM</strong> : Security service for RBAC-based user and role management</p>
</li>
<li>
<p><strong>Cloud Monitor</strong> : Integrated monitoring service that can monitor all services and workloads configured in Alibaba cloud environment</p>
</li>
<li>
<p><strong>SLS</strong> : A service that manages application and infrastructure logs generated by each cloud service</p>
</li>
<li>
<p><strong>ARMS</strong> : APM service that controls application performance</p>
</li>
</ul>
<h2 id="reference-architecture">Reference Architecture</h2>
<p>The Alibaba cloud services described in the section above enable you to transform your on-premises applications to SaaS cost-effectively, securely, and easily. An example architecture for it is shown below.</p>
<p><img src="https://user-images.githubusercontent.com/34003729/147716488-2e13c1d7-a9e0-4e69-866f-a1aca2583f31.png" alt="image"></p>
<h2 id="solutions">Solutions</h2>
<p>The service logic expressed in the above architecture is divided into a total of 5, and you can refer to the explanation of why the service is arranged in this form below.</p>
<h3 id="isv-application">1. ISV Application</h3>
<p>First, when migrating an on-premises application to the cloud, the first thing to consider is the migration of the application itself. In the cloud environment, we already have everything of an environment where a company loses, and we can properly select a service according to the type of service.</p>
<p>a. Public Subnet (NAT, SLB)</p>
<p>Most enterprise environments are configured in closed-network environments. Subnets can be divided so that a closed network can be maintained even in a cloud environment. In this environment, you need a module that requires internet connection or a service that acts as an ingress router that allows customers to connect to the server.</p>
<p>In such a limited network environment, we can configure an architecture that can satisfy both scalability and security by using NAT and SLB services that help connect to the public network.</p>
<p>b. Function Compute</p>
<p>Sometimes, when we configure microservices, we run services (login, logout, etc.) that do not need to maintain the server 24/7 among modules. In such a case, you can consider implementing a serverless type of service.</p>
<p>We can easily implement Serverless by using FC service. This service can increase customer scalability and cost effectiveness.</p>
<p>c. ACK, ASK</p>
<p>Among the many applications implemented recently, most of the modules that value scalability are implemented as a container environment. In Alibaba Cloud, the ACK service provides a Kubernetes environment that can efficiently manage containers. We can efficiently configure the basis of ACK using various types of instances such as CPU, GPU, and High Memory.</p>
<p>d. ECS</p>
<p>If the application has the most suitable architecture for a legacy environment that is not considered a serverless or container environment, you can consider moving to the instance environment as it is.</p>
<p>Alibaba Cloud provides the fastest and highest SLA virtual machine in the ECS instance environment to perform cost-effective instance migration.</p>
<h3 id="data-area">2. Data Area</h3>
<p>The most difficult part of moving legacy applications to the cloud is data migration. Alibaba Cloud provides a variety of DBMS and storage services for each data type, so migration can be performed more easily without major data changes.</p>
<p>a. Polar DB / RDS</p>
<p>Relational database is the most common form of database for managing data of service applications provided by enterprises. Even the same relational database has a different data structure depending on the type of vendor/open source project. For this reason, the migration of databases is considered the most difficult migration process.</p>
<p>Alibaba Cloud provides a variety of commonly used RDBMS-based services. In addition, we provide a migration service that helps you perform data migration easier and faster.</p>
<p>Using these services, we can perform the data transfer, which is considered the most difficult step, without issues.</p>
<p>b. OSS / NAS</p>
<p>Enterprise SaaS applications need to consider the process of efficiently storing data. In addition, depending on the properties in which data is stored, a storage configuration suitable for the form such as a data lake or shared storage is required.</p>
<p>In this case, we can use OSS and NAS as representative of Alibaba Cloud’s various storage services. OSS is a service that provides object storage, allowing you to cost-effectively store and utilize various data such as object storage and file system. In addition, if you need shared storage, you can use a NAS with network and storage optimization to store and utilize data faster.</p>
<h3 id="network">3. Network</h3>
<p>We only need to consider the North-South bound network when configuring our application in the legacy environment. However, when an application moves to a cloud environment, all internal communication (East-West bound) must be taken into account.</p>
<p>We can solve network issues that occur in various types of channels by using the following network services of Alibaba Cloud.</p>
<p>a. DCDN</p>
<p>If the service provided mainly provides large-capacity content (photos, pictures, and videos), network delays for customers to use the content will be fatal to the business. The most needed service in these cases is the CDN.</p>
<p>Alibaba Cloud provides CDN service cost-effectively and has the advantage of DCDN, which can handle both static and dynamic content in particular. We also have the largest number of CDN PoPs in Asia, allowing us to deliver large content to customers faster.</p>
<p>b. Global Accelerator</p>
<p>Cross-border data communication must be considered when customers with overseas offices use our SaaS. In particular, public networks between China and Korea can incur a lot of latency and jitter. In this case, using Alibaba Cloud’s GA service, data communication can be performed faster and more reliably.</p>
<p>c. API Gateway</p>
<p>We talked about the need to provide an API for customer-specific customization of SaaS in the steps above. Management is required for this open API.</p>
<p>We can use the API Gateway service to manage our APIs. API Gateway can manage versions and permissions for APIs, and can monitor all traffic.</p>
<p>d. VPN</p>
<p>If an enterprise wants to access a secure protected environment (VPC), it is necessary to use a protected network rather than a public network. In addition, the isolated private network must decide whether to access or not according to the role of the operator.</p>
<p>We can connect these cases with a VPN service. A VPN service allows you to connect to a more secure VPC, increasing the security of your operations.</p>
<p>e. Express Connect</p>
<p>In some ISV applications, only the clients are deployed to the cloud and the main servers are kept in the local IDC. From this point of view, reliable and fast communication is essential for client-server data communication.</p>
<p>We can use the Express Connect service in these cases. If you use EC, you can construct a stable and fast network using the dedicated line provided by Alibaba Cloud without having to build an expensive dedicated line.</p>
<p>f. CEN</p>
<p>There are various ways to configure DR in the enterprise, such as hot, warm, and cold forms. In particular, DR in the cloud environment consists of logic that synchronizes data and service sinks by connecting the production VPC and the DR VPC through a private network.</p>
<p>You can use the CEN service to configure a network between different VPCs as a private network.</p>
<h3 id="security">4. Security</h3>
<p>One of the easiest benefits of moving our applications to the cloud is the integration of security-related services. In fact, in an on-premises environment, security is the area where we spend the most money. By replacing this large-scale security with cloud services, the initial investment can be significantly reduced.</p>
<p>a. Anti-DDoS, WAF, Cloud Firewall</p>
<p>When our applications are moved to the cloud, the first security consideration to consider is network-related security enhancements.</p>
<p>Anti-DDoS, Alibaba Cloud’s network security service, effectively defends against DDoS attacks from around the world. In addition, security attacks at the application layer can be defended using bots in WAF, and attacks at the network layer can be defended using Cloud Firewall. We can use these 3 services to keep our SaaS safe.</p>
<p>b. Security Center</p>
<p>Security Center is a centralized security management system that dynamically identifies and analyzes security threats and generates alerts when threats are detected. Security Center provides several features to ensure the security of cloud resources and servers in your data center. Features include ransomware protection, antivirus, web tamper protection, container image scanning and compliance scanning. This enables you to automate security operations, response and threat tracking, and meet compliance requirements.</p>
<p>c. SSL Certification</p>
<p>If it is a service that provides web-based SaaS, it is necessary to configure the HTTPS protocol. HTTPS protocol must be managed by SSL Certificate. You can use Alibaba Cloud’s SSL Certification service to manage these SSL certificates.</p>
<p>d. RAM</p>
<p>RAM allows you to create and manage accounts and grant different privileges to a single account or group. In this way, you can grant different identities access to different Alibaba Cloud resources. This service allows us to perform RBAC-based user account and service management.</p>
<h2 id="conclusion">conclusion</h2>
<p>So far, we have looked at the services and architecture of Alibaba Cloud that can help you in the process of creating a legacy application ISV on Cloud, i.e. configuring SaaS.</p>
<p>These services enable us to transform our business cost-effectively, quickly and safely when migrating our applications to the cloud.</p>
<p>If you have any questions about this architecture or would like consulting, please send an e-mail to <a href="mailto:cloudkorea@list.alibaba-inc.com">cloudkorea@list.alibaba-inc.com</a>.</p>

