# Authorization and authentication

 goAct API utilizes the OAuth2 as authentication protocol. In addition to
standard OAuth2 username/password authentication, backend provides the ability
to authenticate as an application. Authentication information must be sent to
backend within every request, in the Authorization HTTP header.

You must never store  goAct user's username or password in your application.
Instead, you exchange them to the access token, which is used to authenticate
all requests.


## 1. User registration 

This flow is used in a case when the end user sign up with the client app. 

### POST /mint/api/v1/auth/register

application/json field | Value
----------|------
grant_type | signup
firstname | _User's first name_
lastname | _User's last name_
username | _User's username, typically the email address_
password | _User's password, passwords length should be more than 5 characters_
deviceid | _Device id for receiving notification Firebase notification(FCM) or Web site url _  

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"grant_type" : "signup", "firstname":"Kate", "lastname":"Smith", "username":"kc@goact.com.au","password":"xyz", "deviceid" : "bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P"}' https://test.goact.co/mint/api/v1/auth/register
```

**Example Response**

```javascript
{ 
    "user" : 1231,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "user",
    "redirect_after_login" : "https://test.goact.co/mint/healthTracking.seam",
    "expires_in" : 3600
}
```

Property | Meaning
------|--------  
user | Id of the associated user
access_token | The access token to be used in Authorization header of the requests
token_type | The token type, currently supported type is "user"
redirect_after_login | *Optional* If not missing, the native app should redirect to the url.
expires_in | *Optional* In how many seconds the token expires. If missing, the token is not set to expire.

If the username has already existed, HTTP 403 error is
returned. In this case, the client application should ask the user to
sign-up with different username again or log in with same username.


**Errors**

Error identifier | HTTP Status | Description
-----------------|-------------|------------
403 | 403 | The username has already existed

 

##  2. User authentication 

This flow is used in a case where the end user has trusted her password with the
client app. It conforms to OAuth2 specification section
http://tools.ietf.org/html/rfc6749#section-4.3

### POST /mint/api/v1/auth/authorize

application/json field | Value
----------|------
grant_type | password
username | _User's username, typically the email address_
password | _User's password_

**Authentication**

No authentication headers are required.

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"grant_type" : "password", "username":"kc@goact.com.au","password":"xyz"}' https://test.goact.co/mint/api/v1/auth/authorize
```

**Example Response**

```javascript
{ 
    "user" : 1231,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "user",
    "redirect_after_login" : "https://test.goact.co/mint/healthTracking.seam",
    "expires_in" : 3600
}
```

Property | Meaning
------|--------  
user | Id of the associated user
access_token | The access token to be used in Authorization header of the requests
token_type | The token type, currently supported type is "user"
redirect_after_login | *Optional* If not missing, the native app should redirect to the url.
expires_in | *Optional* In how many seconds the token expires. If missing, the token is not set to expire.

If the token is already expired, or otherwise cancelled, HTTP 403 error is
returned. In this case, the client application should ask the user to
authenticate again.


**Errors**

Error identifier | HTTP Status | Description
-----------------|-------------|------------
404 | 404 | The requested resource was not found on server 
404 | 404 | The password does not match, suggest reset? 

 

## 3. Checking access token information

### GET /mint/api/v1/auth/:access_token

Get information about the access token used in the request.

**Authentication**

Request must be authenticated by User specific token.

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X GET https://test.goact.co/mint/api/v1/auth/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```

**Example Response**

```javascript
{
  "user" : 1231,
  "redirect_after_login" : "https://test.goact.co/mint/healthTracking.seam",
  "token_type": "user"
}
```

Please see previous section for explanations of the access token properties.

 
**Errors**

Error identifier | HTTP Status | Description
-----------------|-------------|------------
404 | 404 | The requested resource was not found on server  
 

## 4. User request new password 

This flow is used in a case when the end user request new password in the client app. 

### POST /mint/api/v1/auth/request_new_password

application/json field | Value
----------|------
grant_type | reset 
username | _User's username, typically the email address_ 

**Validation**

Must be validated for 'grant_type' and 'username' before requesting it.

**Example Request**

```sh
curl -i -H "Content-Type: application/json" -H "Authorization: ApplicationToken 1YotnFZsEjr1zCsicMWpAAFSa" -X POST -d '{"grant_type" : "reset", "username":"aaa9@goact.com.au"}' https://test.goact.co/mint/api/v1/auth/request_new_password
```

**Example Response**

```javascript
{ 
    "user" : 1231,
    "access_token" : "2YotnFZFEjr1zCsicMWpAA",
    "token_type" : "user" 
}
```

Property | Meaning
------|--------  
user | Id of the associated user
access_token | The access token to be used in Authorization header of the requests
token_type | The token type, currently supported type is "user"  

If the username hasn't existed, HTTP 400 error is
returned. In this case, the client application should ask the user to enter correct username again.


**Errors**

Error identifier | HTTP Status | Description
-----------------|-------------|------------
404 | 404 | The requested resource was not found on server  


  
