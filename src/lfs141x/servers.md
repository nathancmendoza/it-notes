# GraphQL servers

## GraphQL execution

### Example schema

```GraphQL
type Query {
    author(id: ID!): Author 
}

type Author {
    posts: [Post]
}

type Post {
    title: String
    content: String
}
```

### Executing example query 

```GraphQL
Query {
    author(id: "abc") : Author {
        posts: [Post] {
            title
            content
        }
    }
}
```

- Every field in a query can be associated with a type
- Execution starts and the **query type** and goes **breadth-first**
    1) `Query.Author` resolver is run first 
    2) Resulting author object is passed into resolver for `Author.posts`
    3) Resulting list is processed for requested field's resolver
- Execution finishes by structuring data as requested in the query and sending back the result

```JavaScript
let author =  Query.Author(root, {id: "abc"}, context);
let posts = Author.posts(author, null, context);
for (post in posts) {
    let title = Post.title(post, null, context);
    let content = Post.content(post, null, context);
}
```

> Most GraphQL implementations offer **default resolvers**, eliminating the need to specify one for *every* field

## Batched resolving

- The execution process above is somewhat naive
    - Imagine a GraphQL layer that calls on another backend component in a resolver function
    - In a single query, the backend component can be invoked several times

```GraphQL
Query {
    posts {
        title
        author {
            name
            avatar
        }
    }
}
```

- If these are posts on a blog, there is a good chance that multiple posts have the same author 
- Fetching the author objects of multiple posts might cause multiple, unnecessary calls to the author backend component

```JavaScript
fetch('/authors/1');
fetch('/authors/2');
fetch('/authors/1');
fetch('/authors/2');
fetch('/authors/1');
fetch('/authors/2');
```

### Loader utilities

- A loader utility can wait for all resolvers to finish and only fetch what is necessary

```JavaScript
authorLoader = new authorLoader();
authorLoader.load(1);
authorLoader.load(2);
authorLoader.load(1);
authorLoader.load(2);
```

- Loader utility acts like a memoization utility to fetch only if the result has not been computed
- When multiple requests are made, there are only loaded from the backend **once**

### Batch requests

- This requires the backend component to support the notion of batch requests
- It should allow `fetch('/authors?ids=1,2)` to get the author objects if the specified list of IDs in one request
