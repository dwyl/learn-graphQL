# Learn GraphQL [Work In Progress!!!]
![GraphQL nodeJS logo](https://www.reindex.io/blog/building-a-graphql-server-with-node-js-and-sql/GraphQL_NodeJS.png)  
Learn to use GraphQL - A query language that allows client applications to specify their data fetching requirements _(it's really just a way of thinking about data!)_

## What is GraphQL?
GraphQL is a query language that sits on top of your existing application code. It allows you to make a query to your application data model that returns a JSON result that is very similar to your query. GraphQL enables queries to be specified on the client side which changes the client-server relationship.

Here's a basic example of a GraphQL query and the returned result:

GraphQL query
```
 {
   me {
     name
   }
 }
```
JSON result
```json
 {
   "me" : {
     "name" : "John Smith"
   }
 }
```
## Parametrization
In GraphQL you can parameterize your queries to return explicit results. For example if you want to retrieve specific data for a user who has the user ID = 1, you can add that ID as a query parameter:

```
 {
   user(id: 1) {
     name
   }
 }
```    
```json
 {
   "user" : {
     "id"   : 1,
     "name" : "John Smith"
   }
 }
```
You can parameterize fields at any query level. Imagine we are querying a user profile and we want to specify the size of the profile picture that gets returned to us. We could make a query like this:

```
 {
   user(id: 1) {
     id,
     name,
     profilePicture(size: 300) {
       width,
       height,
       url
     }
   }
 }
```

```json
 {
   "user" : {
     "id"   : 1,
     "name" : "John Smith",
     "profilePicture" : {
       "width"  : 300,
       "height" : 300,
       "url"    : "https://cdn/profile.jpg"
     }
   }
 }
```
The profile picture has been returned with the dimensions specified in the query.

## Aliasing
Aliasing allows you to return different version results of the same object. For example if you wanted to make another request for a profile picture but this time you wanted both a large and a small version, you could write two request aliases like this:

```
 user {
   id,
   name,
   bigPic: profilePicture(size: 600) {
     width,
     height,
     url
   },
   smallPic: profilePicture(size: 50) {
     width,
     height,
     url
   }
 }
```
```json
 "user" : {
   "id"   : 1,
   "name" : "John Smith",
   "bigPic" : {
     "width"  : 600,
     "height" : 600,
     "url"    : "https://cdn/600.jpg"
   },
   "smallPic" : {
     "width"  : 50,
     "height" : 50,
     "url"    : "https://cdn/50.jpg"
   }
 }
```

## GraphQL vs REST API Requests
GraphQL can be used as an alternative to REST API requests. With a REST API request, the client makes a request from a 'view' to an endpoint for a specific 'model' on the server. The server then sends the requested result back to the client. The problem with this method is that anytime a 'view' is updated _(by adding more data)_ the 'model' must also be updated. Over time this causes the size of the payload to increase because REST API requests cannot select an individual column of a database. All of the data has to be sent every time due to the fact that the iteration of both 'models' and 'views' are coupled.  
