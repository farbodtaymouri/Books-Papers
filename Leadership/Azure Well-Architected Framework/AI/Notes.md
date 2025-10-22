# AI Workload on Azure
* In the context of Well-Architected Framework, the term workload refferes to the collection of application, resources, custom codes, AI models and supporting infrastructure that functions togather to define a business outcome.
* The Azure Well-Architected framework focuses on ethical functionality, adapting to fast evolving AI technologies and staying relevant and explainable. Note that, applying __Well-Architected Framework Pillars (Reliability, Security, Opereational Excellecen, Performance Efficiency, and Cost Optimisation)__ ant any decision point ensure the system is reliable, secured, efficient and cost effective.
* Before to begin the design strategy for an inititative consider the following points:
  *  Whether to use discriminative AI or Generative AI?
  *  Evaluate your build vs buy option. If a generic responses are required then a prebuilt model is or an AI-service solution should be sufficient for the workload, but if you need data specific to your buinsess or have compliance requrments it is better to create a custom model. Consider the following aspects for seelcting between buy vs build:
      * __Data Control__: Custom model gives you better control over sensitve data
      * __Customization__: Custom model gives better flesxibility for unique needs.
      * __Cost and upkeep__: Custom models need ongoing maintenance and resources
      * __Performance__: Prebuilt models and AI services are better at scaling and providing lower latency
      * __Expertise__: Custom models needs expert people to work on them. Pre-built models are easier to deploy with less technical backgorund
   
* What are the challenges with the AI Workloads
    * __Compute Cost__: AI functions can be expensive because of high-compute needs and compute needs can vary according to the workload design.
    * __Security and Compliance Requirment__: Off-the-shelf solutions might not meet your security and compliance beeds
    * __Volume of data__
    * __Model Decay__: Model can degarde over time (data shift or model shift concept). Hence testing AI workloads needs to be ongoing and it is challengeing
    * __Peace of AI Innovation__: Adding a new technology to the current stsck needs to impove user-expericne and don't just add complexity to the system
    * __Ehtical Requirments__
* How to use Well-Architected Framework for AI Workloads
    * Start with the __Design methodology__
    * Proceede with the __Design Principles__ to see how the design methodology alings with the core Well-Architected Framework pillars
    * Focus on the desing areas (MLOPS, GENAI) that has the biggest effect on your solution
    * Use Assessemtn Review Tool https://learn.microsoft.com/en-us/azure/well-architected/ai/assessment to evlaluate the readiness of your AI workload.
# Design Methodology for AI Workloads
If you desing a capability or intoduce and improvement, evaluate the change from the methodology perpective. Does your change affect the user experience? Is your change felxible enough to adapt to the future innovations? Does it disrupt the experimentation flow?

* __Design with an Experimental Mindset__: Design your AI workload or system as if experimentation is oart of their DNA. AI workloads rarely work perfectly in the first run, and hence they got better by adding feedback, trials etc. This desing methodology includes two loops: __ineer loop__ where the focus is on model training and evalaution in dev/test and __outer Loop__ or continues montoring and re-training in production.
  
* __Design responsibly__: You should embedd the Responsbile AI principles in your design as the users trust your system. __Content Moderation__ is the key strategy in respoinsible desing on AI systems (evalautiong the input/out of the system).  __Ethical data management__ is central to responsible design. The user trust you to ensure that no perosnal information is kept or if it is kept it is with their consents to ensure the data is protected which helps ensure privacy and security are in place.

* __Design for Explanability__: AI models must be explainable. you should be able __to trace the origin of data, the inference process and the journey of data from the source to the serve layer__.

* __Stay ahead of model Decay__: model decay is common in AI workloads and can happen time to time without any changes to the code. sometimes it can happen suddenly due to technology changes. Such deterioration affects various aspects of the system such as data_ingestion speed, data quality,monitoring speed.

* __Design for adaptability__: Be aware that what you build today might become obsolte quickly and keep that in mind when you make decisions and create process. It is important to recogniae the components that have limited lifespan. Adopt a __Pause-and-think__ approach that focuses on the research for the model discovery, programming libraries, frameworks, etc.
  
