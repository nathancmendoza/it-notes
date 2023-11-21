# GraphQL security

## Timeout

- Use a timeout (time limit) to defend against large queries
  - Server does not need to know anything about the query
  - Server simply stops executing a query whose runtime exceeds the timeout
- Very simple to implement and should act as a **final** line of defense
- Cutting a connection may result in *unexpected behavior*

## Maximum query depth

- GraphQL schema are often cyclic graphs
- We can prevent abusive queries by implementing a **maximum query depth** and analyzing an incoming query's structure
- The GraphQL server is able to reject or accept a query based on the request's depth
- Queries are analyzed when they reach the server; rejected queries would **not** execute, minimizing impact on server load
- **WARNING**: depth alone is not enough to protect against *all abusive queries*

```GraphQL
query IAmEvil {            #Depth: 0
  author(id: "abc") {      #Depth: 1
    posts {                #Depth: 2
      author {             #Depth: 3
        posts {            #Depth: 4
          author {         #Depth: 5
            .....          #Depth: 6
          }
        }
      }
    }
  }
}
```

> A GraphQL server configured with a maximum query depth of `3` would reject this query

## Query complexity

- Certain fields in a scheme may require more computation than others
- Query complexity allows developers to define the complexity of particular fields
- Servers can be configured to accept queries that are below the **maximum query complexity** allowed
- Complexity of fields are typically defined using a simple numbering scheme
  - Individual fields can be assigned different complexities
   Complexities can vary based on arguments like requesting the last `n` posts
- Implementing query complexity can provide more flexibility to clients but can be difficult to do so perfectly
  - Needs to be kept up to date manually by developers
  - Mutations are hard to estimate, especially if they have side effects like background jobs
```GraphQL
query {
  author(id: "abc") {          #complexity: 1
    posts {                    #complexity: 1
      title                    #complexity: 1
    }
  }
}
```

> Simple addition tells us the total complexity of this query is 3; a server configured with a maximum complexity of 2 would reject this query

## Throttling

> Previous solutions are great at preventing Individual abusive queries, but fall short when a client makes a lot of requests in a short timespan

- In a REST API, a throttle stops a client from requesting resources too often
- In GraphQL, throttling on the number of request is not very helpful since queries can have varying levels of complexity
- Instead, we can utilize the [Leaky Bucket Algorithm](https://en.wikipedia.org/wiki/Leaky_bucket) algorithm to throttle a client

### Based on server time

- Say a client has a maximum server time of 1000ms
- Clients gain an additional 100ms of extra server time every second
- Clients cannot "save up" time; any addition time that does not fit within the maximum time is lost

```GraphQL
mutation {
  createPost(input: { title: "GraphQL security"}) {
    post {
      title
    }
  }
}
```

- Say the mutation above takes 200ms on average to execute
- A new client will be able to execute this mutation 5 times within 1 second before they get throttled and have to wait
- After 2 seconds, the client would be able to execute the mutation again

> This is a great way to rate limit a GraphQL API so no 1 client gets to execute more queries than any other

### Based on query complexity

- Say a client has a maximum query complexity of 10
- Clients gain an additional 1 query complexity every second
- Clients cannot "save up" query complexity; any additional complexity that does not fit within the maximum amount is lost

```GraphQL
query {
  author(id: "abc") {          #complexity: 1
    posts {                    #complexity: 1
      title                    #complexity: 1
    }
  }
}
```

- The query above has a total complexity of 3 as seen earlier
- A new client would be able to execute this query 3 times within 1 second before getting throttled
- After 2 seconds, the client would be able to execute the query again

> This is an even better way to rate limit a GraphQL API because the limits are **more clear** to clients

## Whitelisting

- The server is only allowed to execute a **predetermined list of queries** made by clients
- This is advantageous to APIs that are not publicly accessible
