https://learn.microsoft.com/en-us/azure/well-architected/ai/application-platform

* It is very important to consider the application hosting platfrom that your AI workload is deployed to ensure that you can maximse efficency, operations and reliability.
* The required platfrom capability depends on the applications and AI workloads hosting on that. This article provides platfrom design considerations for the the types of applications or AI workloads
    * Explratory Data analysis (EDA)
    * Model training and model fine tuning
    * Infrencing 

# Recommendations

This section recoomends some design considerations for different types of workloads that needed to be deployed on the platfrom

*  __Resue Tools__: It is recommended to reuse existing tools for your AI workloads if they meet the functional requiremtns for security, reliability, cost, and peformance. Intorducing a new tool might not worth the cost and efforts.
*  __Consider the Compliance Requirments for your data and the region that you plan to dpeloy__ 
*  __Minimize the building__: It is advised to use PaaS and SaaS as much as posuble to avoid the operational burden of custom solutions like patching and other maintenance.
*  __Understand quatas and limits__: when using PaaS and SaaS understand the quata and limits that apply. The scale up or scale down of the application might be limited by quata
*  __Deploy in the same region__:
*  __Practice Safe Deployment__: It is recommdned to have staged deployment (e.g., the deployment first will be accessible only to part of end-users to ensure if there is and impact it can be controlled)
*  __Establish performance and Benchmarks through experimentation__:


# Considerations for the EDA platfrom
