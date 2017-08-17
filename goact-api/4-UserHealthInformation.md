# Health Information related User account
  

## 1. POST /mint/api/v1/user/health/:visitor_id

Store Persional Health Information(PHI) fields into database.
This api should be call when user logged in and access to monitor page.

### Authentication

Request must be authenticated by Application specific token.

### Example Request

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"accessType" : "add", "accessToken":"51b946bac50f459051f85713d716cb07", "accessToken":"LWtZp5evX5KcEvvi_xvdISRkLgY=bw7s2IdY42fb85ea06c98a63d44c7a41fea208a8540a661f8ad9328c7915527c8c9d402234d1bb5c76efd98c126d098f16adf1aa5004fb865245112d24f4e3f3ecdb3d6874efba9c1e9954ceb2eaf85f531f624505cbdd0667e7af463a82de224004bb6475a6fa13a4b6308e702f592d5a6b104e", "publicToken" : "9fa02df6631b81bf3f2405a642592b8b" }'  https://test.goact.co/mint/api/v1/health/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```
### Example Response 1 if user health information has already been added.

```javascript
{ 
  "accessType" : "add",
  "accessToken" : "51b946bac50f459051f85713d716cb07",
  "firstName" : "Firstname",
  "lastName" : "Lastname",
  "age" : 33,
  "sex" : "F",
  "height" : 178, 
  "weight" : 75,
  "bmi" : 75,
  "smoking" : 75,
  "alcoholConsumption" : 75 
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
firstName | *Optional* User's firstname.
lastName | *Optional* User's lastname.
age | *Optional* User's age.
sex | *Optional* Biological sex. Choises are "M" for male, "F" for female or null.
weight | *Optional* Weight in kilograms
height | *Optional* Height in centimeters  
smoking | *Optional* Cigarettes per day. 
alcoholConsumption | *Optional* Standard drinks per week.

 



## 2. GET /mint/api/v1/user/health/:access_token

Fetch Persional Health Information(PHI) fields from goAct database.
This api should be call when user logged in and access to monitor page.

### Authentication

Request must be authenticated by Application specific token.

### Example Request

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET https://test.goact.co/mint/api/v1/health/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```
### Example Response 1 if user has previous health information in goAct database.

```javascript
{    
  "firstName" : "Firstname",
  "lastName" : "Lastname",
  "accessToken" : "51b946bac50f459051f85713d716cb07",
  "factors" :[
  {"added" : "2017-02-21 13:34:23",
  "age" : 33,
  "sex" : "F",
  "height" : 178, 
  "weight" : 75,
  "bmi" : 75,
  "smoking" : 75,
  "alcoholConsumption" : 75},
  {"added" : "2017-05-21 13:34:23",
  "age" : 33,
  "sex" : "F",
  "height" : 178, 
  "weight" : 75,
  "bmi" : 75,
  "smoking" : 75,
  "alcoholConsumption" : 75}]
}
```

### Example Response 2 if user hasn't have previous health information in goAct database yet.

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
firstName | *Optional* User's firstname.
lastName | *Optional* User's lastname.
accessToken | goAct access token for this user. **Must not be null**.
added | *Optional* date added (YYYY-MM-DD HH:MM:SS).
age | *Optional* User's age.
sex | *Optional* Biological sex. Choises are "M" for male, "F" for female or null.
weight | *Optional* Weight in kilograms
height | *Optional* Height in centimeters  
smoking | *Optional* Cigarettes per day. 
alcoholConsumption | *Optional* Standard drinks per week.


### Rule for visitor_id
When communicating with goAct api, it must use visotor_id from analytics tool cookie.
 







