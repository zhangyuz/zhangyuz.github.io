---
title: GraphQL Schemas and Types
date: 2020-03-16 22:15
categories: 
    - Graphql
tags:
    - Graphql
    - Android
    - Apollo-Android

---

本文将讨论GraphQL的类型系统和如何定义数据类型（由于 GraphQL 是一种规范，你可以用任何编程语言实现它，这里的讨论将不涉及任何特定的编程语言，只讨论规范中的概念定义）。

## Type system
GraphQL 的查询语句基本是从对象中查询数据域。查询语句的数据结构与查询返回的数据结构非常接近，但是我们仍然需要明确的知道服务器端可提供的数据：可供的选择的数据域、可能返回什么类型的对象、数据域子对象包含哪些数据域。为了解决这个问题，GraphQL引入了 Schema。

每个 GraphQL 的服务端都通过 Schema 定义了所有可用的数据类型集合。

## Type language
GraphQL 规范可以用任何编程语言实现，为了易于讨论 GraphQL Schema 的定义， 我们使用 "GraphQL schema language" 来描述 Schema 的定义。它类似于查询语句。

### Object types and fields
GraphQL Schema 最基本的组成是对象的定义，表示你可以从服务器获取的数据类型、对象的数据域：
```json
type Character {
    name: String!
    appearIn:[Episode!]!
}
```

上边的定义非常易读，解读如下（这样就可以统一我们对GraphQL Schema 的概念了）：
- `Character` 是 *GraphQL 的对象类型* *（GraphQL Object Type）* ，表示这个类型包含一些其他数据域。
- `name` 和 `appearsIn` 是 `Character` 类型的数据域。它的意义是在任何对 `Character` 的查询中最多只能返回这两个数据域。
- `String` 是 GraphQL 内置的 *标量（scalar）* 类型。这些类型不再包含子数据域。
- `String!` 表示这个数据域 *不可为空（non-nullable）*, 服务端保证在任何请求中如果需要都会保证返回这个数据域。
- `[Episode!]!` 表述 `Episode` 数组，不可为空，并且数组中的每个元素也不可为空。

## Arguments
GraphQL 对象的没个数据域都可以包含0到多个参数，比如：
```json
type Starship {
    id: ID!
    name: String!
    length(unit: LengthUnit = METER): Float
}
```
所有的参数必须有名字，并且传递参数时必须指定参数名。参数可以是必选或可选参数，对于可选参数需要定义参数默认值。

## The Query and Mutation types
Schema中多数的类型定义都类似于普通对象，但是有两个类型是被特别处理的：
```json
schema {
    query： Query
    mutation：Mutation
}
```
GraphQL 服务端必须包含 `query` 类型， `matation` 类型是可选的。他们与普通类型的定义一样，但是服务器端会特别处理它们，因为他们定义了查询操作的入口。

```json
query {
  hero {
    name
  }
  droid(id: "2000") {
    name
  }
}
```
```json
{
  "data": {
    "hero": {
      "name": "R2-D2"
    },
    "droid": {
      "name": "C-3PO"
    }
  }
}
```
如果上边的 query 是可用的，那么就意味着服务器端在 `Query` 类型中定义了 `hero` 和 `droid`  两个数据域：
```json
type Query {
    hero(episode: Episode): Character
    droid(id:ID!): Droid
}
```
Mutation 与 Query 类似。你需要定义 `Mutation` 类型中的数据域。
Mutation 和 Query 类型除了是 schema 的入口外，与其他类型的定义没有区别。

## Scalar types
GraphQL 的对象类型至少包含了名字和数据域，优势这些数据域要被解析成一些基础类型。这就是 scalar 类型存在的原因：他们代表了查询的叶子节点（译注：个人理解类似Java中的基本数据类型）：
```json
# query
{
    hero {
        name
        appearIn
    }
}
```
```json
{
    "data": {
        "hero": {
            "name": "R2-Dd2",
            "appearIn": [
                "NEWHOPE",
                "EMPIRE",
                "JEDI"
            ]
        }
    }
}
```
之所以说 `name` 和 `appearIn` 是标量类型（scalar type），是因为 query 中他们就是叶子节点。
GraphQL 内置了默认的标量类型，可以直接使用：
- `Int`: 32位有符号整数。
- `Float`：有符号双精度浮点值。
- `String`：UTF-8 字符串。
- `Boolean`：`true` 或 `false`。
- `ID`：表示唯一的标识符，通常用来做对象缓存的标识使用，本质与字符串一样，not hunman-readable。

在多数的 GraphQL 服务端支持自定义标量类型。比如 `Date`：
```json
scalar Date
```
接下来需要服务端来具体实现 `Date` 类型的序列化、反序列化和验证的方式。比如你可以指定 `Date` 类型序列化为整型的时间戳，客户端需要知道如何处理这个类型。

## Enumeration types
枚举类型也称为 `Enums`，是一类特别的标量，它的值限定在一些特定的值中。这允许你：
1. 验证此类型的参数都是可用的值
2. 在整个类型系统中明确某个数据域必须是某些特定值

