# Authorization and authentication


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


  
