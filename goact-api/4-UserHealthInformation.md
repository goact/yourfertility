# Health Information related User account
  

## 1. POST /mint/api/v1/user/health/:visitor_id

Store Persional Health Information (PHI) fields into the goAct database.
This api should be called when a user has logged in.

### Authentication

Request must be authenticated by an Application specific token.

### Example Request

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"accessType" : "add","accessToken" : "51b946bac50f459051f85713d716cb07", "firstName" : "Firstname", "lastName" : "Lastname", "age" : 33, "sex" : "F", "height" : 178, "weight" : 75, "bmi" : 75, "smoking" : 75, "alcoholConsumption" : 75 , "conceiveTry" : 75, "conceiveTryMonthly" : 75, "healthyBaby" : true, "sti" : true, "stiPositive" : false, "menstruation" : true, "havingSex" : 10, "havingSexMultiple" : 'unsure', "contraception" : true, "medicalConditions" : ["Diabetes","Endometriosis"] }'  https://test.goact.co/mint/api/v1/health/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```
### Example Response 1 if user health information has already been added.

```javascript
{ 
  "accessType" : "add",
  "accessToken" : "51b946bac50f459051f85713d716cb07", 
  "age" : 33,
  "sex" : "F",
  "height" : 178, 
  "weight" : 75,
  "bmi" : 75,
  "smoking" : 75,
  "alcoholConsumption" : 75 ,
  "conceiveTry" : 75,
  "conceiveTryMonthly" : 75,
  "healthyBaby" : true,
  "sti" : true,
  "stiPositive" : false,
  "menstruation" : true,
  "havingSex" : 10,
  "havingSexMultiple" : 'unsure',
  "contraception" : true,
  "medicalConditions" : ["Diabetes","Endometriosis"] 
}
```

### Example Response 2 if user hasn't been added .

```javascript
{ 
  "error" : "500",
  "description" : "The server encountered an unexpected error"
}
```

If the field value is not set, it is **null**.

### Fields

Fields that belong to the user account

Field | Description
---------|-------- 
accessType  | 'new' or 'add'. **Must not be null**. new - for new visitor who has no cookie. add - for visitor who has cookie or logged in user.
accessToken | goAct access token for this user. **Must not be null** if accessType is 'add'. Response **Must not be null** . For anonymous user it should be set with visitor ID
age | *Optional* User's age.
sex | *Optional* Biological sex. Choises are "M" for male, "F" for female or null.
weight |   Weight in kilograms
height |   Height in centimeters  
smoking | Response for Cigarettes per day. 
alcoholConsumption | Response for Standard drinks per week.
conceiveTry | Response for Are you trying to conceive?.
conceiveTryMonthly | *Optional* Response for How many months have you been trying to conceive?
healthyBaby | Response for Have you previously had a healthy baby?
sti | Response for Have you been tested for Sexually Transmitted Infections (STI)?
stiPositive | *Optional* Response for Did you test positive for an STI? 
menstruation | *Optional* Response for Are you having fewer than 9 menstrual cycles per year or menstrual cycles longer than 35 days?
havingSex | Response for How often you have sex during a given month?
havingSexMultiple | Response for Are you having sex multiple times at the right time of the month? 
contraception | Response for Are you currently using contraception?
contraception | Response for Do you have any of the following medical conditions?

## 2. GET /mint/api/v1/user/health/:access_token

Fetch Persional Health Information (PHI) fields from the goAct database.
This API should be call when a user is logged in.

### Authentication

Request must be authenticated by Application specific token.

### Example Request

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET https://test.goact.co/mint/api/v1/health/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```
### Example Response 1 if user has previous health information in the goAct database.

```javascript
{     
  "accessToken" : "51b946bac50f459051f85713d716cb07",
  "factors" :[
  {"added" : "2017-02-21 13:34:23",
   "age" : 33,
   "sex" : "F",
   "height" : 178, 
   "weight" : 75,
   "bmi" : 23.5,
   "smoking" : 75,
   "alcoholConsumption" : 75 ,
   "conceiveTry" : 75,
   "conceiveTryMonthly" : 75,
   "healthyBaby" : true,
   "sti" : true,
   "stiPositive" : false,
   "menstruation" : true,
   "havingSex" : 10,
   "havingSexMultiple" : 'unsure',
   "contraception" : true,
   "medicalConditions" : ["Diabetes","Endometriosis"] },
  {"added" : "2017-05-21 13:34:23",
   "age" : 33,
   "sex" : "F",
   "height" : 178, 
   "weight" : 77,
   "bmi" : 29.2,
   "smoking" : 75,
   "alcoholConsumption" : 35 ,
   "conceiveTry" : 55,
   "conceiveTryMonthly" : 25,
   "healthyBaby" : true,
   "sti" : true,
   "stiPositive" : false,
   "menstruation" : true,
   "havingSex" : 20,
   "havingSexMultiple" : 'unsure',
   "contraception" : true,
   "medicalConditions" : ["Diabetes"] }]
}
```

### Example Response 2 if user doesn't have previous health information in the goAct database yet.

```javascript
{ 
  "accessToken" : "51b946bac50f459051f85713d716cb07"
}
```

If the field value is not set, it is **null**.

### Fields

Fields that belong to the user account

Field | Description
---------|--------   
accessToken | goAct access token for this user. **Must not be null**.
added | *Optional* date added (YYYY-MM-DD HH:MM:SS).
age | *Optional* User's age.
sex | *Optional* Biological sex. Choises are "M" for male, "F" for female or null.
weight | *Optional* Weight in kilograms
height | *Optional* Height in centimeters  
smoking | Response for Cigarettes per day. 
alcoholConsumption | Response for Standard drinks per week.
conceiveTry | Response for Are you trying to conceive?.
conceiveTryMonthly | *Optional* Response for How many months have you been trying to conceive?
healthyBaby | Response for Have you previously had a healthy baby?
sti | Response for Have you been tested for Sexually Transmitted Infections (STI)?
stiPositive | *Optional* Response for Did you test positive for an STI? 
menstruation | *Optional* Response for Are you having fewer than 9 menstrual cycles per year or menstrual cycles longer than 35 days?
havingSex | Response for How often are you have sex during a given month?
havingSexMultiple | Response for Are you having sex multiple times at the right time of the month? 
contraception | Response for Are you currently using contraception?
contraception | Response for Do you have any of the following medical conditions?


### Rule for visitor_id
When communicating with goAct api, it must use visotor_id from analytics tool cookie.
 







