# User account related resources
 
## 1. GET /mint/api/v1/user/info/:access_token

Return the current user profile data.

### Authentication

Request must be authenticated by Application specific token.

### Example Request

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET  https://test.goact.co/mint/api/v1/user/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```
### Example Response

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

### Fields

Fields that belong to the user account

Field | Description
---------|--------
id | Unique and permanent user id.
username | User's email address. **Must not be null**
firstname | User's firstname.
lastname | User's lastname.
date_of_birth | *Optional* User's date of birth.
sex | *Optional* Biological sex. Choises are "M", "F", and null.
weight | *Optional* Weight in kilograms
height | *Optional* Height in centimeters 
created | Timestamp of user creation
updated | Timestamp of user profile update


## 2. PUT /mint/api/v1/user/info/:access_token

Update all or some of the fields in user profile.

### Authentication

Request must be authenticated by Application specific token.


### Fields

Fields that can update to the user account.

Field | Description
---------|-------- 
firstname | *Optional* User's firstname.
lastname | *Optional* User's lastname.
date_of_birth | *Optional* User's date of birth(YYYY-MM-DD).
sex | *Optional* Biological sex. Choises are "male", "female", and null.
weight | *Optional* Weight in kilograms
height | *Optional* Height in centimeters  
password | *Optional* new password to change. Minimum length is 6. The native app should confirmed that new password is correct by double checking.   

 
### Request

The data to update is sent in request body in JSON format, as in the GET
request. id, username, created, and updated fields are ignored, if present.

Fields can be removed (cleared) by setting a field value to null in the request.

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X PUT -d '{"firstname":"Kate", "lastname":"Smith", "date_of_birth":"1981-03-05","sex":"male", "weight" : 65.5, "height" : 169.0, "password": "newpaswd!23", "deviceid" : "bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P" }' https://test.goact.co/mint/api/v1/user/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```

### Response

Fields that requested will be returned with.

Returns the updated user profile, see GET request.

 



