
![goAct logo](https://media.licdn.com/mpr/mpr/shrink_200_200/AAEAAQAAAAAAAAPAAAAAJGJkMjgwNTRiLTRlYTYtNDA3YS05ZDM0LTRkMGNlYzE3NWY5Mw.png)


# [goAct](http://www.goact.co) webservice API documentation
 
**yourfertility.org.au** (the frontend)

Version: 1.0 Beta, 18th August, 2017
 
  


# Table of Contents
  1. [General](#chapter-1)
  1. [Authentication](#chapter-2)
  1. [User Account Resources](#chapter-3)
  1. [User Health Data Resources](#chapter-4)  


## [General](#chapter-1)

Documents the general conventions used throughout the API.

## [Authentication](#chapter-2)

All access to user data in goAct is authenticated with access tokens. This
chapter describes how authentication works and how access token is obtained.

## Resources

### [User Account Resources](#chapter-3)

This chapter describes the resources for retrieving user data.

### [User Health Data Resources](#chapter-4)

This chapter describes the resources for retrieving user health data.



----------------------------------------
<div class="pagebreak"></div> 

# 1. General<a id="chapter-1"></a>

The API is initially hosted at the address "test.goact.co", and all requests require the HTTPS
protocol, such that each resource address starts with **https://{subdomain}.goact.co/mint/**.

## Conventions for responses

The following description applies to all URLs in the API. All resources return a
HTTP 200 response code on success, otherwise an appropriate error code.

Response data is supplied in JSON format, unless otherwise noted.


## Conventions for error responses

The following error case descriptions apply for all API URLs.
In case of an error, a non-200 HTTP code is returned along with an error message
and human readable description:

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
```

Common error messages are listed in the table below. Error messages specific to
particular resources are described in their own sections.

Error identifier        | error | description
------------------------|-------|------------
not_found               |   404 | The requested resource was not found on the server
bad_request             |   400 | The request contained invalid data (a more detailed error description may be included).
forbidden               |   403 | The given authorization credentials were not valid for this resource. The request did not contain (correct) authorization credentials.
unsupported_api_version |   410 | The version of API is no longer supported. You need you notify user to upgrade the client app.
server internal error   |   500 | The server encountered an internal error or misconfiguration and was unable to complete your request.


## Http status

Common HTTP status messages are listed in the table below. Error messages specific to
particular resources are described in their own sections. 

Http status | description 
----|---------------------
404 | The requested resource was not found on server
500 | The server encountered an internal error or misconfiguration and was unable to complete your request.


## Token types

The goAct backend supports two token types: application and user tokens.

### 1. Application tokens

Application authentication is used for requests that do not require user
authentication, for example creating a new user. A token is provided in the
HTTP authorization header in the following format:

```
Authorization: Application <application id>:<application secret>
```

### 2. User tokens

The user token is granted by the backend to each user using the flow described
below. The token is provided in the request uri in following format:

``` 
/mint/api/v1/auth/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
/mint/api/v1/user//info/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
/mint/api/v1/health/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2 
```


----------------------------------------
<div class="pagebreak"></div> 

# 2. Authorization and Authentication<a id="chapter-2"></a>


The goAct API utilizes the OAuth2 as authentication protocol. In addition to
standard OAuth2 username/password authentication. The backend provides the ability
to authenticate as an application. For security purposes authentication information 
must be sent to the backend within every request in the Authorization HTTP header.

A frontend application must never store goAct username or password details.

Instead, exchange these for a unique user access token, which is used to authenticate
all subsequent requests with the goAct backend.


## 1. User registration (POST /mint/api/v1/auth/register)

This flow is used for end user sign-up (registers) to the web app from the frontend. 
  
### Request 

**Authentication**

Request must be authenticated by an Application specific token. goAct provides the 'ApplicationToken' for the frontend to access APIs.

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"grant_type" : "signup", "firstname":"Kate", "lastname":"Smith", "username":"kc@goact.com.au","password":"xyz"}' https://test.goact.co/mint/api/v1/auth/register
```

application/json field | Value
-----------|------
grant_type | signup
firstname  | _User's first name_
lastname   | _User's last name_
username   | _User's username, typically the email address_
password   | _User's password, passwords length should be more than 5 characters_
 
### Response 

**Example Response**

```javascript
{ 
    "user" : 1231,
    "token_type" : "user", 
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "expires_in" : 3600
}
```

Property     | Meaning
-------------|--------  
user         | Id of the associated user. This would be 'vistor id' for Analytics capture.
token_type   | The token type, currently supported type is "user"
access_token | The access token to be used in URI of the requests. This token should be set to web browser for _exprires_in_ in order for web-tool to access to goAct APIs. 
expires_in   | *Optional* In how many seconds the token expires. *Default* If missing, the token is not set to expire.

If the username already exists, 'Error identifier' 403 error is
returned. In this case, the client application should ask the user to
sign-up with different username again or log in with same username.


**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
```
 
 HTTP Status | Error identifier | Description
-------------|------------------|------------
200          | 403              | The username has already existed

 

##  2. User authentiction (POST /mint/api/v1/auth/authorize) 

This flow is used where the end user has trusted her password with the frontend. 

### Request

**Authentication** 

Request must be authenticated by Application specific token. goAct provides the 'ApplicationToken' for the frontend to access APIs .

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"grant_type" : "password", "username":"kc@goact.com.au","password":"xyz"}' https://test.goact.co/mint/api/v1/auth/authorize
```


application/json field | Value
-----------------------|------
grant_type             | password
username               | _User's username, typically the email address_
password               | _User's password_


### Response

**Example Response**

```javascript
{ 
    "user" : 1231,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "user", 
    "expires_in" : 3600
}
```

Property     | Meaning
-------------|--------  
user         | Id of the associated user. This would be 'vistor id' for Analytics capture.
token_type   | The token type, currently supported type is "user"
access_token | The access token to be used in URI of the requests. This token should be set to web browser for _exprires_in_ in order for web-tool to access to goAct APIs. 
expires_in   | *Optional* In how many seconds the token expires. If missing, the token is not set to expire.

If the token has already expired, or was otherwise cancelled, 'Error identifier' 404 error is
returned. In this case, the client application should ask the user to authenticate again.


**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
``` 

 HTTP Status |Error identifier | Description
-------------|-------------|------------
200          | 404         | The requested resource was not found on server 
200          | 404         | The password does not match, suggest reset? 




## 3. Check user session time ( GET /mint/api/v1/auth/:access_token ) 

Get information about the access token used in the request and extend user session time in the goAct application.

### Request  

**Authentication** 

Request must be authenticated by Application specific token. goAct provides the 'ApplicationToken' for yourfertilyt.org.au to access APIs .

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET https://test.goact.co/mint/api/v1/auth/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```

### Response

**Example Response**

```javascript
{ 
    "user" : 1231,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "user", 
    "expires_in" : 3600
}
```

Property     | Meaning
-------------|--------  
user         | Id of the associated user. This would be 'vistor id' for Analytics capture.
token_type   | The token type, currently supported type is "user"
access_token | The access token to be used in URI of the requests. This token should be set to web browser for _exprires_in_ in order for web-tool to access to goAct APIs. 
expires_in   | *Optional* In how many seconds the token expires. *Default* If missing, the token is not set to expire.

If the token has already expired, or otherwise cancelled, 'Error identifier' 404 error is
returned. In this case, the client application should ask the user to authenticate again.


**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
``` 

 HTTP Status |Error identifier | Description
-------------|-----------------|------------
200          |             400 | The request contained invalid or expried 'access token' (a more detailed error description may be included).
200          |             404 | The requested resource was not found on server   



## 4. User request new password ( POST /mint/api/v1/auth/request_new_password )

This flow is used when the end user requests a new password from the frontend . 

### Request  

**Authentication** 

Request must be authenticated by Application specific token. goAct provides the 'ApplicationToken' for the frontend to access APIs .

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"grant_type" : "reset", "username":"aaa9@goact.com.au"}' https://test.goact.co/mint/api/v1/auth/request_new_password
```

application/json field | Value
-----------------------|------
grant_type             | reset 
username               | _User's username, typically the email address_ 


### Response  

**Example Response**

```javascript
{ 
    "user" : 1231,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "user", 
    "sent" : true 
}
```

Property     | Meaning
-------------|--------  
user         | Id of the associated user. This would be 'vistor id' for Analytics capture.
token_type   | The token type, currently supported type is "user"
access_token | The access token to be used in URI of the requests. This token should be set to web browser for _exprires_in_ in order for web-tool to access to goAct APIs. 
sent         | Email sent. If false, the server encountered an unexpected error while sending email.    

If the username doesn't exist, 'Error identifier ' 404 error is returned. 
In this case, the client application should ask the user to enter correct username again.


**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
``` 

HTTP Status | Error identifier  | Description
------------|-------------------|------------
200         | 404               | The requested resource was not found on the server  


  
----------------------------------------
<div class="pagebreak"></div> 

# 3. User Account Resources<a id="chapter-3"></a>


## 1. Attain Visitor ID  ( GET /mint/api/v1/user/visitorId )

Return the unique visitor ID for anonymous user.
This would be 'ID' for Analytics capture.

### Request 

**Authentication**
 
Request must be authenticated by Application specific token. goAct provides the 'ApplicationToken' for the frontend to access APIs .

**Example Request** 

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET  https://test.goact.co/mint/api/v1/user/visitorId
```


### Response

**Example Response**

```javascript
{  
  "visitorid" : "dbd4bc88-7f44-4cd7-b9f6-06db922e36c2" 
}
```
 
**Fields** 

Fields that belong to the user account

Field         | Description
--------------|-------- 
visitorid     | User's visitor ID **Must not be null**  
 
**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
``` 

 HTTP Status |Error identifier  | Description
-------------|------------------|------------
200          |              404 | The requested resource was not found on server 
200          |              403 | The given authorization credentials were not valid for this resource. The request did not contain (correct) authorization credentials.
200          |              500 | The server encountered an internal error or misconfiguration and was unable to complete your request.




## 2. Access User profile  ( GET /mint/api/v1/user/info/:access_token )

Return the current user profile data.

### Request 

**Authentication**
 
Request must be authenticated by Application specific token. goAct provides the 'ApplicationToken' for the frontend to access APIs .

**Example Request** 

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET  https://test.goact.co/mint/api/v1/user/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```


### Response

**Example Response**

```javascript
{  
  "username" : "kc@goact.com.au",
  "firstname" : "Kate",
  "lastname" : "Smith",
  "date_of_birth" : "1981-03-05",
  "sex" : "male",
  "weight" : 65.5,
  "height" : 169.0, 
  "created" : 1371472503.646541,
  "updated" : 1371492826.623422
}
```

If the field value is not set, it is **null**.


**Fields** 

Fields that belong to the user account

Field         | Description
--------------|-------- 
username      | User's email address. **Must not be null**
firstname     | User's firstname.
lastname      | User's lastname.
date_of_birth | *Optional* User's date of birth.
sex           | *Optional* Biological sex. Choises are "M", "F", and null.
weight        | *Optional* Weight in kilograms
height        | *Optional* Height in centimeters 
created       | Timestamp of user creation
updated       | Timestamp of user profile update
 
 
**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
``` 

 HTTP Status |Error identifier  | Description
-------------|------------------|------------
200          |              404 | The requested resource was not found on server 
200          |              400 | The request contained invalid data (a more detailed error description may be included).
200          |              403 | The given authorization credentials were not valid for this resource. The request did not contain (correct) authorization credentials.
200          |              500 | The server encountered an internal error or misconfiguration and was unable to complete your request.



## 3. Update User profile ( PUT /mint/api/v1/user/info/:access_token)

Update all or some of the fields in user profile.

### Request  

The data to update is sent in request body in JSON format, as in the PUT request. id, username, created, and updated fields are ignored, if present.
Fields can be removed (cleared) by setting a field value to null in the request.

**Authentication**
 
Request must be authenticated by Application specific token. goAct provides the 'ApplicationToken' for the frontend to access APIs .

**Example Request** 

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X PUT -d '{"firstname":"Kate", "lastname":"Smith", "date_of_birth":"1981-03-05","sex":"male", "weight" : 65.5, "height" : 169.0, "password": "newpaswd!23"}' https://test.goact.co/mint/api/v1/user/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```

### Response

Fields that are requested will be returned. 
Returns the updated user profile, see PUT request.

**Example Response**

```javascript
{ 
  "username" : "kc@goact.com.au",
  "firstname" : "Kate",
  "lastname" : "Smith",
  "date_of_birth" : "1981-03-05",
  "sex" : "male",
  "weight" : 65.5,
  "height" : 169.0, 
  "created" : 1371472503.646541,
  "updated" : 1371492826.623422
}
```

If the field value is not set, it is **null**.


**Fields** 

Fields that belong to the user account

Field         | Description
--------------|-------- 
username      | *Optional* User's email address. **Must not be null**
firstname     | *Optional* User's firstname.
lastname      | *Optional* User's lastname.
date_of_birth | *Optional* User's date of birth.
sex           | *Optional* Biological sex. Choices are "M", "F", and null.
weight        | *Optional* Weight in kilograms
height        | *Optional* Height in centimeters 
password      | *Optional* new password to change. Minimum length is 6. The client app should confirm that new password is correct by double checking.   
created       | Timestamp of user creation
updated       | Timestamp of user profile update

 
**Example Error Response** 

The following error case descriptions apply for this API URL. 

```javascript
{
    "error" : "error_identifier_string",
    "description" : "Human readable description of the error"
}
``` 

 HTTP Status |Error identifier  | Description
-------------|------------------|------------
200          |              404 | The requested resource was not found on server 
200          |              400 | The request contained invalid data (a more detailed error description may be included).
200          |              403 | The given authorization credentials were not valid for this resource. The request did not contain (correct) authorization credentials.
200          |              500 | The server encountered an internal error or misconfiguration and was unable to complete your request.



----------------------------------------
<div class="pagebreak"></div> 

# 4. User Health Data Resources<a id="chapter-4"></a>
  

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
 









