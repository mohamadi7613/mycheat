# Graphql

## Restfull issues
1. Url  schema gets tough when we have heavily nested relationships
2. when we have too many http requests
3. when we are vulnerable to over fetching data, for ex, when I want to fetch a specific data maybe we need to read the whole data of table.


## Express.js
```js
const express = require('express');
const expressGraphQL = require('express-graphql').graphqlHTTP;
const schema = require('./schema/schema.js');      // from schema file
const app = express();

app.use('/graphql',expressGraphQL({         // register graphql use a middleware with a configuration
    schema: schema,                           // pass schema file
    graphiql: true                          // graphiql is a tool inside browser for testing
}))
app.listen(4000,()=>{
    console.log('Listening');
})
```

## Schema File

| schema\
|--- schema.js
```js
// schema file is what tells grapql what our data is looks like
const graphql = require('graphql');
const _ = require('lodash');        // helper
const {
    GraphQLObjectType,
    GraphQLString, GraphQLInt,
    GraphQLSchema,
} = graphql         // destructure from grapql


const UserType = new GraphQLObjectType({
    name: 'User',           // required property // what our type should be called
    fields: {             // what properties that user have
        id: {type: GraphQLString},   // name and fields are required properties
        first_name: {type: GraphQLString} ,
        age: {type: GraphQLInt}
    }
})

const RootQuery = new GraphQLObjectType({         // recives all the queries
    name: 'RootQueryType',
    fields: {
        user:{
            type: UserType,
            args:{ id: {type: GraphQLString}},     // a query should have at least one arg which is id
            resolve(parentValue,args){       // return a promise for getting actual data from db
                return axios.get(`https://jsonplaceholder.com/users/${args.id}`).then(res=>res.data)      // args.id comes from uesr query
            }
        }
    }

})

module.exports = new GraphQLSchema({
    query: RootQuery
})

// {  // run this code inside browser graphiql and you get data
//     user:(id: '241'){     // 241 is an args which we set in root_query  
//         id,               // this query will send to root_query 
//         first_name,       // return three itmes
//         age
//     }
// }
```
















