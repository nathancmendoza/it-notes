# GraphQL FAQs

## Is GraphQL a database technology?

No. GraphQL is often confused with being a database technology. This is a misconception, GraphQL is a query language for APIs - not databases. In that sense it’s database agnostic and can be used with any kind of database or even no database at all.

## Is GraphQL only for React/JavaScript Developers?

No. GraphQL is an API technology so it can be used in any context where an API is required.

On the backend, a GraphQL server can be implemented in any programming language that can be used to build a web server. Next to Javascript, there are popular reference implementations for Ruby, Python, Scala, Java, Clojure, Go and .NET.

Since a GraphQL API is usually operated over HTTP, any client that can speak HTTP is able to query data from a GraphQL server.

Note: GraphQL is actually transport layer agnostic, so you could choose protocols other than HTTP to implement your server.

## How does server-side caching work with GraphQL?

One common concern with GraphQL, especially when comparing it to REST, are the difficulties to maintain server-side cache. With REST, it’s easy to cache the data for each endpoint, since it’s sure that the structure of the data will not change.

With GraphQL on the other hand, it’s not clear what a client will request next, so putting a caching layer right behind the API doesn’t make a lot of sense.

Server-side caching still is a challenge with GraphQL. More information about caching can be found on the [GraphQL](https://graphql.org/learn/caching/) website.

## How does authentication and authorization work with GraphQL?

Authentication and authorization are often confused. [Authentication](https://www.howtographql.com/react-apollo/5-authentication/) describes the process of claiming an identity. That’s what you do when you log in to a service with a username and password, you authenticate yourself. [Authorization](https://graphql.org/learn/authorization/) on the other hand describes permission rules that specify the access rights of individual users and user groups to certain parts of the system.

Authentication in GraphQL can be implemented with common patterns such as [OAuth](https://oauth.net/).

To implement authorization, it is recommended to delegate any data access logic to the business logic layer and not handle it directly in the GraphQL implementation. If you want to have some inspiration on how to implement authorization, you can take a look to [Graphcool’s permission rules](https://www.graph.cool/).

## How does GraphQL handle errors?

A successful GraphQL query is supposed to return a JSON object with a root field called "data". If the request fails or partially fails (e.g., because the user requesting the data doesn’t have the right access permissions), a second root field called "errors" is added to the response:

```JSON
{
  "data": { ... }, 
  "errors": [ ... ] 
}
```

For more details, you can refer to the [GraphQL specification](http://spec.graphql.org/).

## Does GraphQL support offline usage?

GraphQL is a query language for (web) APIs, and in that sense by definition only works online. However, offline support on the client-side is a valid concern. The caching abilities of Relay and Apollo might already be enough for some use cases, but there isn’t a popular solution for actually persisting stored data yet. You can gain some more insights in the GitHub issues of [Relay](https://github.com/facebook/relay/issues/676) and [Apollo](https://github.com/apollographql/apollo-client/issues/424) where offline support is discussed.

One interesting approach for offline usage and persistence can be found in the following blog post ["Offline GraphQL Queries with Redux Offline and Apollo"](http://www.petecorey.com/blog/2017/07/24/offline-graphql-queries-with-redux-offline-and-apollo/?from=east5th.co) by Pete Corey.
