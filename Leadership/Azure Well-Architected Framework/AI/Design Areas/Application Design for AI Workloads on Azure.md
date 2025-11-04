This section covers the areas that can benefit from applying Azure Well-Architected Framework. Note that, it is not necessary to went through all of them but it is good to review them based on your needs https://learn.microsoft.com/en-us/azure/well-architected/ai/application-design.

# Application Design for AI Workloads on Azure

This section provides common design areas and factors to cosioder when you make decisions about the technology and approach when you create an application that has AI functions. When designing intelligent features, your functional and nonfunctional requirements determine whether you need simple inference or advanced problem-solving capabilities. These requirements should guide your high-level and detailed design decisions throughout the process.

* The first step in designing your AI worload is to make a decision on buy-vs-build and to achieve this, it is good to consider __model catalouge, Liscencing, Key components like model architecture, training data, performance metrics etc__

## Application Layer Architecture

When designing intelligent capabilites, establishing clear boundaries in your architecture with the following key layers.

![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/electra_loss.png)

* __Client Layer__ : This layer lets the user or other clients experience your intelligent workload's ca;pabilites. This layer must be kept thin and delegate most capabilites to other layers.
* __Intelligence Layer__: Routing, Orchestration, agent capabilites (agent's card) that coordinate AI Operations. This layer contains, model routing, conversation management and intelligent decision-makings
* __Inferenceing Layer__: This layer handles model loading and runtime invocation, input preprocesing and output preprocessing, along with model serving predictions via API or embedded systems.
* __Knowledge Layer__: Grounding data, knoweledge graphs, and rertival services that provide relevant context and information into the intelligent layer. This layer enforcess data access policies and authorisation.
* __Tools Layer__: Business APIs, and external capabilites that inteliggence layer can invoke. This layer should use standardised interface and enforce it's own security policies.

*Note that each layer mentioned enforces itw own polices, identites, and caching strategies* to achieve their own __localised, reliability, security and performance requirments.__

## Recommendation 


| **Recommendation** | **Description** |
|----------------------|-----------------|
| **Prioritize security and Responsible AI controls** | Implement traditional application security plus AI-specific safety measures as a primary design driver. Enforce provider safety systems, input/output filtering, identity-bound rate limiting and quotas, and token/prompt caps. Security and safety controls must be verified and can't be assumed from managed services. |
| **Keep intelligence away from the client** | Design back-end services to handle cross-cutting concerns like rate limiting, failover operations, and AI processing logic. Abstract behavior and intelligence away from the client to future-proof your design and improve maintainability. |
| **Block direct access to data stores** | Code in AI systems shouldn’t directly access your data stores. Route all data requests through an API or similar data access abstraction that enforces authorization and propagates user or tenant context into retrieval and filtering. Pass forward user identity so that data level security can be applied. |
| **Abstract your models and tools** | Use abstraction layers to decouple your application from specific models, tools, and technologies. Implement standardized interfaces and protocols to provide flexibility as technologies evolve, which makes your design more maintainable and future-proof. |
| **Isolate behaviors and actions** | Design clear boundaries across client, intelligence (routing/orchestration/agents), knowledge (grounding data), and tools (business APIs) layers. Each layer should enforce its own policies, identities, and caching strategies to reduce blast radius and focus development efforts. |
| **Prioritize prebuilt solutions** | Use software as a service (SaaS) or platform as a service (PaaS) to handle workload functions when they meet security, safety, compliance, and quota needs. Implement compensating controls through gateways that enforce authentication, quotas, safety, and logging. Use prebuilt and pretrained models where possible to minimize the operational and development burden for your workload and operations teams. |

## Distincition Between Inferencing and Intelligence Applications

* __Inferecneing Application__: Such pplications will do a single step operations like classification or regression. The typical architecture is like the client communicate via AI gateway that provides authentication, quatas, safety, and routing. That calles into the model serving (model inferenecing) layer in Azure AI Foundry, AKS, or online managed endpoints. While practical th results might be chashed for further useage. __Such applications require minimal orchestration, and the focus is on performance and throughput__.

* __Intelligent Application__: Such application perform planning, coordination, multi-step reasoning and are handled via agents and agent orchestration. The typical architecture likes the client (client layer) invokes an agent or agent orchestrator (intelligence layer) where the agent will invoke external tools via MCP (tool layer) or call other agents or APIs. The agent might call into grounding knowlegde (knowledge layer). __This kind of applications usually need agentic patterns and model routing and comples workflow coordination integrate with multiple data sources and tools and requires conversation and context management.__


## Fundamental AI Applications Design Guidlines

* __Start with the business outcomes__: Before you come up with the technology selection come up with the business problem defintion. This defintion helps you to select the choices and architectural design.
  * __Success metrics__:  Define measurable outcomes such as accuracy, cost reduction, or user satsifaction
  * __User Exepericne Requirments__: Understand how the users and process interact with the AI capabilites and what response time they expect.
  * __Regulatory Constraints__: Identify compliance requirments such as data retention and explanability requirments.
 
* __Design for security from day one__

* __Design for observability from day one__: AI workloads needs extra obcervability (application and infrastructure)
  * __Model Performance Tracking__: Model accuracy drift, inference latency
  * __Data quality Monitoring__ : The input data that can change the model peformance
  * __User interaction analysis__
 
* __Plan for the abstraction and future capability__: Desing systems with abstraction layer that allow you to adopt as AI evovles
  * __Model Abstraction__: Abstract models behind consistent interface. For example, create an interface for LLMs from diffent vendors.
  * __Standardised protocols__: 
  * __Capability-based Design__: Design your AI workload or application around capabilites rathetr than specific technology. For example, instead for desining for Text summerisation via GPT4.o endpoint, define a class for summerisation and then put GPT models behind that inteface class. This way you decouple the technology from the capability.

* __Externalise prompts and configurations__

* __Plan for model deprecation__

* __Containerise Components__: To ensure indepdendently deployable components are self-contained and to improve deployment consistency consider the followings:
  * __Microservices__: Inidvidual microservices that handles data processing, model inference must be containerised
  * __AI Models__:
  * __Data Processing__:
  * __Infrastructure Services__:
 
* __Implement multi-layer caching strategies__: A multi-layer chacing strategy improves the performance of AI workload and reduce the cost. Consider the following levels:
  * __Results and Answer Caching__: This can, for example, minimise the number of calls to the LLM
  * __Retrival and Grounding Snipper Caching__: This can help minimize the number of calls to retrive the data from the search engine
    
  For developiing caching at the levels mentioned a couple of important things need to be noted. For example, what data is frequently get accessed and how the application users's role infunce what data they can access. Factors that need to be considered

  * __Cache Key Component__: Cache values need to be tied to specific runtime fsctors within your workload. Include values such as tenant, or user identity, policy context and model version
  * __Time-to-live (TTL)__: Teh cached values must have expiration time to ensure data freshness and content sensitivity
  * __User Privacy Protection__: Necer cache PI data unless it is required at runtime or by the policy. Usually caching works if the cached values work across several users. Hence do not create cahcing mechanism for individual users.

Note that caching improves the performance and might reduce the operational cost but at the same time it increases the security risk by data leakage and privacy violations.

## Evaluate the use of Orchestration and Agents in Generative AI Solutions

### Choose the apporpriate coordiation approach based on your application's requirments

  * __When you use orchestration__
     * __PRedictable workflows__: Multi-step well defined sequences and decisions points
     * __Compliance Requirments__: When you you need specific steps for regulatory requirments (e.g., masking sensitive data before saving)
     * __Simple coordination__
     * __Performance Crictical Path__: Workflows where the latency and resource usage must be controlled
  
  * __when you use agent collaboration__
     * __Multi-step Reasoning__ : Tasks require planning and decision-making across many steps
     * __Tool Coordination__ : Complex workflows that require coordinating multiple specialised tools
     * __ Adaptive Behavior__ : Scenarios where the system needs to change the nehavior based on some intermediate results
     * __ Context Management__ : The tasks where the agent need to maintain the user-context and converation state across intercations.

### Agent Design Summary

  * __Design considerations__: Define clear boundaries between agent capabilities to improve reliability and testing. Use proven communication patterns (e.g., topic–queue) and standard interfaces for tool abstraction and flexibility. Avoid adding unnecessary agent layers—only use them when complexity justifies it.

  * __State management__: Persist minimal agent state for multi-turn or long-running tasks using secure storage (e.g., Cosmos DB, Redis). Implement TTLs to manage privacy and cost, and ensure agents can resume seamlessly.

  * __Hybrid approaches__: Combine orchestration and agent reasoning where appropriate:

    * Use orchestration for structure and control.
    * Use agents for complex or domain-specific tasks.
    * Escalate to agents when deterministic logic isn’t enough.

__Trade-off__: Orchestration offers control and predictability; agents add flexibility but increase complexity.

## Implement AI Gateway for Policy Enforcement

Use AI Gateway in your design to provide __gateway offloading__, __gateway Offloading__, __gateway aggregation__ capabilites within your worload. Ai Gateway can be used to proxy requests to model providers, agent endpoints, tool, and knowledge stores.

### Core AI Gateway Capabilites

AI gateway can address cross-cutting concerns and swerve as a layer of abstraction, indirection from the target system. The following capabilites can be considered from AI Gateway.

* __Protocol Normalisation__ : For shared AI capabilites across providers, normolise interfaces to a common protocol via gateway imroves maintanability
* __Authentication and Authorisation__ : Enfroce identity-based access control and pass the user context to the downstream applications
* __Rate Limit and Quoata__ : Implement per-user/per-application or per tenant rate limits to avoid abuse consumption and mange costs
* __Request and Response Filtering__ : Apply input validation (i.e., type check), prompt filtering, and checking output safety via AI gateway
* __Token and Prompts Cap__: Put maximum token cap per transaction or prompt to avoid resource exhausation
* __Model Routing Policy__ : Rout the ewquests to the apprpriate model based on the user permission and request type (text, image etc)
* __Header Injection and Transfomration__ : Add user data context, security token for the downstream applications
* __Chargeback Management__: Allocate charges across depratments tha use shared AI services.

### Use AI Applications Design Patterns

There are a number of indusctry design patterns that one can use to simplify the design of AI workloads.

*  __Ensembling__ : It combines the outbout from several models to improve accuracy and robustness by mitigating the limitation of single model
*  __Microservice architecture__ : It seperates components into independetly deployable services to enhannce scalability and maintenance. It enables teams to work on different components simultanously.
*  __Event Driven Architecture__ : This design pattern uses events to trigger actions, which enables decoupled components and realtime processing, to make system more responsive and adaptable to changeing data
*  __Multi-model routing__ : Model rounter helps to improve the system's responses by routing the request to a healthy model when another model runs into unhealthy state. Or it can help the quality of responses by selectign the best model responses. Note that, it is important to know on when to use model router:
   *  __When to use model rounter__: Use this pattern if your workload cal tolerate latency and variability in responses or you need to balance the cost and capabilites across different models
   *  __when to avoid model router__: Don't use this pattern if you need very precise responses from the fine-tuned model or when determinsitc behavior, including consistent performance is critical.
 
__Note that, small applications or POCs will not benfit from the mentioned design patterns. These pplications must be designed for simplicity. Likewise, if there is a limited time or budget, or headcount, use a simple approach where later can be refactored to adapt a complex design patter.__

### Choose the right Frameworks, Libraires, and Protocols

The choice of framework and libraries is closely intertwined with application design. They affect, __performance, scalability, and maintenance__. However, the application design requirments can limit your framework choice. For example, use of Semantic Kernel SDK often ecourages a microservice-based design. wwhen you select a library or protocol, perefer open documented interfaces. There are a lot of emerging libraries and tools, for example, agent-to-agent, consider the tech debt you might bring from such evolving libraires.  Layers of abstraction can help keep the design interoperable across different AI libraries and frameworks.

Consider the following factors when you choose frameworks and libraries:

*  __Application Requirments__: The requirments of the application like real-time processing, batchprocessing, low latency etc might limit the choice of framework. For example, if low latency is part of the requirments then the framework needs to support asynch calls.
*  __Integration needs__: If the applications needs to be integrated with specific systems or needs to generate specific formats then it might limit your choice of framework
*  __Team Expertise__: The design that relies on less familiar framework might add extra development time and complexity
*  __Community and Support__: Active communities and comprehensive documents reduce the implemntation risks
*  __Performance characteristics__: Evalaute framewoek peformance such as memory usage, startup time, and inference speed.
  

Also, adopt standardised too protocols to improve governance and flaxbility
*  __Use MCP style servers__: Use MCP or another sutiable standard tools to wrap business capabilites as descoverable tools
*  __Use Specifications that have broad SDK support__: Define Tools by using OpenAPI specifications for cnsistent inference documentation and validation
*  __Advertise Capabilites__: Design tools to advertise their capabilites, which allows the orcehrtrator to discover and route the requests appropriately. For example, if you have an ERP system, wrap the read and write operations as discoverable tools where the agents can discover them, hence no need to change the agent logics for the ERP.
    

## Design a Security Strategy for AI Workload

Implement AI security measure at all layers of applications while using standard identity providers like Microsoft Entra ID

### Core Security Principals

* __Standard autentication and authorization__
* __Data Lineage security wuth user context propagation: Ensure that the access control is maintained throughout the data pipeline. Pass user identity so that the ueser can only access the results he/she are authorised to see.
*  __audit trails__ : Implement detailed logging of AI interaction for compliance and security monitoring.
