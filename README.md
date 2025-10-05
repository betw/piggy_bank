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

