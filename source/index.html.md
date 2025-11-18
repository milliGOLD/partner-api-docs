---
title: Partner API

toc_footers:
  - Copyright © milliGOLD.in
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: milliGOLD Partner API
    content: Documentation for the milliGOLD Partner API
---

# Introduction

Welcome to the Partner API Documentation site for milliGOLD! Our API offers a wide range of functionalities to seamlessly integrate milliGOLD's services into your applications.

With our API, you can access and manipulate various aspects of milliGOLD accounts, transactions, subscriptions, and more. Let's take a closer look at some of the key features:

- Account Management: Verify the existence of an account and retrieve its details using the registered mobile number. You can also create new accounts by providing the customer's full name, email address, and mobile number.

- Balance and Pricing: Retrieve the gold balance of a customer and obtain the current retail price of milliGOLD per gram.

- Transactions: Create new transactions with ease by specifying the registered mobile number, amount, and payment type. You can also fetch recent transaction details using the customer's mobile number.

<!-- - Subscription Plans: Set up recurring payment plans by creating new subscriptions. Define the customer's name, email address, mobile number, amount, debit date, and duration for seamless subscription management.

- Redemption Requests: Enable customers to redeem their gold by submitting requests via the API. Specify the registered mobile number, gold gram quantity, and redemption type such as Coin/Bar, Jewelry Shop, RBI Gold Bond, Bank Settlement, or Reward Points. -->

- Profile and Banking Information: Update and maintain customer profiles with details such as full name, date of birth, gender, email address, mobile number, address, and pin code. Additionally, store bank account information, including the account number, account name, and IFSC code.

- Nominee Details: Set nominee information for the account, providing the nominee's name, date of birth, and relationship.

# Breaking changes policy

We may publish incremental non-breaking changes to API endpoints. Examples of non-breaking changes include:

- Addition of new endpoints.
- Adding optional parameters added to existing endpoints, or changing the default value of parameters.
- Adding new request headers, or changing the default value of request headers.
- Adding new fields to response objects. When you make API requests, your system must be prepared to receive more fields than you might have in the past.
- Different values for fields which seem unchanging day-to-day. For example, if we change the access token expiration, that would be a non-breaking change. Whenever possible, your system should avoid hard-coding assumptions about values in API responses, and instead process the API response as provided.
- Adding new errors or HTTP response codes.
- Rephrased or reworded error messages. Avoid building app logic based on the specific wording of specific error messages. Instead, app logic should be based on HTTP response status codes.

# Access Tokens

All requests to the Partner API must be made with an access token. Access tokens are created by making a request to the authentication endpoint with your `partnerId` and `partnerSecret` values.

<aside class="warning">
The access tokens created with partner credentials are only for secure, server-side access to user data. Don't hard code access tokens or partner secret in mobile apps, or in front-end JavaScript web apps, or publish them to social media.
</aside>

### Expiration

Access tokens can't be refreshed. The appropriate pattern is to request a new one when they expire. The expiration time isn't configurable and is currently set to 1 hour.

### Request/Response

