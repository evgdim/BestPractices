CONTENTS
--------
 - Development Environment Setup (runtime, IDE, DB installation, SourceControl installation)
 - Source Control Setup (new repository or setup existing repository)
 - New Project setup
 - New Database schema setup
 - Implement Change Request (new branch, copy database, generate scripts...)
 - Build sources
 - Deploy (dev, test, prod)
 - Naming Convention
 - [Rest API Design](#Rest API Design)

 Development Environment Setup (runtime, IDE, DB installation, SourceControl installation)
-----------------------------------------------------------------------------------------


Source Control Setup (new repository or setup existing repository)
------------------------------------------------------------------


New Project setup
-----------------


New Database schema setup
-------------------------


Implement Change Request (new branch, copy database, generate scripts...)
-------------------------------------------------------------------------

Build sources
-------------

Deploy (dev, test, prod)
------------------------

Naming Convention
-----------------
- Repository (DAO) layer
  * For interfaces use names like **(Entity)Repository** - e.g. **UserRepository**, **CustomerRepository**
  * For implementations use the technology name before the interface name **(Technology)(Entity)Repository** - e.g. **JdbcUserRepository**, **JpaCustomerRepository**, **FileSystemCustomerRepository**
  * For methods that insert some Entity use **insert(Entity)** - e.g. **insertUser**, **insertCustomer**
  * For method that edit some Entity use **edit(Entity)** - e.g. **editUser**, **editCustomer**
  * For method that select some Entity use **find(Entity)** - e.g. **findUser**. Use overloading to pass different search parameters.

- Service layer

- Controller (Web) layer
  * Use names like **(Entity)Controller** - e.g. **UserController**
  * Use **@RequestMapping("(Entity)")** on class( Controller ) level, so all the method can be relative to that url
  ```
    @RequestMapping("/user")
    public class UserController {}
  ```
  * Try to use REST like endpoint names - see Rest API Design

# Rest API Design

```
GET     /tickets    - Retrieves a list of tickets
GET     /tickets/12 - Retrieves a specific ticket
POST    /tickets    - Creates a new ticket
PUT     /tickets/12 - Updates ticket #12
PATCH   /tickets/12 - Partially updates ticket #12
DELETE  /tickets/12 - Deletes ticket #12
```
Relations
```
GET     /tickets/12/messages    - Retrieves list of messages for ticket #12
GET     /tickets/12/messages/5  - Retrieves message #5 for ticket #12
POST    /tickets/12/messages    - Creates a new message in ticket #12
PUT     /tickets/12/messages/5  - Updates message #5 for ticket #12
PATCH   /tickets/12/messages/5  - Partially updates message #5 for ticket #12
DELETE  /tickets/12/messages/5  - Deletes message #5 for ticket #12
```
Result filtering, sorting & searching
```
GET /tickets?state=open - Get only open tickets
GET /tickets?sort=-priority,created_at - Sorted by priority (desc) and then by created_at (asc)
GET /tickets?state=closed&sort=-updated_at - Retrieve recently closed tickets
GET /tickets?q=return&state=open&sort=-priority,created_at - Retrieve the highest priority open tickets mentioning the word 'return'
```
Updates & creation should return a resource representation

A PUT, POST or PATCH call may make modifications to fields of the underlying resource that weren't part of the provided parameters (for example: created_at or updated_at timestamps). To prevent an API consumer from having to hit the API again for an updated representation, have the API return the updated (or created) representation as part of the response.

In case of a POST that resulted in a creation, use a HTTP 201 status code and include a Location header that points to the URL of the new resource.

Return Status
* 200 OK - Response to a successful GET, PUT, PATCH or DELETE. Can also be used for a POST that doesn't result in a creation.
* 201 Created - Response to a POST that results in a creation. Should be combined with a Location header pointing to the location of the new resource
* 204 No Content - Response to a successful request that won't be returning a body (like a DELETE request)
* 400 Bad Request - The request is malformed, such as if the body does not parse.
* 401 Unauthorized - When no or invalid authentication details are provided. Also useful to trigger an auth popup if the API is used from a browser
* 403 Forbidden - When authentication succeeded but authenticated user doesn't have access to the resource
* 404 Not Found - When a non-existent resource is requested
* 405 Method Not Allowed - When an HTTP method is being requested that isn't allowed for the authenticated user
* 422 Unprocessable Entity - Used for validation errors
