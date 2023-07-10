# Errors

Any API request could potentially result in an error. An error response is indicated by an HTTP status code other than 200.

<aside class="notice">
See the <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Status" target="_blank">MDN documentation</a> for a list of standard HTTP response codes and their meanings.
</aside>

<aside  class="warning">
Many errors can be resolved by adjusting the way you make the API request, so please review the API documentation carefully.
</aside>

### Error Response Format

| Response  |                                                                                                        |
| --------- | ------------------------------------------------------------------------------------------------------ |
| success   | A boolean value denoting the status of the request.                                                    |
| error     | A short description of the error that has occurred.                                                    |
| errorCode | A unique ID for this specific error. Please provide this error id when you ask for help with an error. |

### Common Errors

| Status Code | Status Text           | Tips                                                                                                                                                                                                                                            |
| ----------- | --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 400         | Bad Request           | Review your HTTP request logic to make sure the request body is a valid JSON object. When creating or updating records, the JSON object should have a data field, which is an object with the fields and values you intend to create or update. |
| 401         | Unauthorized          | Review your HTTP request logic and make sure you're including an Authorization. header                                                                                                                                                          |
| 404         | Not Found             | Incorrect URLs will return this error. Make sure the URL path you are sending a request to exactly match as given in the API documentation                                                                                                      |
| 429         | Too Many Requests     | Check the X-Rate-Limit-Reset header for a timestamp. This is the point after which the number of remaining requests will be reset. You can re-try any failed API requests after this point.                                                     |
| 500         | Internal Server Error | Please contact is with the `errorCode` value from the response object. That specific error code will help us look up additional diagnostic information for that specific request.                                                               |
