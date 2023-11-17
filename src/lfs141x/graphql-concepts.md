# GraphQL concepts

## Enhancing reusability with fragments

A **fragment** is essentially a collection of fields on a particular type. They are used on the client-side to improve the structure and reusability of GraphQL code.

- Consider the normalized definition of the `User` type 

```GraphQL
type User {
    name: String!
    age: Int!
    email: String!
    street: String!
    zipcode: String!
    city: String!
}
```

- We can extract the details that relate to `User`'s address as follows

```GraphQL
fragment addressDetails on User {
    name # Who's address is this?
    street
    zipcode
    city
}
```

- Now the fragement can be referred to in queries and the server will understand the query

```GraphQL
# Query using the defined fragment
{
    allUsers{
        ...addressDetails
    }
}

# Equivalent query without the fragment
{
    allUsers {
        name
        street
        zipcode
        city
    }
}
```

> Fragments are useful when a set of fields is reused in multiple queries

## Parametrized fields with arguments

In GraphQL type definition, each field can accept 0 or more arguments. Each argument has a name and a type. Optionally, arguments can be given a default value

- Consider the following query definition

```GraphQL
type Query {
    allUsers(olderThan: Int = -1): [User!]!
}
```

- The `allUsers` query can now accept a single integer argument
- If not specified, it adopts the default value of `-1`, and all user records are returned
- If specified, it adopts the given integer value, all all user records that safisfy the age requirement are returned

```GraphQL
{
    allUsers(olderThan: 30){
        name
        age
    }
}
```

The query above will return the `name` and `age` fields of all `User` records that are older than 30 (years).

## Named query results with aliases

- Consider the following GraphQL query

```GraphQL
{
    User(id: "1") {
        name
    }
    User(id: "2") {
        name
    }
}
```

- This is an invalid query and the GraphQL will produce an error rather than the expected data
- The error occurs because the query is requested the **same field** with *different arguments*

```GraphQL
{
    first: User(id: "1") {
        name
    }
    second: User(id: "2") {
        name
    }
}
```

- To avoid the problem, we use **aliases** to bind the results of the parametrized fields
- The server will then name each `User` object according the specified aliases used in the query

```JSON
{
    "first": {
        "name": "Alice"
    },
    "second": {
        "name": "Sarah"
    }
}
```

## Advanced SDL

### Scalar types

Scalar types are used to represent **concrete** units of data. There are 5 predefined scalars in the GraphQL specification

1) `String`
2) `Int`
3) `Float`
4) `Boolean`
5) `ID`

GraphQL allows definition of custom scalars. A common scalar that is defined is the `date` type. All scalar types need to specify how its units are validated, serialized, and deserialized.

### Object types

> Object types have fields that express the properties of the type and are composable. These represent the entity in a system.

```GraphQL
type User {
    name: String!
    age: Int!
    email: String!
    street: String!
    zipcode: String!
    city: String!
}
```

### Enumeration types

> An `Enum` is a language feature that expresses the semantics of a type with a set of predetermined values

```GraphQL
enum Weekday {
    MONDAY
    TUESDAY
    WEDNESDAY
    THURSDAY
    FRIDAY
    SATURDAY
    SUNDAY
}
```

### Interfaces

- Used to define a type in an abstract way
- Specify a set of fields that a **concrate** type that implement an interface *need* to have
- To enforce that every type in a schema have an `ID` field, it could be done as follows

```GraphQL
interface Node {
    id: ID!
}

type User implements Node {
    id: ID! # Required field from interface
    name: String!
    age: Int!
}
```

### Union types

- Used to group a collection of other types
- Useful when specifying conditional fragments

```GraphQL
union Person: Adult | Child

type Adult {
    name: String!
    work: String!
}

type Child {
    name: String!
    school: String!
}
```

- A query that involves the `Person` union could look like

```GraphQL
{
    allPersons {
        __typename
        name
        ... on Child {
            school
        }
        ... on Adult {
            work
        }
    }
}
```

- Depending on the type of the `Person` object, the `school` field will be retrieved for a `Child` and the `work` field will be retrieved for an `Adult`
- The `__typename` field resolves to the typename of the current `Person` object
