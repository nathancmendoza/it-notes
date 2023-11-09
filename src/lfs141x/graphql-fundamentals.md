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