比如：
```json
enum Episode {
    NEWHOPE
    EMPIre
    JEDI
}
```
这表示任何时候你使用 `Episode` 类型对象的时候，它的值只能是 `NEWHOPE`、 `EMPIRE`、 `JEDI`中的一个。

## Lists and Non-Null
在 GraphQL 中只能定义对象（Object types）、标量（scalar）和枚举（enums）这三类数据类型。同时也定义了类型修饰符来协助你验证数据的值：
```json
type Character {
    name: String!
    appearIn: [Episode]!
}
```
上边的例子，使用`!` 表示那么数据不可为空，如果出现空值会导致服务器内部错误，并应该通知客户端。

非空修饰符还可以用于查询变量的定义，如果不满足，返回验证错误信息：
```json
# query
query DroidById($id:ID!) {
    droid(id:$id) {
        name
    }
}
# variables
{
    "id":null
}
```
```json
{
  "errors": [
    {
      "message": "Variable \"$id\" of required type \"ID!\" was not provided.",
      "locations": [
        {
          "line": 1,
          "column": 17
        }
      ]
    }
  ]
}
```

数组也是类似的，用方括号（List修饰符）标识数据的类型是 List,将会返回对象的数组。非空修饰符和List修饰符可以组合使用：
```json
# List 本身可以为空，但 List 中的元素不可以为空
myField: [String!]
```
```json
myField: null                   // valid
myField:[]                      // valid
myField:['a', 'b']              // valid
myField:['a', null, 'b']        // error
```
```JSON
# List 本身不可空，List 中元素可为空
myField: [String]!
```
```json
myField: null                   // error
myField: []                     // valid
myField: ['a', 'b']             // valid
myField: ['a', null, 'b']       // valid
```

## Interfaces
就像其他的类型系统，GraphQL 也支持接口类型。*接口（Interface）* 是一个抽象类型，它包含数据域的定义，所有继承它的类型都必须 包含这些数据域：
```json
# 定义 Character 接口表示任何角色
interface Character {
    id: ID!
    name: String!
    friends: [Character]
    appearsIn: [Episode]!
}
```

任何实现 `Character` 的类型都要包含这些数据域、参数和返回值类型：
```json
# Character 的子类，必须包含 Character 类型的所有数据域，同时增加自己的数据域
type Human implements Character {
    id: ID!
    name: String!
    friends: [Character]
    appearsIn: [Episode]!
    starships: [Starship]
    totalCredits: Int
}
type Droid implements Character {
    id: ID!
    name: String!
    friends: [Character]
    appearsIn: [Episode]！
    primaryFunction: String
}
```
注意下边的查询会导致错误：
```json
query HeroForEpisode($ep: Episode) {
    hero(episode: $ep) {
        name
        primaryFunction
    }
}
# variables
{
    "ep": "HEDI"
}
```
```json
{
  "errors": [
    {
      "message": "Cannot query field \"primaryFunction\" on type \"Character\". Did you mean to use an inline fragment on \"Droid\"?",
      "locations": [
        {
          "line": 4,
          "column": 5
        }
      ]
    }
  ]
}
```
出错的原因是 hero 返回值类型是 `Character` ，根据 `episode` 参数的不同，可能是 `Human` 或 `Droid` 类型。但 `primaryFunction` 不是 `Character` 类型的数据域，所以会出错。你可以用内联（in-line）fragment 来实现目标：
```JSON
query HeroForEpisode($ep: Episode) {
    hero(episode: $ep){
        name
        ... on Droid {
            primaryFunction
        }
    }
}
# variables
{
    "ep": "JEDI"
}
```
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "primaryFunction": "Astromech"
    }
  }
}
```

## Union types
Union 与 接口类型类似，但是不能指定重复的数据类型。
```json
union SearchResuld = Human | Droid | Starship
```

在返回 `SearchResult` 的时候，可能返回 `Human` `Droid` 或 `Starship` 类型。注意 union 的组成类型必须是具体的类型，不能是抽象类型（比如 interface）。在这种情况下，如果你的query可能返回这三种数据类型，那么你就需要额外的meta信息来区分返回的数据类型：
```json
{
  search(text: "an") {
    __typename
    ... on Character {
      name
    }
    ... on Human {
      height
    }
    ... on Droid {
      primaryFunction
    }
    ... on Starship {
      name
      length
    }
  }
}
```
Note that name is still specified on Starship because otherwise it wouldn't show up in the results given that Starship is not a Character!

## Input types
到目前为止，我们只讨论了传递标量值（枚举、字符串）的情况。GraphQL 也支持同时传递复杂类型，尤其在 Mutation 操作中更有益处。在 GraphQL Schema 中，输入类型与其他类型一样，但是使用 input 关键字来定义：
```json
# 定义输入类型
input ReviewInput {
    stars： Int！
    commentary： String
}
```
```json
# 使用输入
mutation CreateReviewForEpisode($ep:Episode, $review: ReviewInput) {
    createReview（episode:$ep, review:$review） {
        stars
        commentary
    }
}
# variables
{
    "ep": "JEDI",
    "review": {
        "stars": 5,
        "commentary": "This is a great movie!"
    }
}
```
```json
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```
The fields on an input object type can themselves refer to input object types, but you can't mix input and output types in your schema. Input object types also can't have arguments on their fields.

