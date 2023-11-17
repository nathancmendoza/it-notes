# GraphQL tooling and ecosystem

## Instrospection

Schema designers already know what a schema looks like, but how can a client discover what is accessible from a GraphQL API?

- GraphQL provides the ability to query the `__schema` metafield to provide this information
- It **must** always be available as the root type of the query

```GraphQL
query {
    __schema {
        types {
            name
        }
    }
}
```

- Consider the following example schema to be hosted on a GraphQL server

```GraphQL
type Query { 
    author(id: ID!): Author 
}

type Author { 
    posts: [Post!]! 
}

type Post { 
    title: String! 
}
```

- The instrospection query would yield the following, providing information on both object and scalar types that are defined

```JSON
{
    "data": { 
        "__schema": { 
            "types": [ 
                { 
                    "name": "Query" 
                }, 
                { 
                    "name": "Author" 
                }, 
                { 
                    "name": "Post" 
                }, 
                { 
                    "name": "ID" 
                }, 
                { 
                    "name": "String" 
                }, 
                { 
                    "name": "__Schema" 
                }, 
                { 
                    "name": "__Type" 
                }, 
                { 
                    "name": "__TypeKind" 
                }, 
                { 
                    "name": "__Field" 
                }, 
                { 
                    "name": "__InputValue" 
                }, 
                { 
                    "name": "__EnumValue" 
                }, 
                { 
                    "name": "__Directive" 
                },
                { 
                    "name": "__DirectiveLocation"
                }
            ] 
        } 
    } 
}
```

- Instrospection types can also be instrospected with the `__type` query

```GraphQL
{
    __type(name: "Author") {
        name
        description
    }
}
```

> Instrospection is a powerful GraphQL feature that can be used to provide amazing features such as documentation generation, autocomplete, code generation and more.

## GraphiQL

- A powerful "GraphQL IDE" for working with GraphQL APIs interactively
- Features an editor for GraphQL queries, mutations, and subscriptions, equipped with autocompletion and validation
- A very helpful tool for development and debugging queries without writing plain GraphQL
