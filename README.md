# Marin Erdelji

**IT Infrastructure & Solutions Architecture**

I'm an IT Infrastructure Engineer actively working on AWS solution architecture designs, cloud engineering and DevOps projects.

Focus areas:

- Designing scalable, highly available, and resilient architectures aligned with AWS Well-Architected principles  
- Cloud migration and modernization strategies (rehost, replatform, refactor)  
- Building distributed, event-driven, and microservices-based systems  
- Cost optimization and performance efficiency across cloud workloads  
- Translating business requirements into secure, reliable, and scalable technical solutions

You can find more information about me on  [LinkedIn](https://linkedin.com/in/marin-erdelji-6a7914177)

<a href="https://www.credly.com/badges/ee6120e3-34f4-4e65-a63e-ddc6bd82cef8/public_url">
<img src="https://raw.githubusercontent.com/robzwolf/cloud-cert-logos/main/2022/AWS-Solutions-Architect-Associate.png" width=15% height=15%>
</a>

<a href="https://learn.microsoft.com/api/credentials/share/en-us/Marin-7436/18FB9D0A8069C5D0?sharingId=87ADDC395A7EF8D1">
<img src="https://github.com/erdmarin/cloud_cert_logos/blob/main/microsoft_azure_az104_cert.png" width=16% height=15%>
</a>



# Projects

A Solutions architect portfolio which includes real-world AWS case studies.

##  Project 1: Highly Available Web Application

### Overview

This project demonstrates a highly available web application architecture on AWS designed to support a SaaS platform with moderate-scale traffic. The architecture focuses on high availability, scalability, and cost efficiency while maintaining operational simplicity.

### Business Scenario

The application serves a public-facing product, where unpredictable traffic spikes are common — driven by marketing campaigns, seasonal demand, or viral content. The previous single-server deployment experienced outages during peak traffic and required manual intervention to recover from infrastructure failures. The business required a re-architecture that could absorb sudden load increases, survive AZ-level failures automatically, and reduce operational load.

The business requires:
- High availability (99.95%)
- Scalability to handle traffic spikes
- Cost-conscious design
- Secure and maintainable infrastructure

 **Stakeholders**

Engineering team -   Own deployments, instance types, and application configuration  

Platform / SRE team -   Own infrastructure, scaling policies, and disaster recovery runbooks  

Finance -   Require cost predictability; auto-scaling must have defined upper limits  


  ### Problem

The current single-instance architecture creates a single point of failure and cannot handle traffic spikes effectively. Any instance or availability zone failure results in downtime, impacting user experience and revenue.

### Solution

The solution implements a multi-tier, highly available architecture across multiple availability zones using managed AWS services. It’s designed to expand and contract automatically
in response to the application’s needs, ensuring users experience consistent, responsive performance.
When users access and interact to the application using website or mobile application. Their requests
are routed through Amazon Route 53, which is a highly available and scalable Domain Name System
(DNS) web service. Amazon CloudFront, a CDN, is used to distribute static content like images,
stylesheets, and JavaScript files efficiently. This design ensures resilience, scalability, and operational efficiency while maintaining cost control.

### Highly Available Web Application Diagram:
<img src="images/Highly_Scalable_Web_App.png"> 

## Architectural Flow

User requests travel through a layered set of AWS services. The flow is deterministic, with health-check-based routing at every layer boundary.
## Inbound Request Flow Explanation

1. **DNS resolution**  
   The user's browser resolves the domain via Route 53. A latency-based routing policy returns the Regional ALB endpoint. Route 53 health checks can fail over to a secondary region if configured.

2. **Edge caching**  
   The request hits CloudFront. Static assets are served from the nearest edge location with no origin round-trip. Dynamic requests are forwarded to the internet-facing ALB.

3. **Internet-facing ALB**  
   The Application Load Balancer receives HTTPS traffic, inspects host and path-based routing rules, and forwards requests to the target group of EC2 web layer instances. Health checks deregister unhealthy instances in < 10 seconds.

4. **Web layer (public subnet, AZ A + B)**  
   EC2 instances in the Auto Scaling Group serve the web layer. Each AZ hosts a bastion host for SSH access and a NAT Gateway providing outbound internet access for private-subnet instances. The web layer forwards application logic requests to the internal ALB.

5. **Internal ALB**  
    A second Application Load Balancer sits between the web and application layers, entirely within the VPC. It distributes requests across the private-subnet EC2 application server clusters in both AZs.

6. **Application layer (private subnet, AZ A + B)**  
    EC2 server clusters handle business logic. Instances are in private subnets with no inbound internet route. Outbound traffic (software updates, third-party API calls) exits through the NAT Gateway in the same AZ to avoid cross-AZ data transfer costs.

7. **Database layer (private subnet, AZ A + B)**  
    The application tier connects to the Aurora cluster endpoint (writer) for transactional writes and to the reader endpoint for read queries. Aurora Database is deployed with a primary writer in AZ A and a standby / read replica in AZ B. Additional read replicas in each AZ provide increased read throughput under load.
