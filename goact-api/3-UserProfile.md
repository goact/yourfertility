# User account related resources
 
## 1. Access User profile  ( GET /mint/api/v1/user/info/:access_token )

Return the current user profile data.

### Request 

**Authentication**
 
Request must be authenticated by Application specific token. goAct providers 'ApplicationToken' for yourfertilyt.org.au to access APIs .

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

The following error case descriptions apply for all API URLs. 

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



## 2. Update User profile ( PUT /mint/api/v1/user/info/:access_token)

Update all or some of the fields in user profile.

### Request  

The data to update is sent in request body in JSON format, as in the PUT request. id, username, created, and updated fields are ignored, if present.
Fields can be removed (cleared) by setting a field value to null in the request.

**Authentication**
 
Request must be authenticated by Application specific token. goAct providers 'ApplicationToken' for yourfertilyt.org.au to access APIs .

**Example Request** 

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X PUT -d '{"firstname":"Kate", "lastname":"Smith", "date_of_birth":"1981-03-05","sex":"male", "weight" : 65.5, "height" : 169.0, "password": "newpaswd!23"}' https://test.goact.co/mint/api/v1/user/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```

### Response

Fields that requested will be returned with. 
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
sex           | *Optional* Biological sex. Choises are "M", "F", and null.
weight        | *Optional* Weight in kilograms
height        | *Optional* Height in centimeters 
password      | *Optional* new password to change. Minimum length is 6. The client app should confirmed that new password is correct by double checking.   
created       | Timestamp of user creation
updated       | Timestamp of user profile update

 
**Example Error Response** 

The following error case descriptions apply for all API URLs. 

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




