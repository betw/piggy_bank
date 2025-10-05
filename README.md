# Original Concept
### concept TripCostEstimation [User]
* **purpose** generate realistic cost estimates based on trip details
* **principle** based on a user's choice of initial dpearture city and arrival city, and the user's sheltering accommodations and food location preferences, an estimate is provided
* that reflects the aforementioned
* **state**
    * a set of Users with
      * a set of **TravelPlans**
    * a set of Locations with
       * a String city
    * a set of **TravelPlans** with
        * a `fromCity` **Location**
        * a `toCity` **Location**
        * a `fromDate` **Date**
        * a `toDate` **Date**
        * a `necessity' **Necessity**
    * a set of **Necessities** with
        * an `accommodation` **Bolean**
        * a `diningFlag` **Boolean**
* **actions**
    *  createTravelPlan(user: User, fromCity: Location, toCity: Location, fromDate: Date, toDate: Date): (travelPlan: TravelPlan)
         * **requires** fromCity and toCity exists and toDate >= fromDate and both are greater than the current date 
         * **effect** create and return a travelPlan with a fromCity, toCity, and from and to dates, and a default necessity (accommodation = True indicating planning to save for rooming costs, diningFlag = True indicating planning to save for food costs))
    * deleteTravelPlan(user: User, travelPlan: TravelPlan)
         * **requires** travelPlan exists and belongs to user
         * **effect** delete the travelPlan
    *  updateNecessity(user: User, travelPlan: TravelPlan, accommodation: Boolean, diningFlag: Boolean): (travelPlan: TravelPlan, necessity: Necessity)
         * **requires** travelPlan exists and belongs to user, accommodation exists as one of the livingSpaces and diningFlag indicates whether the user eats out most of the time (1) or eats homecooked meals (0)
         * **effect** create and add the necessity with accommodation and diningFlag to travelPlan
    * resetNecessity(user: User, travelPlan: TravelPlan)
         * **requires** travelPlan exists and belongs to user
         * **effect** reset the necessity belonging to travelPlan to the default as described in the action createTravelPlan
    * `estimateCost (user: User, travelPlan: TravelPlan): (totalCost: Number)`
         * **requires** travelPlan exists and belongs to user
         * **effect** based on the departure, arrival dates, necessities and departure and arrival locations, gives an estimated cost of the plan, totalCost
# AI-Augmented Concept
<concept_spec>
concept TripCostEstimation

purpose
generate realistic cost estimates based on trip details, using AI for data retrieval and calculation

principle
based on a user's choice of initial departure city and arrival city, and the user's sheltering accommodations and food location preferences, an estimate is provided; the LLM is used to search for and calculate median cost ranges based on the provided necessities.

state
* a set of Users with
   * a set of TravelPlans
* a set of Locations with
   * a city String
* a set of TravelPlans with
   * a fromCity Location
   * a toCity Location
   * a fromDate Date
   * a toDate Date
   * a necessity Necessity
* a set of Necessities with
   * an accommodation Boolean // true for saving for rooms, false for not
   * a diningFlag Boolean // true for saving for eating out, false for not
* a set of CostEstimates with
   * a travelPlanID String
   * a flight Number // estimated total round-trip flight cost in USD
   * a roomsPerNight Number // estimated cost per night in USD
   * a foodDaily Number // estimated cost per day in USD
   * a lastUpdated Date // tracking when the estimate was generated

actions
* createTravelPlan(user: User, fromCity: Location, toCity: Location, fromDate: Date, toDate: Date): (travelPlan: TravelPlan)
   * **requires** fromCity and toCity exists and toDate >= fromDate and both are greater than the current date
   * **effect** create and return a travelPlan with a fromCity, toCity, and from and to dates, and a default necessity (accommodation = true, diningFlag = true)

* deleteTravelPlan(user: User, travelPlan: TravelPlan)
    * **requires** travelPlan exists and belongs to user
    * **effect** delete the travelPlan and any associated CostEstimates

* updateNecessity(user: User, travelPlan: TravelPlan, accommodation: Boolean, diningFlag: Boolean): (travelPlan: TravelPlan, necessity: Necessity)
    * **requires** travelPlan exists and belongs to user, accommodation exists as one of the livingSpaces and diningFlag indicates whether the user plans to save for eating out (true) or not (false)
    * **effect** create and add the necessity with accommodation and diningFlag to travelPlan

* resetNecessity(user: User, travelPlan: TravelPlan)
    * **requires** travelPlan exists and belongs to user
    * **effect** reset the necessity belonging to travelPlan to the default as described in the action createTravelPlan

* **async generateAICostEstimate(user: User, travelPlan: TravelPlan, llm: GeminiLLM): (costEstimate: CostEstimate)**
    * **requires** travelPlan exists and belongs to user
    * **effect** retrieves trip details (dates, locations) and necessity preference (accommodation, dining) and uses the llm's specialized tool (e.g., Google Search/Flights/Hotels) to calculate and return the median cost estimates for flight, rooms\_per\_night, and food\_daily; the resulting data is stored as a new CostEstimate associated with the travelPlanID.
    * **note** the LLM prompt will be specifically tailored to search for accommodation prices matching the `accommodation` Boolean (e.g., true for hotel/motel costs) and food costs based on the `diningFlag` (true for "restaurant costs," false for "no food costs"). If the LLM fails to provide an estimate for any reason or the costs are widely inaccurate (less than 50, more than 100000 for example) then the user can manually enter the total cost of the trip that they plan to save for.

* estimateCost (user: User, travelPlan: TravelPlan): (totalCost: Number)
    * **requires** travelPlan exists and belongs to user and an associated CostEstimate exists
    * **effect** calculates and returns the totalCost by multiplying the estimated daily/nightly costs by the duration and adding the flight cost.
notes
The LLM augmentation moves the burden of research and calculation from the user to the LLM. The original estimateCost action is now strictly a calculation of the stored AI-generated or manually-inputted estimate. A manual fallback for cost input will still be available in the final implementation.