The response will be JSON formatted. For more details, see the related [Create Access Token endpoint](#create-access-token) documentation.

| Request      |                                       |
| ------------ | ------------------------------------- |
| URL          | `{BASE_URL}/auth/token`               |
| Method       | `POST`                                |
| Content-Type | `application/x-www-form-urlencoded`   |
| Body:        | `partnerId`=[your partner id]         |
|              | `partnerSecret`=[your partner secret] |

| Response      |                                                                                               |
| ------------- | --------------------------------------------------------------------------------------------- |
| `accessToken` | The Access Token you will use for Partner API requests.                                       |
| `expiresIn`   | Number of seconds until the access token expires (can be added to the `createdAt` timestamp). |
| `createdAt`   | A timestamp when the token was created.                                                       |

# Using the Partner API

### Base URLs

The base URL for requests to the partner API is given below and should be followed by the endpoint path.

| Sandbox                                      | Production                           |
| -------------------------------------------- | ------------------------------------ |
| https://api-sandbox.milligold.in/api/partner | https://api.milligold.in/api/partner |

### Authorization

All requests to Data API endpoints should include the Authorization header.

| Request Header  | Allowed Values                  | Optional/Required               |
| --------------- | ------------------------------- | ------------------------------- |
| `Authorization` | `Bearer your-access-token-here` | Required for all API endpoints. |

<aside class="notice">
Note that there is a single white space between the "Bearer" and the access token value, and this is all one header. For example: <code>Authorization: Bearer your-access-token-here</code>
</aside>

### Request Format

API requests should be made with the appropriate HTTP Method that corresponds to the Partner API operation you're trying to perform.

| HTTP Method | Description                           |
| ----------- | ------------------------------------- |
| GET         | View list of records or single record |
| POST        | Create a new record                   |
| PATCH       | Update properties of a single record  |

### Creating/Updating Data

When creating or updating records via the Partner API, the record data should be sent in the request body as a JSON object. The root object should have a data field, which is an object including any fields you intend to include in the create/update operation. It isn't necessary to include every possible field in every update operation, but for create operations, depending on the endpoint you might be required to provide a value for all fields.

<aside class="notice">
All "update" operations in the Partner API are PATCH updates. These updates only write to the fields you provide, and fields not provided in an update won't be affected. Another kind of update uses PUT, which replaces the entire object all at once. The Partner API only supports PATCH updates.
</aside>

### Response Format

Partner API endpoints will return JSON formatted strings.

- Collection endpoints will return an array of objects for the data field (it will always be an array even if there are zero or one objects returned).
- Single record endpoints will return an object for the data field.
- Update and create endpoints will return the record you have created/updated.

# Rate Limiting

All requests to the Partner API are subject to rate limiting. You will be limited to 20 requests every 10 seconds from the first request. Every 10 seconds, the limit will reset. When the rate limit is exceeded, an HTTP 429 Too many requests response will be issued.

### Response Headers

The rate limit error Response from the Partner API will include the following headers. You can use these headers to avoid exceeding the allotted request capacity.

| HTTP Method              | Description                                                      |
| ------------------------ | ---------------------------------------------------------------- |
| `X-Rate-Limit-Limit`     | The maximum number of requests per limit period.                 |
| `X-Rate-Limit-Remaining` | The number of requests remaining in the current limiting period. |
| `X-Rate-Limit-Reset`     | The timestamp when the current limiting period resets.           |

# Authentication

## Create Access Token

```shell
curl --location --request POST 'https://{BASE_URL}/auth/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'partnerId=<your partner ID>' \
--data-urlencode 'partnerSecret=<your partner secret>'
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiJ9.eyJwYXJ0bmVySWQiOiJjbGptd2dldTEwMDAwZzVmdnp0bjJtcnZuIiwicGFydG5lck5hbWUiOiJUZXN0IFBhcnRuZXIiLCJpYXQiOjE2ODg5OTQyNzEsImV4cCI6MTY4ODk5Nzg3MX0.0SOC-RqUdBt_7GuAjS9_Qb6YMRT7zZu8nzFEOVvUUpc",
    "expiresIn": 3600,
    "createdAt": 1688994271
  }
}
```

This endpoint will create an access token, which should be used to authorize yourself before trying to fetch data from other endpoints

### HTTP Request

`POST https://{BASE_URL}/auth/token`

### Headers

| Name           | Description                                     |
| -------------- | ----------------------------------------------- |
| `Content-Type` | Needs to be `application/x-www-form-urlencoded` |

### Request Body

| Parameter       | Description         |
| --------------- | ------------------- |
| `partnerId`     | your partner ID     |
| `partnerSecret` | your partner secret |

# Gold Rate

## Get Gold Rate

```shell
curl --location --request GET 'https://{BASE_URL}/gold/rate' \
--header 'Authorization: Bearer <your access token here>'
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "data": {
    "rate": 1111,
    "gstRate": 0.03,
    "updatedAt": "2023-01-05T09:17:03.459Z"
  }
}
```

This endpoint will fetch the current gold rate.

### HTTP Request

`GET https://{BASE_URL}/gold/rate`

### Headers

| Name            | Description                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| `Authorization` | The authorization header used to authenticate partners. Needs to be `Bearer <your access token here>` |

# User

## Get User Info

```shell
curl --location --request GET 'https://{BASE_URL}/user/info/{MOBILE_NUMBER}' \
--header 'Authorization: Bearer <your access token here>'
```

> The above command returns JSON structured like this if the user exists:

```json
{
  "success": true,
  "data": {
    "id": "cljwvd2x20001mm0gg0a9z5px",
    "name": "Test User",
    "email": "test@example.com",
    "countryCode": "91",
    "mobileNumber": "1234567890",
    "gender": "MALE",
    "birthday": null,
    "upiId": "milligold.911234567890@icici",
    "address": null,
    "pincode": null,
    "goldBalance": 0
  }
}
```

> If a user with the given mobile number does not exist, this endpoint will return a 404 status with the following JSON response:

```json
{
  "success": false,
  "error": "The requested resource was not found",
  "errorCode": "3d1c75d0-37ee-4530-b281-4ab9c597f2bb"
}
```

This endpoint will fetch information about the user with the given mobile number.

### HTTP Request

`GET https://{BASE_URL}/user/info/{MOBILE_NUMBER}`

### Headers

| Name            | Description                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| `Authorization` | The authorization header used to authenticate partners. Needs to be `Bearer <your access token here>` |

## Create User

```shell
curl --location --request POST 'https://{BASE_URL}/user/create' \
--header 'Authorization: Bearer <your access token here>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "name": "Test User",
        "email": "test@example.com",
        "mobileNumber": "1234567890",
        "countryCode": "91",
        "gender": "MALE",
        "birthday": "2000-01-01",
        "address": "India",
        "pincode": "123456",
        "bankAccount": {
            "accountNumber": "1234567890",
            "ifscCode": "KKBK0000261",
            "firstName": "Test",
            "lastName": "Account"
        },
        "nominee": {
            "firstName": "Test",
            "lastName": "Nominee",
            "gender": "FEMALE",
            "relationship": "Mother",
            "dateOfBirth": "1995-08-19"
        }
    }
}'
```

> The above command returns JSON structured like this if the user has been created successfully:

```json
{
  "success": true,
  "data": {
    "id": "cn17h9z6eyua78wme67tnc04",
    "name": "Test User",
    "email": "test@example.com",
    "countryCode": "91",
    "mobileNumber": "1234567890",
    "gender": "MALE",
    "birthday": "2000-01-01",
    "upiId": "milligold.911234567890@icici",
    "address": "India",
    "pincode": "123456",
    "goldBalance": 0,
    "bankAccount": {
      "id": "kulqr1et45y9w8bqmll44jwz",
      "accountNumber": "1234567890",
      "ifscCode": "KKBK0000261",
      "firstName": "Test",
      "middleName": null,
      "lastName": "Account",
      "deleted": false,
      "createdAt": "2023-02-11T12:40:39.977Z",
      "updatedAt": "2023-02-11T12:40:39.977Z",
      "userId": "cn17h9z6eyua78wme67tnc04"
    },
    "nominee": {
      "id": "fhl24bawak2zjfvl8jfckckx",
      "firstName": "Test",
      "middleName": null,
      "lastName": "Nominee",
      "gender": "FEMALE",
      "relationship": "Mother",
      "dateOfBirth": "1995-08-19",
      "deleted": false,
      "createdAt": "2023-02-11T12:40:40.015Z",
      "updatedAt": "2023-02-11T12:40:40.015Z",
      "userId": "cn17h9z6eyua78wme67tnc04"
    }
  }
}
```

> If a user with the given mobile number already exists, this endpoint will return a 400 status with the following JSON response:

```json
{
  "success": false,
  "error": "User with given mobile number already exists",
  "errorCode": "966b9329-1c68-4cf6-8ac07071f7198734"
}
```

This endpoint will create a new user with the given mobile number and inputted user information.

### HTTP Request

`POST https://{BASE_URL}/user/create`

### Headers

| Name            | Description                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| `Authorization` | The authorization header used to authenticate partners. Needs to be `Bearer <your access token here>` |

<aside class="notice">
A '*' next to a property signifies that it is a required property
</aside>

### Body

| Name            | Description                                 |
| --------------- | ------------------------------------------- |
| `name*`         | The name of the user                        |
| `email`         | The email address of the user               |
| `mobileNumber*` | The mobile number of the user               |
| `countryCode*`  | The country code of the user's phone number |
| `gender`        | The gender of the user                      |
| `birthday`      | The birthday of the user (YYY-MM-DD)        |
| `address`       | The address of the user                     |
| `pincode`       | The pincode of the user's address           |
| `bankAccount`   | The bank account details of the user        |
| `nominee`       | The nominee details of the user             |

`bankAccount` Object

| Name             | Description                                      |
| ---------------- | ------------------------------------------------ |
| `accountNumber*` | The bank account number of the user              |
| `ifscCode*`      | The IFSC code of the user's bank                 |
| `firstName*`     | The first name associated with the bank account  |
| `middleName`     | The middle name associated with the bank account |
| `lastName*`      | The last name associated with the bank account   |

`nominee` Object

| Name            | Description                                  |
| --------------- | -------------------------------------------- |
| `firstName*`    | The first name of the nominee                |
| `middleName`    | The middle name of the nominee               |
| `lastName*`     | The last name of the nominee                 |
| `gender`        | The gender of the nominee                    |
| `relationship*` | The relationship of the nominee to the user  |
| `dateOfBirth*`  | The date of birth of the nominee (YYY-MM-DD) |

The possible values for the `gender` field are:

- `MALE`
- `FEMALE`
- `NON_BINARY`
- `OTHER`
- `UNKNOWN`

These values represent different gender identities or options for the `gender` field in the JSON body.

## Update User

```shell
curl --location --request PATCH 'https://{BASE_URL}/user/update/{MOBILE_NUMBER}' \
--header 'Authorization: Bearer <your access token here>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "name": "Test User",
        "email": "test@example.com",
        "mobileNumber": "1234567890",
        "countryCode": "91",
        "gender": "FEMALE",
        "birthday": "2000-01-01",
        "address": "India",
        "pincode": "123456",
        "bankAccount": {
            "accountNumber": "0987654321",
            "ifscCode": "KKBK0000261",
            "firstName": "Test",
            "lastName": "Account"
        },
        "nominee": {
            "firstName": "Test",
            "lastName": "Nominee",
            "gender": "MALE",
            "relationship": "Mother",
            "dateOfBirth": "1995-08-19"
        }
    }
}'
```

> The above command returns JSON structured like this if the user has been created successfully:

```json
{
  "success": true,
  "data": {
    "id": "no8y6elzzrgjc4n2d6f65iy0",
    "name": "Test User",
    "email": "test@example.com",
    "countryCode": "91",
    "mobileNumber": "1234567890",
    "gender": "FEMALE",
    "birthday": "2000-1-1",
    "upiId": "milligold.911234567890@icici",
    "address": "India",
    "pincode": "123456",
    "goldBalance": 0,
    "bankAccount": {
      "id": "z92xbbsr0rxjgljpldovyaop",
      "accountNumber": "0987654321",
      "ifscCode": "KKBK0000261",
      "firstName": "Test",
      "middleName": null,
      "lastName": "Account"
    },
    "nominee": {
      "id": "y4j0g0llgp6h3u2ggrtyyjm1",
      "firstName": "Test",
      "middleName": null,
      "lastName": "Nominee",
      "gender": "MALE",
      "relationship": "Mother",
      "dateOfBirth": "1995-8-19"
    }
  }
}
```

> If a user with the given mobile number does not exist, this endpoint will return a 404 status with the following JSON response:

```json
{
  "success": false,
  "error": "The requested resource was not found",
  "errorCode": "5b5ece7f-5629-4a0b-bb69f5e4e4261bb1"
}
```

This endpoint will update a user's information with the given mobile number and inputted user information.

### HTTP Request

`PATCH https://{BASE_URL}/user/update/{MOBILE_NUMBER}`

### Headers

| Name            | Description                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| `Authorization` | The authorization header used to authenticate partners. Needs to be `Bearer <your access token here>` |

### Body

| Name          | Description                          |
| ------------- | ------------------------------------ |
| `email`       | The email address of the user        |
| `gender`      | The gender of the user               |
| `birthday`    | The birthday of the user (YYY-MM-DD) |
| `address`     | The address of the user              |
| `pincode`     | The pincode of the user's address    |
| `bankAccount` | The bank account details of the user |
| `nominee`     | The nominee details of the user      |

`bankAccount` Object

| Name            | Description                                      |
| --------------- | ------------------------------------------------ |
| `accountNumber` | The bank account number of the user              |
| `ifscCode`      | The IFSC code of the user's bank                 |
| `firstName`     | The first name associated with the bank account  |
| `middleName`    | The middle name associated with the bank account |
| `lastName`      | The last name associated with the bank account   |

`nominee` Object

| Name           | Description                                  |
| -------------- | -------------------------------------------- |
| `firstName`    | The first name of the nominee                |
| `middleName`   | The middle name of the nominee               |
| `lastName`     | The last name of the nominee                 |
| `gender`       | The gender of the nominee                    |
| `relationship` | The relationship of the nominee to the user  |
| `dateOfBirth`  | The date of birth of the nominee (YYY-MM-DD) |

The possible values for the `gender` field are:

- `MALE`
- `FEMALE`
- `NON_BINARY`
- `OTHER`
- `UNKNOWN`

These values represent different gender identities or options for the `gender` field in the JSON body.

# Transaction

## Get User Transactions

```shell
curl --location --request GET 'https://{BASE_URL}/transaction/{MOBILE_NUMBER}?startDate=2023-06-01&endDate=2023-07-31' \
--header 'Authorization: Bearer <your access token here>'
```

> The above command returns JSON structured like this if the user exists:

```json
{
  "success": true,
  "data": [
    {
      "id": "adzzl3o8byc7a4demplcyv0g",
      "goldAmount": 0.0008738737951465051,
      "amount": 1,
      "amountWithoutGst": 0.9708737864077672,
      "gstAmount": 0.029126213592233014,
      "goldRate": 1111,
      "paymentType": "PARTNER",
      "paymentStatus": "SUCCESS",
      "createdAt": "2023-07-16T16:27:25.000Z",
      "userId": "no8y6elzzrgjc4n2d6f65iy0"
    },
    {
      "id": "f4wyd1nrsvck5w8p7zt0hfkj",
      "goldAmount": 0.0008738737951465051,
      "amount": 1,
      "amountWithoutGst": 0.9708737864077672,
      "gstAmount": 0.029126213592233014,
      "goldRate": 1111,
      "paymentType": "PARTNER",
      "paymentStatus": "SUCCESS",
      "createdAt": "2023-07-19T11:39:41.000Z",
      "userId": "no8y6elzzrgjc4n2d6f65iy0"
    }
  ]
}
```

> If a user with the given mobile number does not exist, this endpoint will return a 404 status with the following JSON response:

```json
{
  "success": false,
  "error": "The requested resource was not found",
  "errorCode": "20904ccf-a0e4-40c3-bfb2-14e292ece75a"
}
```

This endpoint will fetch the transactions of a user with the given mobile number.

<aside class="notice">
The data object in the response will always be an array even if there are zero or one transactions returned
</aside>

### HTTP Request

`GET https://{BASE_URL}/transaction/{MOBILE_NUMBER}`

### Headers

| Name            | Description                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| `Authorization` | The authorization header used to authenticate partners. Needs to be `Bearer <your access token here>` |

### Query Parameters

| Name        | Description                                                     | Default                        |
| ----------- | --------------------------------------------------------------- | ------------------------------ |
| `startDate` | The start date from which you want the transactions (YYY-MM-DD) | 30 days before The current day |
| `endDate`   | The end date to which you want the transactions (YYY-MM-DD)     | The current day                |

## Create Transaction

```shell
curl --location --request POST 'https://{BASE_URL}/transaction/create' \
--header 'Authorization: Bearer <your access token here>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "mobileNumber": "1234567890",
        "countryCode": "91",
        "amount": 1
    }
}'
```

> The above command returns JSON structured like this if the transaction has been created successfully:

```json
{
  "success": true,
  "data": {
    "id": "hybzln6kk0jlxj2g7s41nueu",
    "goldAmount": 0.0008738737951465051,
    "amount": 1,
    "amountWithoutGst": 0.9708737864077672,
    "gstAmount": 0.029126213592233014,
    "goldRate": 1111,
    "paymentType": "PARTNER",
    "paymentStatus": "SUCCESS",
    "createdAt": "2023-03-23T16:46:42.738Z",
    "userId": "no8y6elzzrgjc4n2d6f65iy0"
  }
}
```

> If a user with the given mobile number does not exist, this endpoint will return a 404 status with the following JSON response:

```json
{
  "success": false,
  "error": "The requested resource was not found",
  "errorCode": "826697c2-6aa3-4416-b24f041762803e61"
}
```

This endpoint will create a new sale transaction and credit the gold amount to the user with the inputted mobile number

### HTTP Request

`POST https://{BASE_URL}/transaction/create`

### Headers

| Name            | Description                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| `Authorization` | The authorization header used to authenticate partners. Needs to be `Bearer <your access token here>` |

### Body

| Name            | Description                                 |
| --------------- | ------------------------------------------- |
| `mobileNumber*` | The mobile number of the user               |
| `countryCode*`  | The country code of the user's phone number |
| `amount*`       | The rupee amount                            |

<aside class="notice">
A '*' next to a property signifies that it is a required property
</aside>
