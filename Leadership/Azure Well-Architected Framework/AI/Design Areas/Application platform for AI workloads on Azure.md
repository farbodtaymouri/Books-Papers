https://learn.microsoft.com/en-us/azure/well-architected/ai/application-platform

* It is very important to consider the application hosting platfrom that your AI workload is deployed to ensure that you can maximse efficency, operations and reliability.
* The required platfrom capability depends on the applications and AI workloads hosting on that. This article provides platfrom design considerations for the the types of applications or AI workloads
    * Explratory Data analysis (EDA)
    * Model training and model fine tuning
    * Inferencing 

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

EDA is a common function that all data scientist do before any ML modeling or statistical analysis. IT can be considered as a development or descovery phase, which means that the target for reliability and performance is much less than those for production resources. For the EDA activities the following capability from the platfrom might be considred:

## Functional Requirments

Considerations the following questions:

*  __Does platfrom support Transient usage__: Most of the EDA activties are time-boxed and interactive, hence the resources used might be able to be turned-off if not used. This capability helps to control the cost of EDA.
*  __Does the platfrom supports compute optionality__: The platfrom should be able to enable on-demand GPUs as needed and various compute options to help right-size the platfrom
*  __Does the platfrom support MLflow__: The platfrom should enable MLFLOW to track the experiments. It is importnat because
     *  __Experiment Tracking__
     *  __Reproduibility__
     *  __Data and model versioning__
     *  __Collaborative work__

## Non-functional Requirments

The NFR for the platfrom capability to support the EDA activities are as follows

*  __How the platfrom can help control costs__: the platfrom should enable DS to do their owrk according to their schduling requirements but it should be right-sized to ensure the cost expectations are met.
*  __What security requirments must be followed by the platfrom__: Since EDA task are done with mostly production level data the same security constrains must be followed by the platfrom.
     *  __Authentication and Authorisation__
     *  __Encryption of data at rest and transient__
     *  __Regional and Data protection requirments__
     *  __Robust monitoring and alresting functinality including logging__
     *  __Private networking access to the centralise repositories for container images, data, and code assests__.
 
 
 # Considerations for model traning and fine-tuning

 When moving to the model training and fine-tuning for sure Data scientist needs high-performance compute nodes for compute intensive tasks. For such tasks, the performance efficiency is more important reliability since these tasks happen behind the scence. However, if you need high reliability when model freshness is required, then you need ro consider who to spread the workload across the regions.

 ## Functional Requirments

 when evalauting platfrom capability for model training and fine-tuning the following questions must be considered

  *  __Does the platfrom support transient usage__? Like EDA tasks, model training and fine-tuning are temporary. Unlike EDA tasks that are interactive, model training requires batch jobs. Hence the compute nodes must be turned off when the batch job finishes.
  *  __Does the platfrom support orchestration__? Since the model training and fine-tuning might require several steps, hence the platfrom need to have an orchestrator
  *  __Can existing technology on the platfrom be part of the solution__?

# Considerations for model hosting and inferencing platform

Model hosting and inferencing functions make the serve layer of an AI workload.These functions are performed with the endpoints that are specific to the software that you use. Model-serviing software solutions are Python SDKs that front an model via API and add functionality that is specific to the solution. Fundamentally the APIs for the serve layer are microservices, thus you must follow the same practice for these APIs that you follow for other microservices in your environment. They should be containairsed, bulkheadec from other services and have their own lifecycle that are indepndent of other APIs and microservices.

## Functional Capabilities

