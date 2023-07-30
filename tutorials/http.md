### http methods

```php
+---------+------+------------+
| Method  | Safe | Idempotent |
+---------+------+------------+
| GET     | yes  | yes        |
| HEAD    | yes  | yes        |
| OPTIONS | yes  | yes        |
| TRACE   | yes  | yes        |
| POST    | no   | no         | create new resourse
| PUT     | no   | yes        | update resourse
| PATCH   | no   | no         | update part of resourse
| DELETE  | no   | yes        |
| CONNECT | no   | no         |
+---------+------+------------+ 
```

### Http status codes
Five classes:
- Informational responses (100 – 199)
- Successful responses (200 – 299)
- Redirection messages (300 – 399)
- Client error responses (400 – 499)
- Server error responses (500 – 599)

Most common
- 101 Switching Protocols - This code is sent in response to an Upgrade request header from the client and indicates the protocol the server is switching to (exmp from http to websocket).
- 400 Bad Request – This means that client-side input fails validation.
- 401 Unauthorized – This means the user isn’t authorized to access a resource. It usually returns when the user
  isn’t authenticated.
- 403 Forbidden – This means the user is authenticated, but it’s not allowed to access a resource.
- 404 Not Found – This indicates that a resource is not found.
- 429 Too Many Requests
- 500 Internal server error – This is a generic server error. It probably shouldn’t be thrown explicitly.
- 502 Bad Gateway – This indicates an invalid response from an upstream server.
- 503 Service Unavailable – This indicates that something unexpected happened on server side (It can be anything like
  server overload, some parts of the system failed, etc.).
- 504 Gateway Timeout - This error response is given when the server is acting as a gateway and cannot get a response in time.

Exm: Yii framework return 500 as default when handling exceptions/errors that doesn't have code

#### About success status codes here are a few examples:
- 200 – as the default success status code; 
- 201 – for resource creation success; 
- 202 – for asynchronous requests (this one is a little bit more complex, so it is ok to be out ;D); 
- 204 – for resource deletion or update, as there is no reason to return a deleted resource or the updated state of the resource (this last one is debatable);
- 206 – for partial content as when returning from a large collection of resources, perhaps pagination, you can assert that there are resources unreceived.

#### Error status codes here are a few things
400 status code for “User already exists”, I believe it is wrong as the request body is in a correct format, the only thing wrong is the information conveyed by it. 
For this problem of conflict there is a much better response, 
the 409 – Conflict, as it simply states there is a conflict between the data provided by the client and the current state of the server. 
400 could be the default error code, for that I agree when an implementation does not desire to use 409. 
But I believe that 400 should be used only by default behaviour and when the format of the data is wrong, 
everything else should lead to 422 (Unprocessable Entity) as it states that the syntax is correct, but it is semantically incorrect by some business rule.

So returning status codes depends on project and team standard. 


### Rest API

A REST API is an application programming interface architecture style that conforms to specific architectural
constraints, like stateless communication and cacheable data.

1. JSON
   To communicate in json format add headers
   Content-Type: application/json
2. Use nouns instead of verbs in endpoint paths
   The verbs are in the HTTP verbs.
3. Use logical nesting on endpoints
4. Handle errors gracefully and return standard error codes