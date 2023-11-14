# GraphQL clients

## GraphQL on the client side

- Develops new abstractions and implement common functionalities
    1) Directly send queries/mutations without HTTP requests
    2) Use view-layer integrations
    3) Automatically cache results
    4) Validate/optimize queries based on a schema
- Can still use regular HTTP requests and fiddle with responses yourself
- There are **2** major GraphQL clients availabe
    - [Apollo](https://www.apollographql.com): Community driven GraphQL API layer 
    - [Relay](https://relay.dev): Facebook's homegrown GraphQL API layer (webapps only)
- Fetch and update data in a declarative manner
- UI integration depends on the platform and/or framework being used

## Caching query results

- Cached information provides a smooth user experience
- Remote information is stored in a local store for later use

### Naive

- Store results of a query as is and use it as a mapping of the query executed to retrieve it
- When the same query is re-run, fetch result from local cache rather than remote server

### Better

- Normalize the data before storing
- Query result is flattened and values can be references with a globally unique ID

## Build-time schema validation and optimization

- Schema maps out all data a client can fetch
- Build environment can parse and verify usage at build time rather than error occuring in user's hands

## Colocating views and data dependencies

- UI code and data dependencies exists *side by side*
    1) Tight coupling greatly improves developer experience
    2) Greatly reduces mental overhead
- Colocation effectiveness depends on the development platform
    - JavaScript applications can have them in the same file 
    - Xcode uses an assistant editor to expose both at the same time
