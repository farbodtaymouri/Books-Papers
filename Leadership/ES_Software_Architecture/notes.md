# Chapter 5 - Politics 

* Politics is the art of getting things done by collaborating with other people.
* The realites in a political eccosystem need to be understood:
  * Every one has a history
  * Point of view on a particular suject based on the history
  * Organizational context on which they operate and drivse their motivations
* Play nice, the person you made unhappy may suddenly become your boss. 
* Unintended political consequences maybe the results of not fully seeing or begin aware of the true financial, organizational of a particular decision
  * Think and act carefully, and always maintain a 360 view of what is going on in the organization around you.
* Be careful of people who do not stand behind what they say when the heat turns up. People rarely change their behavior, and the are likely to repeat it over and over.
* The best way to play the game of politics __is not to play at all__.
    * If you say you are going to do something, do it.
* Not everyone will be happy all the time. You will make hard decisions.
    * Don't be afraid to ask for what you really want, you might actually get it.
* __Success at Politics = Gracious Behavior + Communication + Negotiation + Leadership__

# Chapter 11 Pragmatism

* The job of an architect is to hold vision in one hand and the reality in the other hand, and to bring them together.
* __Pragmatic architecture__ is the notiation of driving toward the architectural vision whicke constraining projects to reality (tacticals). Note that, Pragmatic Enterprise Architecture Framework (PEAF) is frame work for implementing enterprise strategies. From a very high-level, It can be achieved through
   * __Risk management__,
   * __Scope management and__,
   * __Communication__
* Assume that you have a new project proposal that need to be delivered in 4 month and it brings lots of benefits for the company. You have three alternative architects and you have three days to come up with, __resource needs estimate, risk estimates, key assumptions and review the proposal with the affected people.__
  * Using a new open-sources technologies which the company has no experience
  * Developing everything from scratch that needs to be integrated with legacy systems
  * Hacking the legacy system but it might be gragile under heavy useage.
* What do you do regarding the three above options?
## Scope Management
* The first key area to address in pragmatic architecure is the scope management. It means that, work with the business to determine the feature priority. Note that, you must focus on features that bring ROI for the project rather than considering fancy technologies.
* Think of features as tack of cards. Priortize them in the order of customer value. This way you can execute/deliver the highest customer value earlier.
* As an architect you always have to deal with ambiguity and the more information you have the more precise decision you can make about the architecture of the project. However, no matter how many detials you seek, there will always be a level of ambiguity in decision making. The challenge is to get to a point where you are comfortable with the ambiguity and allow the project to move forward.
* To apply scope management in pragmatic architecture, you need to use agile process such as scrum or lean. Agile process lets architect to focus on the parts with les ambiguity and push ambigous parts of the project to the backlog where more information become avaialbe to address them in the next iterations or as part of the spike (time-bounded PoC). The challenge with the agile develoment method is to ensure you have some level of focus on the overall roadmap and product release. Note that the architecture of the project needs to be addressed but it doesn't need to be upfront.
## Risk Management
* The second key area of pragmatic architecture is the risk management. It is good to spend time early at the discovery phase of the project to identify, document and mitigate as many as potential risks as possible.
* __For any architectural approach there are a range of possible implenetations__. You need to ask the following questions to understand the difference between possible and feasible:
  * Which resource will implement that? Do they need training
  * Has this architectural pattern already implemented in the company, industry?
  * Which architectural spike might be needed to remove any high-level risks?
  * Do you understand the nature of sclaing the solution? (From the application server, database server, network perspective, queuing perspective)
  * Do you have  or can you build appropriate tool to monitor that?
  * Have you accounted for any reporting needs?
  * Are there any legal or regulatory issues (RAI)?
  * Do you understand when the project needs to be delivered?
  * How does this project align with the companis strategic long-term goals?
  * Does the project requires specific software packages?
*  __One way to increase the feasibility of the project to minimize the number of new things__ (services, technologies) you are injecting into the organization or to sequence thechanges in a stable manner. Note that, bringin in new things is fun, but trying to get them operationalized is nearly always a challenge.
*  Note that as an architect whenever you make any decision, __assess the risk__ and the __impact__ of that decision by asking the following questions (e.g., via KDD):
   * What are the operational implication of this decision?
   * Does it enable the project the meet it's delivery faster?
   * Will the quality of the project affected?
   * What are the maintenace cost of implication of this decision?
   * What are the development cost of this decision?
* Note that the number of risks associated with a project might be high. However, focus on the risks that has high chance of happening -such as a new technology failing to deliver, a dependent project failing to deliver. As an architect you should have the ability to plan on how to address them and which alternatives must be considred if and when they happen.
* Another way to manage the risk when you need to make a decision about the feasibility of a particualt architectural approach (i.e., using AI search in the chatbot). __Take time to do a time-bounded architectural spike (PoC)__.Also consider the following guidlines:
  * Place time-boundary on investigation
  * Understand the key elements that require investigation
  * Avoid the spike to turn into the real solution
  * Document the results
  * Get the right resources
 ## Communication
 * The thid key area of pragmatic architecture is comminication. The architect must have a __clear, consistent, and continuous__ communication. Note that, any communication gap will be filled by others so __stay on top of the comminication process ecven if you are extraordineray busy__.
 * Note that, when a software is built only a limited variability and flexibility can be realized. Take time to document decisions along the way. Ensure that the decision log is properly distributed so that there is no surprise.
 * Whenever you present alternatives as part of making decision, your ability to clearly articulate the avilable options and which one you recommend will produce a great starting point for a conversation. Don't be afraid of the solution you recommend and show you have a detaild analysis about that decision. __If the don't accepted your recommended solution, owns the one they recommend but make sure the decision makers understand the consequence of that decision (delay, cost, tech debt, etc.)__

