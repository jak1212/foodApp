# foodApp
Initial Java for food app (name TBD)

Food App Backend Processing
0.	Overview
a.	This document details the backend processing for the Food App which is used to determine the global consensus on safety of ingredients within packaged food and drink. The details below describe the flow of data from a front end which is yet to be designed and built, to query formation to pull results from the underlying database, the design of that database, and the generation/delivery of the finalized report.
Version	Description	Date	Created By
1.0	Original document creation	6/14/2023	Jack Kelly
			

1.	Functionality
a.	Database Structure
i.	Food Ingredients and the findings regarding their level of safety for human consumption as determined by the following organizations are stored within a MongoDB instance:
1.	World Health Organization -
2.	United States of America FDA -
3.	European Union Food Health and Safety Administration -
4.	Health Canada -
5.	Japan Department of Agriculture -
6.	New Zealand Org -
b.	Class Structure
i.	Please refer to UML Diagrams:
1.	FoodApp package
a.	FoodAppItems subpackage: https://lucid.app/lucidchart/b401411e-4e4a-4317-891c-0371e2c474ae/edit?beaconFlowId=597171455ED5E7A4&invitationId=inv_12eadf87-a2eb-4e46-85fd-0f48c426eae4&page=0_0#
b.	FoodAppQuery subpackage
c.	FoodAppResults subpackage
c.	User Experience – Getting a Report
i.	Front end UX is to be developed. However, the net inputs to be gathered are as follows:
1.	Email address for delivery of report
2.	Food item name
ii.	The response for the Food item name retrieved from the user is then used to perform a lookup against the FoodItem table as follows:
1.	A FoodAppQuery item is instantiated where emailAddress = user email address input and foodItemName = food item name input. queryID is set as the next up number from the FoodAppQueryCounter
2.	The foodItemName is then queried against the FoodItem table as follows: select FoodItem.foodItemID from FoodItem where FoodItem.name = {{FoodAppQuery.foodItemName}}. The FoodItemQuery then tries to do the following:
a.	Catch no results: If this query yields no results, an email is generated to the user revealing that the query yielded no results, this result is recorded against FoodAppQuery.
b.	Else, the result is stored against the FoodAppQuery instance. continue process as described below. 
3.	With the retrieved foodItemID, the following query is executed to return the list of ingredients associated with the food item: Select FoodItem.ingredientList from FoodItem where FoodItem.FoodItemID = {{Retrieved Food Item ID}}.
a.	For each result, try query against the FoodIngredient table: select * from FoodIngredient where FoodIngredient.ID = {{retrieved food ingredient ID}}.
b.	If no result is found, catch NoIngredientResultException and save result as No Result for outbound report
c.	Else, the result is stored as a QueryResult in FoodAppQuery.Results. The result stores the JapanFinding, FDAFinding, CanadaFinding etc.
4.	With the completed array of FoodItemQuery.Results, each Result is then iterated through and fed into a .csv document containing these Strings.
5.	The .csv is then emailed to the FoodAppQuery.emailAddress
6.	FoodAppQuery.isCompleted = true


