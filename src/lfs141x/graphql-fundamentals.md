# GraphQL fundamentals

## What is GraphQL?

- A new API standard that is more efficient, powerful, and flexible than REST
- Enables **declarative** data fetching
- Exposes a **single endpoints** and responds to *queries*

### An efficient alternative to REST

- REST APIs worked well when client applications were simple
- Reasons REST became difficult
    1) Increased mobile usage
    2) Variety of frontend frameworks
    3) Faster development speed and expectation for rapid feature development

### A history of GraphQL

- Facebook used in all the way back in 2012
- Facebook presented it at 2015 React.js conf
- GraphQL is **not exclusive** to React; can be used with any programming language

## GraphQL is the better REST

> GraphQL is an *evolution* of what REST offers by addressing the **inflexibility and inefficiency** of REST

### REST vs GraphQL: data fetching

We want to fetch the following

- A blog user's posts
- A blog user's 3 most recent followers

#### RESTful API

- Typically would need to make requests to 3 different endpoints
    1) `/users/<id>` for user information
    2) `/users/<id>/posts` for posts
    3) `/users/<id>/followers` for followers
- Endpoints return static templated responses that contain extraneous information
- Client is forced to sift through response to find the desired information

#### GraphQL API

- Make a request to a single endpoint with a query

```GraphQL
query {
    User(id: "<user-id>") {
        name
        posts {
            title
        }
        followers(last: 3) {
            name
        }
    }
}
```

- Only make a single request and get back exactly what was requested

### Overfetching and underfetching

- Overfetching
    - REST endpoints return additional information that the client may not have needed
    - Problematic in low-resource environments to process excess data
- Underfetching
    - May require multiple requests to get desired information
    - Example: fetch the post and followers of multiple users
        1) Request user list from `/users` endpoint
        2) Loop through each user to get their Id 
        3) Fetch each user's posts and followers
- Fetching with GraphQL
    - Request the user, their posts, and their followers in a *single* query
    - Exact data gets returned to the client in a *single* response

### Fast product iterations

- Structuring endpoints effectively
    - REST APIs made endpoints that were tailor-made for views inside the app 
    - If app requirements change, endpoints would need to be modified or new endpoints need to be defined
    - GraphQL allows client to specify exactly what they need and can modify their query without updating the backend
    - This provides insightful analytics on the backend
- Schema and type system
    - SDL: **s**chema **d**efinition **l**anguage
    - Acts as a contract between the frontend and backend components of an applications

## GraphQL key concepts

### The schema definition language (SDL)

> SDL is the syntax for specifying the schema of an API

```GraphQL
type Person {
    name: String!
    age: Int!
}
```

- The type `Person` consists of 2 fields
    - A string for the person's name
    - An integer for the person's age
- The exclamation point (`!`) specifies that the field is **required**

```GraphQL
type Post {
    title: String!
    author: Person!
}

type Person {
    name: String!
    age: Int!
    posts: [Post!]!
}
```

- Relationships can be specified by using existing types are part of the schema of new types
- The example above specifies a 1-to-many relationship between the `Post` and `Person` types

### Fetching data

#### Queries

- The `allPersons` field is this query is the **root** field
- Everything following the root is the **payload** of the query

```GraphQL
{
    allPersons {
        name
    }
}
```

- This will return a list of all persons currently stored in the database
- Each person in the list will only contain the `name` field
- To include the `age` field in the payload, simply update the query

```JSON
{
    "allPersons": [
        { "name": "Johnny" },
        { "name": "Sarah" },
        { "name": "Alice" }
    ]
}
```

- GraphQL allows for querying of nested types
- A query for "All the title of a person's posts" would look like

```GraphQL
{
    allPersons {
        name
        posts {
            title
        }
    }
}
```

#### Arguments

- Each field can have arguments that modify the query's results
- The `last: 2` argument on the `allPersons` query limits the number of `Person` to the last 2 

```GraphQL
{
    allPersons(last: 2) {
        name
        age
    }
}
```

#### Mutations

- A mutation starts with the `mutation` keyword
- The **root** of a mutation takes in the arguments needed to create the specified type
- Like with a query, the field of the newly create type can be returned (similer to SQL's, `RETURNING` clause)

```GraphQL
mutation {
    createPerson(name: "Bob", age: 36) {
        name
        age
    }
}
```

- A typical pattern is the return the ID of the newly created type
- GraphQL will create **unique IDs** for each type that it creates

```GraphQL
mutation {
    createPerson(name: "Bob", age: 36) {
        id
    }
}
```

#### Subscriptions

Subscriptions allows access to real-time data. It avoids the typical request-response pattern of an API by directly streaming data to the client. The process is as follows

1) A **web sockect** is initiated when a client subscribes
2) The server pushes updates to the client

```GraphQL
subscription {
    newPerson {
        name
        age
    }
}
```

> Whenever a new `Person` is created, the server sends information about the new `Person` to the client

### Putting it all together

- The schema is the most important part of an GraphQL API
    - It defines the capabilities of an API by specifying how a client fetches and updates data
    - It represents a **contract** between the client and server
    - It is a collection of GraphQL types with special *root types*

```GraphQL
type Query {
    allPersons(last: Int): [Person!]!
}

type Mutation {
    createPerson(name: String!, age: Int!): Person!
}

type Subscription {
    newPerson: Person!
}
```

- The snippet above defines actions that the client can take on the server
    1) Query the persons stored on the server (optionally limiting the number)
    2) Creating a new `Person` object (can also support updating and deleting)
    3) Subscribing to an event

```GraphQL
type Person {
    id: ID!
    name: String!
    age: Int!
    posts: [Post!]!
}

type Post {
    title: String!
    author: Person!
}
```

- The snippet above defines types that the server knows about and which the client can reference

## GraphQL architecture

### Use cases

1) Server with a connected database
    - Most common for green field project
    - A single server implementing the GraphQL specification
        1) Parse requests
        2) Fetch data from database
        3) Construct response
        4) Return results to client
    - Can be used with any available network protocol
    - Can be used with any database format
2) GraphQL layer that integrates existing systems
    - Unification of multiple existing system
    - Useful for companies with legacy systems
    - Hides the complexity of legacy systems behind a nice GraphQL layer
3) Hybrid with a mix of connected database and existing systems
    - Combination of the two previous approaches
    - Resolves and retrieves data appropriately

> These use cases demonstrate how easily GraphQL can be adopted into *any* system

### Resolver functions

- Each field in a GraphQL query corresponds to a resolver function
- The purpose of a resolver function is to fetch the data for its field

```GraphQL
query {
    User(id: "<id>") {
        name
        friends(first: 5) {
            name
            age
        }
    }
}
```

- The following resolver functions would be invoked
    - `User(id: String!): User`
    - `name(user: User!): String`
    - `age(user: User!): Int`
    - `friends(first: Int, user: User!): [User!]!`
- No other resolver are invoked

### GraphQL client libraries

- GraphQL eliminates the shortcomings of REST
    - Overfetching and underfetching is no longer an issue
    - Powerful machines take care of computational work 
    - Client uses a single, coherent, and flexible API
- Fetching data from a REST API
    1) Construct and send an HTTP request
    2) Receive and parse the server's response
    3) Store the response locally
    4) Display the data in the UI
- Ideal data fetching approach 
    1) Describe data requirements
    2) Display the data in the UI

> GraphQL client libraries like [Relay](https://relay.dev) and [Apollo](https://www.apollographql.com) provide the abstraction needed to focus on important parts of the application
