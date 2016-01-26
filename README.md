# Learn GraphQL [Work In Progress!!!]
![GraphQL nodeJS logo](https://www.reindex.io/blog/building-a-graphql-server-with-node-js-and-sql/GraphQL_NodeJS.png)  
Learn to use GraphQL - A query language that allows client applications to specify their data fetching requirements _(it's really just a way of thinking about data!)_

## What is GraphQL?
GraphQL is a query language that sits on top of your existing application code. It allows you to make a query to your application data model that returns a JSON result that is very similar to your query.

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
## An Alternative to REST API requests
