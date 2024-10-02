
# Chapter 1 - Introduction

* Enterprise Architecture Management (EAM) is a way to deal with organizational complexity and change in an increasingly turbulent business environment.
* EAM is a discipline that helps to systematically design and develop an organization according to its strategic objectives and vision.

    * For this purpose, models are used to guide the development of Enterprise Architecture (EA) by identifying as-is models describing the current state and to-be processes describing the future EA state.
    * Each model covers one aspect of the organization, i.e., business, organization and process, information systems, and infrastructure.

## The need for Enterprise Architecture Management (EAM)

* Companies operate in an ever-changing marketplace characterized by variable customer demand patterns, fast-paced technology innovation, shortening product life cycles, and a global value chain. So there is an urgent need to adapt to the changing environment to stay ahead of the competition.
* Change affects all elements of an enterprise's value creation: products and services, corporate capabilities, etc.
* Enterprises respond to the ever-changing market by redesigning the organizational structure and processes to be efficient and effective, and by leveraging information systems to distinguish their business. In doing so, they continuously change their enterprise architecture.
* Although changes are intended to enhance an organization's competitiveness, they have some side effects:
    * If change initiatives are launched independently, with little or no coordination across the enterprise, they result in heterogeneous, incompatible, and costly changes to information systems, business processes, and organizational structures. __Even worse, additional investment in organizational redesign and information technology might not pay off, as it could produce architectural complexity. Therefore, the investment might generate risks that could even disable the business.__
* Architectural complexity manifests in various ways:
    * __Loss of transparency__: With increasing architectural complexity, managers might lack the fundamental information necessary to make decisions.
    * __Increased complexity costs__: If different technologies are used in different parts of the organization, IT investment will most likely be relatively high.
    * __Increased risks__.
    * __Inability to consistently implement strategic directions across the organization__: The more complex an enterprise architecture is, the more difficult it is to restructure or redesign it, and the more problematic it is to implement strategic changes in the organization. In the worst case, the organization might remain stagnant.
    * __Distraction from core business problems__: Complex enterprise architectures tend to tie down highly skilled professionals. __Instead of maintaining competitiveness, they are distracted by having to manage the complexity and preserve the current state.__
* Organizations that suffer from architectural complexity due to a lack of transparency in their organizational changes cannot answer the following questions:
   * How can we successfully integrate new firms after an acquisition?
   * Can we introduce new products and services using the existing business processes and underlying applications?
   * Which business units and users will be affected by an application's migration?
   * What applications and infrastructure technologies do we require to run new or redesigned business processes?
* Transparency is a prerequisite for reducing organizational complexity to gain flexibility.
* EAM seeks to maintain flexibility, cost-efficiency, and transparency in the enterprise architecture. It emphasizes the interplay between the business (business models, organizational structure, and business process models) and technology (information systems, data, and technological infrastructure). __EAM helps to systematically develop the organization to achieve its strategic objectives and vision.__
* How EAM helps improve enterprise performance:
  * __Architecture Transparency__: EAM establishes transparency by documenting the main enterprise architecture components and their interrelationships. This model is often complemented by additional management-level information such as cost, security, benefits, and compliance. Transparency is a prerequisite for identifying synergies and allocating resources efficiently.
  * __Documented Architecture Vision__: Based on the transparent view of enterprise architecture, the management team can develop the organization or part of it. A documented architecture vision represents a shared view among multiple stakeholders that enables better alignment between different layers of enterprise architecture. This alignment can be, for example, between business processes and IT systems or between IT systems and infrastructure systems. Weak alignment increases manual work, and multiple systems may be needed for one task, leading to lower data quality.
  * __Architecture Principles and Guidelines__: To guide the development of an organization, management must define architecture principles and guidelines. Modularization (microservices) and interfaces are two ways of promoting reuse, cost reduction, and scalability. Modularization also allows for outsourcing or reconfiguration of the value chain.
* EAM can reach its potential if it is aligned with strategic business objectives; therefore, EAM must be linked to the organization's strategic planning and strategic implementation processes. Hence, EAM is not an IT function but rather a strategic function.

![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Leadership/Strategic%20Enterprise%20Architecture%20Management/image/EAMEffects.png)

* EAM plays an important role in organisational transformation and development and it is executed by a board member. __EAM is usually merge with other business development department which underlies the strategic development of enterprse architecture in the following ways__:
   * __IT/IS as a mean of strategic and organisational transformation__: Companiesw invest in their ITs/ISs if tey realise that they improve organisational efficency, employer productivity and implement new strategies. Hence planning of the IS landascape needsc to be closely linked to the strategic direction of the company.
   * __Increases outsourcing__:Increased Outsourcing: Some companies concentrate on their core competencies and oursource the other part of the value chai. In such cases, detailed monitoring of the external service providers is crucial and EAM may provide some mechanisms for monitoring the activites. Also, EAM the required interfaces to the external service roviders and supervise them in their service provision
   * __Business IT alignment__: Many organisations have made great progrss in sourcing, making and delivering IT infrastructure applications. However, it is crucial for business to align their IT/IS services with their business needs and EAM is great tool for establishing this alignment.
   * Enterprise Architecture (EA) includes all relevant infromation for describing an enterprise, including its business and operating model, organizational structure, business process, etc. EA design rules provides stipulation for the development and structring of the components, as well as a means to ensure consistency among the components.
     
### Enterprise architecture models and layers
![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Leadership/Strategic%20Enterprise%20Architecture%20Management/image/EA_layers.png)
* To describe an organization's fundamental structure, EA models often contain large components. The EA is most inclusive of all main components if it is presented from different perspectives at different layers of abstraction. There is no standard on the layers and the components to be included, but usually, the following layers are considered:
   * __Strategy Layer__: It describes the positioning of an enterprise at the higher level of abstraction and it is developed after the business strategy is defined. The typical artifacts of this layer are: __Value network__, __customer and market segmentation, products and service portfolio, business goals__.
   * __Organizational and Process Layer__: It indicates the organization structure and its processes. It comprises the static aspects, i.e., departments, business units, and roles, as well as dynamic aspects, for example, business processes and tasks.
   * __Information System Layer__: It describes how information is processed and shared electronically within the organization. This layer can be broken down into the following layers:
      * __Application Layer__: It includes the main software components that implement the business logic. __Typical artifacts are application components and services__.
      * __Data Layer__: It describes how the key business information is presented and implemented in databases. __Typical artifacts are data models and databases__.
      * __Integration Layer__: It indicates how applications share or could share data and functions with other applications and databases. __Typical artifacts are interfaces, protocols, and integration components__.
      * __Infrastructure Layer__: It includes computing services that form the enterprise technical infrastructure. __The typical artifacts are system software and infrastructure configurations__.
      * __People and Competencies__: It represents the people and their competencies required to develop and operate an enterprise architecture consisting of the aforementioned layers.


```
