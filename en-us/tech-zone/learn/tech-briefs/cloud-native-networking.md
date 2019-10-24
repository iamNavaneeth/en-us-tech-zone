---
layout: doc
---
# Microservices architectures and Cloud Native Networking

## Contributors

**Author:** [Florin Lazurca](https://twitter.com/FlorinLazurca)

## Monolithic to Microservice

Cloud Native is about developing and deploying web apps with agility and realizing operational efficiency. It’s the modern way of architecting apps that includes microservices and containers; it's also the modern way of constructing cross-disciplinary platform teams. Companies want to move fast; they need to create software and deploy it quickly. However, many organizations are siloed - app developers vs. the networking team vs. the security team - causing bottlenecks. Also, networks are complex and change requests are manual - moving at a slow pace.

To address organizational gaps, architects and Digital Officers have been empowered to come up with a fresh approach. An approach that is more holistic. Moreover, individual roles and responsibilities are being adjusted to insert a platform team to work across the business. The team combines multiple disparate product, support, and operational pipelines to create a platform that runs consistently. One that runs consistently on premises and the cloud. One that is easy to use and has the agility to deploy apps quickly. And one that has the operational efficiency to update, scale, and manage the whole system for common use across multiple stakeholders. Lastly the team is charged with platform governance, ensuring that platform meets policies around security, access, and tenancy.

On the technical side, the architecture of web applications and their delivery has changed accordingly. To facilitate agility and operational efficiency, the traditional three tier architecture of presentation, logic, and database has been transformed. The layers become a service mesh comprising single function apps or microservices that decouple critical app functions from each other. This decoupling facilitates continuous integration and development (CI/CD) in which development, testing, and rollout/rollback are done independently at a microservice level. However, creating a mesh opens up new East-West traffic patterns since microservices must communicate among each other.

![Descriptive text](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_east-west2.png)

This East-West traffic benefits from the same advanced traffic management capabilities provided by Application Delivery Controllers (ADC) to North-South traffic, such as security, performance, scalability, and observability.

In a Kubernetes architecture, there is a key component that manages both ingress and inter-microservice traffic. Ingress controllers provide a mechanism to send HTTP traffic to a microservice hosted in a Kubernetes cluster. This traffic directing is done by implementing ingress rules that specify how the ingress traffic is routed to the target microservice from outside the cluster. Ingress controllers watch the Kubernetes API server for updates to the ingress resource and reconfigure the ingress load balancer accordingly. Any high scale production application has several security and traffic routing policies for incoming traffic.

When applications are deployed on Kubernetes, there is no elegant way to apply these policies due to limitations of kube-proxy, the native Kubernetes Ingress Controller. Layer 7 based policies direct the client requests to the microservices that are best suited to handle them. The result is reduced overhead for processing the client requests on the microservices.
Detailed differences between Layer 4 and Layer 7 ingress controllers are listed below.

|                      | Layer 4 | Layer 7 |
|----------------------|---------|---------|
| Load Balancing       | Basic load balancing based on IP address and port only | Advanced load balancing based on URL – images, text, video and on client information – browser, OS, device language           |
|                      | HTTP/S blind      | Takes advantage of the HTTP/S packet info |
|                      | No content (payload rewrite & switching                       | Supports content rewriting: Apps with hard-coded URLs, mergers & acquisitions, publishing internal URLs, misconfigured apps, respond to malicious traffic |
|                      | Inability to change anything on the wire                       | Can parse the payload and apply changes. Allows making smarter content optimization and security decisions like app firewalling along with doing proxy   |
| Session Persistence  | Limited - only based on client IP address                | Advanced session persistence for better user experience Can use cookies – identify users to provide persistent experience Better experience for stateful applications |
| Resource Monitoring  | Health checking limited to Ping and TCP handshake only        | Advanced customizable health checks App-level visibility for better observability and load balancing decisions Enables circuit-breaking capabilities |
| App Security         | Limited due to just IP address and port visibility Lacks deep packet inspection | Advanced protection due to deep packet inspection Examples: web application firewall, L7 DoS protection, application stack vulnerabilities based on signature analysis, anomaly detection|

The limitations of Layer 4 can create challenges for platform teams with kube-proxy when:

-  Migrating legacy apps due to limited TCP/UDP support and no TCP-SSL support. These apps rely on advanced ADC functions not present in native proxies.
-  Exposing critical applications based on Layer 7 protocols such as DNS, FTP, or LDAP. Layer 7 traffic requires an intelligent load balancer act as a proxy.
-  Protecting apps without advanced functions such as Rewrite and Responder (HTTP to HTTPS rewrite, dropping unwanted connections, or responding with custom messages)
-  Operationalizing ingress load balancers such as managing SSL updates, versions
-  Relying on the open source community to plug security gaps - no commercial support
-  Troubleshooting and finding bottlenecks without rich per app metrics and logs

## Citrix Differentiator

Citrix offers a supported, enterprise grade ingress controller with 20 years of production expertise. Citrix Application Delivery Controller or ADC is available as a container-based application delivery controller that works along with Citrix Application Delivery Management (ADM). ADM provides a single pane of glass to monitor and improve application performance. CPX is the same code base as the popular ADC with the benefit of being packaged for microservices. The continuity in software code and management allows the same configuration to be deployed across the entire infrastructure.

When scaling up or deploying new services, CPX recognizes the changes and auto-adjusts the load balancing accordingly. If one microservice becomes slow it can reroute traffic to a faster microservice. The CPX interfaces with Kubernetes APIs, providing real-time configuration of a Kubernetes cluster. ADC and CPX apply advanced Layer 7 policies to manage and secure both high-scale North-South and East-West traffic while enabling troubleshooting and visibility into microservices performance problems using the ADM service graph.

To apply complex Layer 7 security and routing policies, Citrix offers a developer friendly solution using Kubernetes Custom Resource Definitions (CRD). For example, the Rewrite and Responder CRD is designed to expose policies available from Citrix ADCs with an extensive audit log. Using these functionalities, you can rewrite the header and payload of ingress and egress HTTP traffic. You can also respond to HTTP traffic on behalf of a microservice.

Also, application developers can closely monitor the health of TCP/UDP based apps through rich monitors. The ECV (extended content validation) monitors help in checking whether the application is returning expected content. The following chart summarizes the benefits that CPX with Layer 7 policy capabilities provides over those proxies limited to Layer 4.

|                               | Native Proxy | Citrix ADC CPX |
|-------------------------------|---------|---------|
| Legacy App Support            | TCP ingress supported with ConfigMaps - not all TCP LB functions available                                      | TCP LB functions available through smart annotations where key value pairs can be entered to map to direct NetScaler configurations|
|                               | No TCP TLS                            | TCP-TLS supported as are TCP ECV monitors |
| Rewrite/Responder policies    | Standard rewrite and responder policies available through annotations | Extensive Rewrite/Responder policies deployed as APIs to developer through Custom Resource Definitions (CRD)  |
| Day 0 to Day N operations     | Commercial support not available. Security patches, or TLS capability enhancements through community               | Fully supported product with security patches, new capabilities, and feature enhancements through Citrix                  |
| Troubleshooting microservices | Service graph through cloud-based analytics under development| Service graphs with Web Insight data identify errors and latencies |

## Proxy Architectures

The most critical decision to be made as organizations advance towards microservices based applications, is choosing the right proxy architecture for both North-South and East-West traffic. The right architecture for a microservices and Kubernetes-based application delivery selects a balance between ease of implementation and the greatest benefits. When considering the best architecture for an existing or new business-critical application, architects must consider:

-  Each stakeholder has unique needs and evaluation criteria - developers, platform team, networking team, DevOps, SecOps, Site Reliability Engineers, and the app owner
-  Load balancing and traffic control for North-South and East-West (inter-microservices) traffic with as much visibility and security
-  The tradeoff between the benefits and complexity of each architecture
-  The right fit for the skill sets of the team
-  The rapid pace at which technology and open source innovation are evolving
  
The unique needs of the various stakeholders shape the best choice of architectures. It's a balance between benefit and complexity. At the center is the Platform team which is the connective tissue between multiple stakeholders. Each team’s main responsibilities are listed in the following table.

| Team       |  Responsibilities                                                                                  |
|------------|----------------------------------------------------------------------------------------------------|
| Platform   | Platform governance, Operation efficiency, Developer Agility                                       |
| DevOps     | Faster release & development cycles, CI/CD & automation, Canary & progressive rollout              |
| Developers | User experience, Troubleshooting, Microservice discovery, and routing                               |
| SRE        | Application availability, Observability, Incident Response, Postmortems                            |
| NetOps     | Policy & compliance, manage, control & monitor network, resources & capacity planning (visibility) |
| DevSecOps  | Application & infrastructure security, Container security & API gateways, Automation               |

Plotting four common architectures shows the relationship between benefits and complexity. On the lower left corner, you see Two-tier ingress which is the simplest to deploy. It and takes the North-South load balancing and splits it into a two-tier deployment. A variant of that is the Unified Ingress which combines the Two-tier ingress into one tier. The most advanced and feature rich architecture which has emerged as a leading architecture is the service mesh. A less complex version of the Service Mesh is known as Service Mesh Lite.

![Architecture Complexity Benefit Spectrum](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_plot.png)

Citrix developed a seven-attribute framework to evaluate the four architectures. The attributes are based on the unique needs of the six stakeholders. and provides an insight into their benefit versus complexity tradeoff. Each attribute is described below, and each architecture is measured against these requirements for both North-South and East-West traffic.

| Attribute                    | Rating                                                                                                            |
|------------------------------|-------------------------------------------------------------------------------------------------------------------|
| App Security                      | How effectively are communications and traffic being secured and segmented for both North-South and East-West |
| Observability                     | How effectively and to what detail can traffic be logged and visible. What telemetry is available?          |
| Continuous Deployment             | Advanced traffic control technique like progressive and canary rollouts, automated, analysis, green and blue deployment model, and faster roll backs |
| Scale Performance                 | Scale out features such as with clustering, IPVS, IP table |
| Open Source Tool Support          | To what level different architectures enable open source tool integration |
| Istio: Unified Control Plane      | How each architecture fair with Istio unified control plane integration |
| IT Skill Set Required             | What kind of IT skills set are required for each architecture? |

### Two-tier Ingress

The Two-tier Ingress architecture is the quickest and simplest to move into production for both the platform and network teams. The architecture is a standard deployment model for both cloud native novices and experts. It is followed widely irrespective of the platform, whether it's Google Cloud, Amazon Web Services, Azure, or an on-premises deployment.

![Two-tier Ingress Architecture](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_2-tier.png)

In a Two-tier architecture, the ADC is split into two tiers for North-South traffic, each doing a different function. The networking team manages the Tier 1 Citrix ADC VPX/MPX outside of the Kubernetes cluster. This ADC is responsible for all security policies like web application firewall, TLS, and Layer 4 load balancing of North-South traffic. The same ADC can be used to provide Layer 4-7 load balancing of traditional monolithic applications during their transition to microservices. Here configuration changes are more carefully and slowly implemented. The Tier 1 VPX/MPX load balances the Tier 2 CPX inside the Kubernetes cluster. The Platform team manages thee Tier 2 CPX. Developers write their own policies and make configuration changes at their speed which can be daily or hourly, with policies applied to inbound traffic only. While this architecture rates high on the seven attributes for North-South traffic, it is lacking on the East-West traffic as seen in the table below:

| Attribute                    | Rating                                                                                                            |
|------------------------------|-------------------------------------------------------------------------------------------------------------------|
| App Security                 | N-S: Excellent protection by Citrix ADC
|                              | E-W: Limitations with kube-proxy require extra network policy/segmentation solutions, such as Project Calico
| Observability                | N-S: Excellent, Citrix ADC sees all traffic
|                              | E-W: Very limited telemetry with kube-proxy
| Continuous Deployment        | N-S: Excellent, Advanced traffic control by Citrix ADC
|                              | E-W: Lacking due to kube-proxy limitations
| Scale Performance            | N-S: Good for scale-out
|                              | E-W: Use IPVS mode. IP tables mode lacks scalability
| Open Source Tool Support     | N-S: Excellent; (Prometheus, Spinnaker, EFK)
|                              | E-W: Limited due to kube-proxy limitations
| Istio: Unified Control Plane | N-S: Support via Istio – enabled ADCs
|                              | S-W: kube-proxy is not Istio enabled
| IT Skill Set Required        | Minimal training for platform and networking teams
|                              | Both teams can move at their own speed

### Unified Ingress

The Unified Ingress architecture, or One-tier, is a variant of the Two-tier modified to combine the North-South traffic ingress into a single Citrix ADC. A realized benefit of this combination is the reduction to one tier (and of 1 hop extra latency). The reduction provides a level of simplicity over the two-tier architecture at the cost of requiring a very networking savvy platform team.

![Unified Ingress Architecture](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_1-tier.png)

Unified Ingress is a good deployment for internal applications with the flexibility to add external applications that require more protection such as web application firewall and SSL/TLS. As with the Two-tier, this architecture rates high on the seven attributes for North-South traffic, but it is lacking on the East-West traffic. The major difference is the level of skill set required of the platform team.

| Attribute                    | Rating                                                                                                            |
|------------------------------|-------------------------------------------------------------------------------------------------------------------|
| App Security                 | N-S: Excellent protection by Citrix ADC
|                              | E-W: Limitations with kube-proxy require extra network policy/segmentation solutions, e.g. Project Calico
| Observability                | N-S: Excellent, Citrix ADC sees all traffic
|                              | E-W: Limited telemetry with kube-proxy
| Continuous Deployment        | N-S: Excellent, Advanced traffic control by Citrix ADC
|                              | E-W: Lacking due to kube-proxy limitations
| Scale Performance            | N-S: Good for scale-out
|                              | E-W: Use IPVS mode. IP tables mode lacks scalability
| Open Source Tool Support     | N-S: Excellent; (Prometheus, Spinnaker, EFK)
|                              | E-W: Limited due to kube-proxy limitations
| Istio: Unified Control Plane | N-S: Support via Istio – enabled ADCs
|                              | S-W: kube-proxy is not Istio enabled
| IT Skill Set Required        | Platform / Infrastructure team needs to be network savvy

### Service Mesh

The Service Mesh architecture is designed for ultra-secure apps and communications between containers. The Service Mesh is the most advanced and most modern architecture that addresses the functionality requirements for East-West traffic in addition to the North-South traffic.

![Service Mesh Architecture](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_sm.png)

By attaching a mini ADC as a sidecar to every microservice pod, the architecture provides the highest levels of security, observability, integration, and fine-grained traffic management. Functionalities of the microservice like connection retries and encryption between microservices can be offloaded into the sidecar. Excellent advanced traffic control over East-West traffic allows continuous deployment methods such as Canary rollouts to be done independently for each container with a sidecar CPX. The tradeoff is the additional complexity and scalability requirements (CPU/Memory) added with sidecars. Also of note; with the current version of Istio, bottlenecks with Istio Mixer can occur by adding an extra hop to each packet. Lastly, there is a steep learning curve for both the platform and network teams.

| Attribute                    | Rating                                                                                                            |
|------------------------------|-------------------------------------------------------------------------------------------------------------------|
| App Security                 | N-S: Excellent protection by Citrix ADC
|                              | E-W: Excellent protection sidecar, policy, rate control, authentication, mTLS, API, and Layer 7 attack protection
| Observability                | N-S: Excellent, Citrix ADC sees all traffic
|                              | E-W: Excellent, Citrix ADC CPX sidecar sees all traffic
| Continuous Deployment        | N-S: Excellent, Advanced traffic control by Citrix ADC
|                              | E-W: Excellent, Advanced traffic control by Citrix ADC CPX sidecar
| Scale Performance            | N-S: Good for scale-out
|                              | E-W: Distributed architecture scalability, sidecar quality dependent, adds 2-hop latency, more CPU/memory
| Open Source Tool Support     | N-S: Excellent; (Prometheus, Spinnaker, EFK)
|                              | E-W: Excellent; (Prometheus, Spinnaker, EFK)
| Istio: Unified Control Plane | N-S: Support via Istio – enabled ADCs
|                              | E-W: Support via Istio APIs, Istio Mixer bottlenecks
| IT Skill Set Required        | Steep learning curve for platform and networking teams

### Service Mesh Lite

The Service Mesh Lite architecture is similar to a Two-tier, a Citrix ADC VPX/MPX outside the Kubernetes cluster and a CPX inside the cluster. This similarity makes for an easy transition to Service Mesh Lite in the future if starting out with a Two-tier deployment. However, requirements such as securing traffic and observability are met more easily since all East-West communications between pods or microservices pass through a centralized CPX.

![Service Mesh Lite Architecture](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_sml.png)

Effectively, the architecture has most of the benefits of a Service Mesh, but with reduced complexity. One benefit over a Service Mesh is the benefit of removing the extra hop seen with sidecars. However, CPU/Memory sizing of the CPX is more critical to ensure enough resources for each pod. Another distinction is that features like mutual TLS or encryption between the microservices cannot be offloaded to the sidecar. mTLS must be done at the application level. This additional requirement to build encryption – a nonbusiness logic functionality - into a microservice is not optimal for performance, scalability, and consistency.

| Attribute                    | Rating                                                                                                            |
|------------------------------|------------------------------------------------------------------------------------------|
| App Security                 | N-S: Excellent protection by Citrix ADC
|                              | E-W: Excellent protection by Citrix ADC CPX, optional mTLS
| Observability                | N-S: Excellent, Citrix ADC sees all traffic
|                              | E-W: Excellent, Citrix ADC CPX sees all traffic
| Continuous Deployment        | N-S: Excellent, Advanced traffic control by Citrix ADC
|                              | E-W: Excellent, Advanced traffic control by Citrix ADC CPX
| Scale Performance            | N-S: Good for scale-out
|                              | E-W: Highly scalable, adds 1-hop latency
| Open Source Tool Support     | N-S: Excellent; (Prometheus, Spinnaker, EFK)
|                              | E-W: Excellent; (Prometheus, Spinnaker, EFK)
| Istio: Unified Control Plane | N-S: Support via Istio – enabled ADCs
|                              | E-W: Support via Istio APIs, Istio Mixer bottlenecks
| IT Skill Set Required        | Minimal training for platform and networking teams
|                              | Easy transition from 2-Tier ingress architecture

## Architecture Decision Matrix

Cloud Native is all about scale, flexibility, and speed. We know that we can orchestrate everything we need with Kubernetes, and we can run that infrastructure across any Cloud. The key to having all these services running together and adapting to changes and new requirements happens at the ingress point – your Load Balancers. With CPX, you not only have an enterprise-class load balancer, but you can use the data to adapt and respond without any intervention.

The following matrix summarizes the differences between the four proxy architectures available with the CPX as the Ingress Controller. This matrix provides a good framework to consider which architecture to choose.

![Architecture Complexity Benefits Chart](/en-us/tech-zone/learn/media/tech-briefs_cloud-native-networking_spectrum.png)

There is no right or wrong answer, it’s purely dependent on the team skill set and the tradeoff between benefits and complexity. If you’re looking for the simplest and quickest way to production, then Two-tier ingress is the preferred choice. However, you will lack the capabilities for advanced features for East-West traffic. If you have a network savvy team, then Unified ingress seems like a great choice, but you still lack that East-West intelligence. If you’re looking for the best observability, the best security, then the Service Mesh is the architecture of choice, but it is complex. If you want to offer similar features of the Service Mesh but much simpler to deploy and manage, then Service Mesh Lite can be a good balance.