## Demo Time!

The code in this directory is for the
***Getting Started*** guide:
http://graphql.org/docs/getting-started/

It is the *gentelest* introduction to GraphQL available.

To run the code on your local machine, 

Clone this repo:

```sh
git clone https://github.com/dwyl/learn-graphQL.git && learn-graphQL/graphql-demo
```

Then install the dependencies:

```sh
npm install
```

Start the GraphQL (Express) Server

```sh
npm start
```

Now Visit: http://localhost:3000/graphql?query={user%28id:%221%22%29{name}}
(*in your web browser*)

You should expect to see:

```json
{
  "data": {
    "user": {
      "name": "Dan"
    }
  }
}
```

### Understanding the GraphQL Query

The query portion in the url you visited above is:

```js
{
  user(id:"1") { 
    name
  }
}

In *English* this query is requesting the user with `id` **1** and asking for the `name` field to be returned in the result.
The `id` is a refered to as a `field argument`.