# Chapter 12 Vision
* Vision drives how you think,what you do, how you do it.
* Vision is a mental image or state that represent the ideal __end state__ that can be used for focusing and aligning everyhting you seek to accomplish.
* In order to have vision, three elements need to be considered
    * Compelling Destination
    * Strategic Roadmap
    * Aligned Partners
 ## Finding and Establishing a Compelling Destination
 * In order to find a compelling destination for your vision, you need first to
    * __Discover your vision__: Vision is not something clear at the beginning, and it needs brainstorming. The following questions help to discover the vision:
      * Wrtie down your thouts and idead
      * Create a diagram that capture the essense of your ideas
      * Is the vision is to concrete or some level of ambiguity is there
      * Is there any particular problem you want to solve?
      * Is there any particular customer base you are trying to drill? Is it based on the customers you have today or is it something you want to move into adjacent market?
      * Is your vision realted to the overall company vision?
      * Is your vision related  to the product market or technology space? In general, the company vision can be devided into categories such as, product vision, technollogy vision, market vision, operation vision, etc
      * 
   * __Craft your compelling story from your vague facts__ : Developing a vision is hard becasue you need to combine what appear as independent facts and insights into a compelling story. Note that these facts and insights begin to emerge from spending time in fields you are passionate about. Note that when your compelling story start to emerge you need to consider the following questions
      * Does the emerging technology in industry relate to your vision?
      * What do you see as big opportunies and what are the risks?
      * Have you put them in a white paper or presenation?
    * __Overcomming the roadblocks__: You might encounter challenges in trying to formulate a vision. If you realize things are not coalsec (combine and comes as a whole), then consider the following questions. Also beware of the roadblocks as they emerge and priortize their importance against the overal goal you are trying to achieve:
      * What is the specific roadblock you are encountering?
      * Are there parts of the vision that you are avoiding?
      * How can you move or remove around the roadblock?
 ## Developing and Establishing a Strategic roadmap
 * After discovering and establishing your vision with full of context (compelling stories). The next step is to define a road map that takes you and your compeany from the current state to the ideal state you defined in your vision.
 ### Managing the route to the vision
 * When you look at the vision and the way to implement it, then it might be difficult. Try to do it step by step tworads the ideal state even if it is inaccurate as you will adjust it later. To build the roadmap try to consider the following questions:
   *  How big is your vision? Will it take 9 months to acheve or 9 years? Smaller visions is easier to impement and to get to the ideal state comapred to the larger vision which might be very ambigous. In contrast, larger visions will keep the momentum you created for the vision.
   *  what are the fundamental steps that need to be met before starting the road map. Try to complete them before the roadmap gets started
   *  What steps are easier to achieve? Sequence them at the beginning of the road map
   *  What steps are the most vague ones that need more research and investigations? Try to push them towards the end of the road map implementation to have more time to explore them
   *  Can you define the steps in shrtoly manner?
   *  Can you create a one page diagram?
 ## Stablishing the Strategies to Support the Vision
 * Strategies are the plan of actions that take you from the current state to the target ideal state in your vision. Note that, vision is the state but strategies are plannings.  When you develop strategies, the need to be well known and publicly availble to the teams that will be working on projects assocciated with your vision. The strategies allow the others to make decisions that are in alignment with your vision even if you are absent.
 * An example to explain the vision and strategy is:
   * Vision: To be the leader in sustainbale energy in the market
   * Strategy: __To become__ the most sustainble company in the market in the next 5 years by focusing R&D projects, partnership and aquisitions
 * When you develop strategies (Action plans), consider the following questions:
   * which pattern you want to repeat through your vision, strategy and design? (example, what repeatable patterns would you want in developing strategies for Data Science projects)
   * Is vision eveolving? If yes, what factors will affect it?
   * Are there enabling technologies that need development?
   * Is the open-source adoption a key element of your plan?
   * Do you need the required skill set to realize your vision? 
 ## Establishing Aligned Partners
 * In order to put your vision and developed strategies into action, you need to work on three elements:
   * __Alignments__: As your vision expand its boundaries, more people will be involved and aligning the steps of the developed roadmap with their concerns will help you to sell your vision. Consider to answer the following question (to other parties) if __expansion happnes__:
     * Where are your custoemrs trying to go?
     * What your customers willing to pay?
     * What is the current level of commitment within your organization toward the proposed vision? Do executives support it?
     * Who are the caretakers or the company's or division's vision? What are their thoughts in supporintg your vision?
     * Are there any areas that are out of missalignment? If yes, you might postone it in the vision to remove the obstacle.
     * Is your vision is relevant to the marketplace or is it out of date? If it is aged, what aspect of the market place is shifted? Is it because of a new technology? Customers? 
   * __Partnership__: From an __architecture perspective, architects rarely oen anything__. Instead, they only lever they apply to accomplish things is by establshment of vision. That is they craft convinsing story/visions around __product capabilites, development activites, and cosntrcution of a particular project or program__.
     * The reality of a vision can be significantly magnified if the partnerships can be made with key stakeholders since stakeholders often have political influence. The challenge is to find and partner with the right key stakeholders to support your vision and funding you need to move forward.
     *  
   * __Funding__: Establising a vision and developing the strategic roadmap is fun but without funding you can not realize your vision. When you partner with a key stakeholder to align with your vision and provide the support funding support, consider the folloqing questions:
     * Who needs to approve your vision to gain financial support?
     * Are you in the position to seek funding? If not who should you partner with?
     * What business value will be reached by realizing your vision? What is ROI?
     * How much time do you need to implement the vision? How much time to achieve the first step in the developed roadmap?
     * what is the minimum amount of investment you require to seed your vision?
         

