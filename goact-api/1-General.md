# General

The API is hosted in address test.goact.co, and all requests require HTTPS
protocol, so each resource address starts with **https://test.goact.co/mint/**.

## Conventions for responses

The following description applies to all URLs in the API. All resources return a
HTTP 200 response code if everything went well, and otherwise an appropriate
error code.

Response data is given in JSON format, unless otherwise noted.


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

Error identifier | HTTP Status | Description
-----------------|-------------|------------
not_found               | 404 | The requested resource was not found on server
bad_request             | 400 | The request contained invalid data (a more detailed error description may be included).
forbidden               | 403 | The given authorization credentials were not valid for this resource. The request did not contain (correct) authorization credentials.
unsupported_api_version | 410 | The version of API is no longer supported. You need you notify user to upgrade the client app.
server internal error   | 500 | The server encountered an internal error or misconfiguration and was unable to complete your request.


## Token types

Backend supports two token types: application and user tokens.

### 1. Application tokens

Application authentication is used for requests that do not require user
authentication, for example creating a new user. Token is provided in the
HTTP authorization header in following format:

```
Authorization: Application <application id>:<application secret>
```

### 2. User tokens

The user token is granted by backend to particular user using the flow described
below. Token is provided in the request uri in following format:

``` 
/mint/api/v1/auth/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
/mint/api/v1/user/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
/mint/api/v1/sleep/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
/mint/api/v1/sleep/summary/dbd4bc88-7f44-4cd7-b9f6-06db922e36c2
```

