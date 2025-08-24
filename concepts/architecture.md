
# search ..........
## ????????????????

# Architecure patterns

1. Layered Architecture (N-tier Architecture):
2. Client-Server Architecture
3. Microservices Architecture
4. Event-Driven Architecture
5. Microkernel Architecture (Plugin Architecture)
6. Service-Oriented Architecture (SOA)
7. Service-Driven Architecture (SDA)
8. Model-View-Controller (MVC)
9. CQRS (Command Query Responsibility Segregation)
10. Peer-to-Peer (P2P) Architecture
11. Space-Based Architecture (Cloud Data Grid)
12. Pipes and Filters Architecture
13. Monolithic Architecture

MVC (Model-View-Controller)	Separates data (Model), UI (View), and logic (Controller)
MVT (Model-View-Template)	Django’s version of MVC – Templates instead of Views
MVVM (Model-View-ViewModel)	Common in frontend frameworks like Angular or WPF
Layered (N-tier)	Code separated into layers: UI → Logic → Data
Clean Architecture	Inner core (domain logic) isolated from outer layers (frameworks)
Hexagonal (Ports & Adapters)	Application core is independent, communicates via "ports"
Microkernel (Plug-in)	Core system with plug-in modules (e.g., IDEs like Eclipse)
Event-Driven Architecture	Components communicate via events (pub/sub)
Microservices	App is split into small independent services
Serverless Functions	Code is broken into stateless functions triggered by events
Service-Oriented Architecture (SOA)	Similar to microservices but usually heavier and enterprise-focused
Peer-to-Peer (P2P)	No central server; all nodes are equal (e.g., BitTorrent)
Client-Server	Client sends requests; server handles logic and responds
Pipeline	Data flows through a series of processing stages (e.g., compilers)
Component-Based	App is built using reusable, loosely-coupled components

# 


| Deployment Style     | Description                                                             |
| -------------------- | ----------------------------------------------------------------------- |
| **Monolithic**       | Entire app is a single deployable unit                                  |
| **Modular Monolith** | Monolith with clearly separated modules (cleaner structure)             |
| **Microservices**    | App is split into independent services, each with its own deployment    |
| **Serverless**       | Functions run on-demand (e.g., AWS Lambda, Vercel)                      |
| **Containerized**    | App runs in containers (Docker), often used with Kubernetes             |
| **Virtual Machines** | App deployed to full OS instances (EC2, etc.)                           |
| **Cloud-native**     | Built for cloud scaling & auto-deployment (e.g., 12-Factor Apps)        |
| **Edge Deployment**  | App runs close to user (CDNs or edge functions like Cloudflare Workers) |
| **Hybrid Cloud**     | Part of app runs in cloud, part on-prem                                 |
| **On-premise**       | App runs on local/private servers, not cloud                            |
| **Multi-tenant**     | One app instance serves multiple users/companies securely               |





