API Documentation Overview

This document serves as the central hub for the API documentation covering the login, registration, and key generation functionalities of the website. Each section below links to detailed notes on each endpoint, showcasing request and response formats.

- Endpoints
	- Register Endpoint
	- Login Endpoint
	- Key Generation Endpoint
	- User Information Endpoint
	- Claim Key Endpoint

# Register Endpoint

- Endpoint Information
	- URL: /register
	- Method: POST

## Description
Registers a new user by checking the uniqueness of username and email, and the validity of the authentication key.

Request Format
``` json
{
  "type": "register",
  "username": "string",
  "email": "string",
  "password": "string",
  "authKey": "string"
}
```

Responses
- Title: Success
 ```json
  {
    "status": "success",
    "message": "Registration successful."
  }
```

- Title: Error
```json
  {
    "status": "error",
    "message": "Username already exists."
  }
  {
    "status": "error",
    "message": "Email already exists."
  }
  {
    "status": "error",
    "message": "Invalid or expired auth key."
  }
  {
    "status": "error",
    "message": "Missing registration fields."
  }
```


# Login Endpoint

- Endpoint Information
	- URL: /login
	- Method: POST

## Description
Authenticates a user by checking provided username and password against stored credentials.

Request Format
```json
{
  "type": "login",
  "username": "string",
  "password": "string"
}
```

Responses
- Title: Success

```json
  {
    "status": "success",
    "message": "Login successful."
  }
```

- Title: Error

```json
  {
    "status": "error",
    "message": "Invalid username or password."
  }
  {
    "status": "error",
    "message": "Missing login fields."
  }
```


# Key Generation Endpoint

- Endpoint Information
	- URL: /generate-key
	- Method: POST

## Description
Generates a unique authentication key with a specified prefix and expiration.

Request Format
```json
{
  "type": "generateKey",
  "userId" "integer"
  "prefix": "string",
  "expiryDays": "integer"
}
```

Responses
- Title: Success
```json
  {
    "status": "success",
    "key": "generated_key",
    "keyId": "integer"
  }
```
- Title: Error
```json
  {
    "status": "error",
    "message": "Key generation failed."
  }
```


# User Information Endpoint

- Endpoint Information
	**URL:** /user-information
	**Method:** POST


## Description
Retrieves data about a user's authentication key and profile information based on their user ID.

Request Format
```json
{
  "type": "userInformation",
  "userId": "integer"
}
````
Title: success
```json
{
  "status": "success",
  "data": {
    "authKey": {
      "key": "string",
      "expiration": "datetime"
    },
    "profile": {
      "username": "string",
      "email": "string",
      "displayName": "string",
      "description": "string"
    }
  }
}
```
Title: error
```json
{
  "status": "error",
  "message": "User not found."
}
```

# Claim Key Endpoint

- Endpoint Information
	**URL:** /claim-key
	**Method:** POST

## Description
Claims an Auth key to allow the user access to a product for a certain period of time

Request Format
```json
{
  "type": "claimKey",
  "userId": "integer",
  "key" : "string"
}
````
Title: success
```json
{
  "status": "success",
  "data": {
    "authKey": {
      "key": "string",
      "expiration": "datetime"
    },
    "profile": {
      "username": "string",
      "email": "string",
      "displayName": "string",
      "description": "string"
    }
  }
}
```
Title: error
```json
{
  "status": "error",
  "message": "Key not found."
}
{
  "status": "error",
  "message": "User not found."
}
{
  "status": "error",
  "message": "Key is already claimed."
}
```

# Security Considerations

- Overview
	- Details on security practices implemented in the API.

##  Highlights
- Passwords are hashed using SHA256 but you should hash passwords BEFORE sending them to the API.
- Authentication keys are generated with a mix of timestamps and random bytes while logging the userId to ensure the user has the correct permissions.
- HTTPS is recommended to secure data in transit.

## Warning
- Keys and user profiles are currently configured for a single product at varying durations. if multiple products are required future implementation will be 100% an option.

# Rate Limits
- Login Endpoint: 5 requests per min, resets if the login is successful.
- Register Endpoint: 10 requests per hour
- Key Generation Endpoint: 100 requests per 30m per user, if the user isn't permitted to use the endpoint we block requests to the end point for 30 seconds from the respective IP
- User Information Endpoint: 60 requests per min

## Rate Limited Response
```json
  {
    "status": "ratelimited",
    "until_seconds": "integer",
    "userId": "integer"
  }
  ```

- Security
	- if the rate limit is triggered 3 times in a row without a successful attempt the IP is blocked for 12 hours
