# Learn GraphQL [Work In Progress!!!]
![GraphQL nodeJS logo](https://www.reindex.io/blog/building-a-graphql-server-with-node-js-and-sql/GraphQL_NodeJS.png)  
Learn to use GraphQL - A query language that allows client applications to specify their data fetching requirements _(it's really just a way of thinking about data!)_

## What is GraphQL?
GraphQL is a query language that sits on top of your existing application code. It allows you to make a query to your application data model that returns a JSON result that is very similar to your query. GraphQL enables queries to be specified on the client side which changes the client-server relationship.

Here's a basic example of a GraphQL query and the returned result:

GraphQL query
```
 {
   user {
     name
   }
 }
```
JSON result
```json
 {
   "user" : {
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
Which would return a result like this:
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


## Aliasing
Aliasing allows you to return different version results of the same object. For example if you wanted to make another request for a profile picture but this time you wanted both a large and a small version, you could write two request aliases like this:

```
 {
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
 }
```
Which returns both the "bigPic" and the "smallPic":
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

## Descending Down Connections
GraphQL provides a natural way of expressing the hierarchical data that you want. You say _declaritively_ exactly what you want and you get a response that looks exactly like the query. Say you wanted to return a list of names of the events that your friends are going to, you could write a query like this:

```
 {
   user {
     name,
     friends {
       name,
       events {
         name
       }
     }
   }
 }
```
The result would look something like this:
```json
 {
   "user" : {
     "name" : "John Smith",
     "friends" : [
       {
         "name" : "Ben Thompson",
         "events" : [
           {
             "name" : "Don't Pitch Me Bro!"
           },
           {
             "name" : "Apps Junction"
           }
         ]
       },
       {
         "name" : "Kate Wright",
         "events" : [
           {
             "name" : "Don't pitch Me Bro!"
           }
         ]
       }
     ]
   }
 }
```
## Type System
Every GraphQL server exposes an application specific schema to those who use it. You can create your types in either a ```.js``` file or a ```schema.graphql``` file. At every step along the way with a query you know exactlywhat **_type_** you have at each specific node which means that you know exactly what type of fields you can query. As you descend down through the query, different **_types_** become in context. Let's have a look at an example:

```
 {
   user {
     name,
     profilePicture(size: 300) {
       width,
       height,
       url
     },
     friends(orderby: IMPORTANCE, first: 1) {
       name,
       events(first: 1) {
         name
       }
     }
   }
 }
```
With every GraphQL schema there is a **_root_** type in context that determines what nodes you can start your query at. Let's break it down. The root type in our example is the 'user' node:

```
 {
   user
```
The root node has a type 'Query':
```js
 type Query {
   user(id: Int) : User
 }
```
Let's move down the query...
```
 name,
 profilePicture(size: 300),
 friends(orderby: IMPORTANCE, first: 1)
```
The friends node takes the type 'User' which is the same object structure as this one because GraphQL is a coherent type system.
```js
 type User {
   name : String
   profilePicture(size: Int = 50) : ProfilePicture
   friends(first: Int, orderby: FriendOrderEnum) : [User]
   events(first: Int) : [Event]
 }

 enum FriendOrderEnum {
   FIRST_NAME,
   LAST_NAME,
   IMPORTANCE
 }
```
If you have thousands of different data types, it can become expensive to maintain an index for them which means that you'll need to make an explicit decision _(whitelisting)_ as to what you can order your data by. This is where the FriendOrderEnum comes in and it's saying that it can provide ordering by ```FIRST_NAME```, ```LAST_NAME``` or ```IMPORTANCE```. It can only provide these explicit options because it can't guarantee that any others will be scalable (across millions of users). The ```IMPORTANCE``` enum is an example taken from Facebook where they calculate an importance score based on your interactions with your friends and then ranks them in order.

The **_type system_** is the method through which data is accessed with GraphQL.



## GraphQL vs REST API Requests
GraphQL can be used as an alternative to REST API requests.

#### REST API
With a REST API request, the client makes a request from a 'view' to an endpoint for a specific 'model' on the server. The server then sends the requested result back to the client. The problem with this method is that anytime a 'view' is updated _(by adding more data)_ the 'model' must also be updated. Over time this causes the size of the payload to increase because REST API requests cannot select an individual column of a database. All of the data has to be sent every time due to the fact that the iteration of both 'models' and 'views' are coupled. This also means that multiple requests will have to be sent to multiple endpoints to retrieve different types data.

#### GraphQL
With a GraphQL query, the server expresses the possibilities of what you can query across the entire graph of your application using the type system. The client specifies its requirements via a specific GraphQL query. This means that the 'models' and 'views' can now be iterated on the client which removes an element of client-server coupling that's pretty undesirable. The client asks the server for a certain data shape and then the server replies with the relevant data. This is the equivalent to having just one endpoint with access to the complete data model of your application.

GraphiQL

## Fragments
As the products you build become more complex, so too do your GraphQL queries. To tackle this GraphQL provides a method of decomposition called **_fragments_**. They are reusable as they can be used across many queries _(a fragment can really be classed as sub-query)_

Here's an example of a fragment, we can use one of the previous examples to demonstrate it:

```
 {
   user {
     name
     friends {
       name
       events {
           name
       }
     }
   }
 }
```
This query can be split. Here we've split it on the friends node and created a friendFragment. We've specified that the fragment is on the 'User' type of the user node:
```
 {
   user {
     name
     friends {
       ...friendFragment
     }
   }
 }

 fragment friendFragment on User {
   name {
     events {
       name
     }
   }
 }
 ```
