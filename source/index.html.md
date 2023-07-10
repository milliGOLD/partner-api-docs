---
title: API Reference

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: milliGOLD Partner API
    content: Documentation for the milliGOLD Partner API
---

# Breaking changes policy

### We may publish incremental non-breaking changes to API endpoints. Examples of non-breaking changes include:

- New endpoints created.
- Adding optional parameters added to existing endpoints, or changing the default value of parameters.
- Adding new request headers, or changing the default value of request headers.
- Adding new fields to response objects. When you make API requests, your system must be prepared to receive more fields than you might have in the past.
- Different values for fields which seem unchanging day-to-day. For example, if we change the access token expiration, that would be a non-breaking change. Whenever possible, your system should avoid hard-coding assumptions about values in API responses, and instead process the API response as provided.
- Adding new errors or HTTP response codes.
- Rephrased or reworded error messages. Avoid building app logic based on the specific wording of specific error messages. Instead, app logic should be based on HTTP response status codes.

# Access Tokens

All requests to the Partner API must be made with an access token. Access tokens are created by making a request to the authorization `/auth/token` endpoint with your `partnerId` and `partnerSecret` values.

<aside class="warning">
The access tokens created with partner credentials are only for secure, server-side access to user data. Don't hard code access tokens or partner secret in mobile apps, or in front-end JavaScript web apps, or publish them to social media.
</aside>

### Expiration

Access tokens can't be refreshed. The appropriate pattern is to request a new one when they expire. The expiration time isn't configurable and is currently set to 1 hour.

### Request/Response

The response will be JSON formatted. For more details, see the related Create Access Token endpoint documentation.

| Request              |                                     |
| -------------------- | ----------------------------------- |
| URL                  | `{BASE_URL}/partner/auth/token`     |
| Method               | `POST`                              |
| Content-Type         | `application/x-www-form-urlencoded` |
| Body (form encoded): | partnerId=(your partner id)         |
|                      | partnerSecret=(your partner secret) |

| Response    |                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------- |
| accessToken | The Access Token you will use for Partner API requests.                                       |
| expiresIn   | Number of seconds until the access token expires (can be added to the `createdAt` timestamp). |
| createdAt   | A timestamp when the token was created.                                                       |

# Using the Partner API

### Base URLs

The base URL for requests to the partner API is given below and should be followed by the endpoint path.

| Sandbox                                  | Production                       |
| ---------------------------------------- | -------------------------------- |
| https://api-sandbox.milligold.in/partner | https://api.milligold.in/partner |

### Authorization

All requests to Data API endpoints should include the Authorization header.

| Request Header  | Allowed Values                  | Optional/Required               |
| --------------- | ------------------------------- | ------------------------------- |
| `Authorization` | `Bearer your-access-token-here` | Required for all API endpoints. |

<aside class="notice">
Note that there is a single white space between the "Bearer" and the access token value, and this is all one header. For example: <code>`Authorization: Bearer your-access-token-here`</code>
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

All requests to the Partner API are subject to rate limiting. You will be limited to 10 requests every 20 seconds from the first request. Every 20 seconds, the limit will reset. When the rate limit is exceeded, an HTTP 429 Too many requests response will be issued.

### Response Headers

The error Response from the Partner API will include rate limiting headers. You can use these headers to avoid exceeding the allotted request capacity.

| HTTP Method              | Description                                                      |
| ------------------------ | ---------------------------------------------------------------- |
| `X-Rate-Limit-Limit`     | The maximum number of requests per limit period.                 |
| `X-Rate-Limit-Remaining` | The number of requests remaining in the current limiting period. |
| `X-Rate-Limit-Reset`     | The timestamp when the current limiting period resets.           |

# Authentication

## Create Access Token

```shell
curl --location 'https://{BASE_URL}/auth/token' \
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

This endpoint will create an access token, which should be used to fetch data from the other endpoints

### HTTP Request

`POST https://{BASE_URL}/auth/token`

### Headers

| Name           | Description                                     |
| -------------- | ----------------------------------------------- |
| `Content-Type` | Needs to be `application/x-www-form-urlencoded` |

### Request Body

| Parameter       | Description          |
| --------------- | -------------------- |
| `partnerId`     | your partner ID.     |
| `partnerSecret` | your partner secret. |

# Gold Rate

## Get Gold Rate

```shell
curl --location 'https://{BASE_URL}/gold/rate' \
--header 'Authorization: Bearer <your access token here>'
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "data": {
    "rate": 1111,
    "cgstRate": 0.015,
    "sgstRate": 0.015,
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
curl --location 'https://{BASE_URL}/user/info/{MOBILE_NUMBER}' \
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
