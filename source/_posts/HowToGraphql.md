---
title: N 个小时入门 Graphql
date: 2020-03-14 12:03
categories: 
    - Graphql
tags:
    - Graphql
    - Android
    - Apollo-Android

---

> 本文是笔者学习 GraphQL 的笔记，参考了 GraphQL.org 的官方文档。本文仅做学习记录。

## GraphQL 简介
Graphql 是一种面向 API 的查询语言，面向强类型数据查询的服务端运行环境。它不与任何数据库绑定，而是基于当前已有的数据、代码来支持它的运行。

GraphQL 服务需要定义数据类型和数据类型中的数据成员，并且为每个数据成员提供对应的数据处理函数：
```graphql
# 类型定义
type Query {
    me: User
}
type User {
    id: ID
    name: String
}
# 数据处理函数
function Query_me(request) {
    return request.auth.user;
}

function User_name(user) {
    return usere.getName()；
}
```

GraphQL 服务在运行的时候，能够接收 GraphQL 查询，验证并执行。它会首先检查这个查询只使用了已定义的数据域，然后运行数据处理函数返回结果。
```json
// 查询
{
    me {
        name
    }
}
```
```json
// 结果
{
    "me": {
        "name": "Luke Skywalker"
    }
}
```


## Queries and mutations
### Fields
简化来说，GraphQL 的功能就是从对象中获取数据成员（field）。
```json
# Query JSON
{
    # 这是 selection
    hero {
        name
        # 这是一个 sub-selection
        friends {
            name
        }
    }
}
```
```json
{
    "data": {
        "hero": {
            "name": "R2-D2",
            # 注意这里返回的是列表类型
            "friends": [
                {
                    "name": "Luke Skywalker"
                },
                {
                    "name": "Hann Solo"
                }
            ]
        }
    }
}
```
可以直观的看出，查询的结果与查询本身包含相同的数据结构。

### Arguments
在 GraphQL 中，你可以方便的传递参数，包括每个数据域、每个内嵌的数据对象都可以。甚至可以在服务端实现数据转换。
```json
# 查询
{
    human(id:"1000") {
        name
        height(unit:FOOT)
    }
}
```
```json
# 结果
{
    "data": {
        "human": "Luke Skywalker",
        "height": 5.6430448
    }
}
```

### Aliases
在上边传递参数的例子中，你可以发现，同一个参数不能传递多个参数值。这就是使用 alias 的原因，他可以让你将结果的数据域重命名。在这个例子中，两个 `hero` 数据域会冲突，但是通过alias重命名，我们在同一个请求中就可以获取两份数据：
```json
# 译注：同一个query，但重命名了返回数据的名字
{
    empireHero: hero(episode: EMPIRE) {
        name
    }
    jediHero: hero(episode: JEDI) {
        name
    }
}
```
```json
{
    "data": {
        "empireHero": {
            "name": "Luke Skywalker"
        },
        "hediHero": {
            "name": "R2-D2"
        }
    }
}
```
### Fragments
GraphQL 的可重用模块称为 *fragments*。Fragment 可以让你构造一数据域的集合，然后再任何query需要的地方包含它：
```json
# 两个 query 需要返回相同的数据
{
    leftComparison: hero(episode: EMPIRE) {
        ...comparisonFields
    }
    rightComparison: hero(episode: JEDI) {
        ...comparisonFields
    }

    fragment comparisonFields on Character {
        name
        appearsIn
        friends {
            name
        }
    }
}
```
```json
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        },
        {
          "name": "C-3PO"
        },
        {
          "name": "R2-D2"
        }
      ]
    },
    "rightComparison": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```
fragment 主要用于将复杂的数据分割成小块数据，让你的工程更加易于维护。
#### Using variables inside fragments
```json
query HeroComparison($first: Int = 3) {
    leftComparison: hero(episode: EMPIRE) {
        ...comparisonFields
    }
    rightComparison: hero(episode: EMPIRE) {
        ...comparisonFields
    }

    fragment comparisonFields on Character {
        name
        friendsConnection(first:$first) {
            totalCount
            edges {
                node {
                    name
                }
            }
        }
    }
}
```
```json
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "friendsConnection": {
        "totalCount": 4,
        "edges": [
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          },
          {
            "node": {
              "name": "C-3PO"
            }
          }
        ]
      }
    },
    "rightComparison": {
      "name": "R2-D2",
      "friendsConnection": {
        "totalCount": 3,
        "edges": [
          {
            "node": {
              "name": "Luke Skywalker"
            }
          },
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          }
        ]
      }
    }
  }
}
```
## Operation name
在上边的例子中我们一直使用的都是简化的语法，省略了 `query` 关键字和 query的名字。在实际的项目中，最好加上它们，降低代码的歧义，增强可读性。
```json
# 增加query关键字和query的名字
query HeroNameAndFriends {
    hero {
        name
        friends {
            name
        }
    }
}
```
```json
# 返回值不变
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```
操作类型的关键字只有 `query`、`mutation`、`subscription`，说明你想要的操作类型。如果不使用简化的语法，操作类型关键是是必须的。

操作的名字明确表示了操作的名字，在多个操作的文档中是必须的。但是我们建议尽量的使用操作的名字，有助于源码调试和服务器端的日志。

## Variables
在上边的实例中，我们都是用了静态的 query 语句，但是在实际的项目中，query 中会使用动态的变量。但是让客户端在运行时动态拼接query语句并不是什么好办法，所以在GraphQL 中提供了传递动态变量的方法。你只要做下面的三步操作即可：
1. 将 query 中的静态值替换为 `$variableName`
2. 声明 query 可以接受 `$variableName` 这个参数
3. 在指定的变量字典中传递 variableName:value 参数

```json
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```
```json
# variables字典
{
  "episode": "JEDI"
}

```
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

### Variable definitions
变量定义就是 `$`符号后跟变量名字和类型。比如上边例子中的 `$episode: Episode`。参数类型只能是标量、枚举或输入类型。如果在变量定义后增加 `!` 表示参数是必须的。

### Default variables
变量的默认值可以使用 `=` 指定。
```json
query HeroNameAndFriends($episode: Episode = JEDI) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

## Directives
在上一节文章中我们使用 variable 避免了开发中动态拼凑 query 语句。然而在实际开发过程中我们可能还需要动态的改变 query 的结构，并通过变量来控制query结构的变化。假设以下的情况：有一个UI组件，它包含一个简介界面和一个详细页面，详细页面包含更多的信息。那么我们可以构造以下的 query：
```json
# query
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
# variables
{
  "episode": "JEDI",
  "withFriends": false
}
```
```json
# 返回的数据
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```
在上边的query中，改变 `withFriends` 的值会改变 query 返回的结果的结构。

这是 GraphQL 的一个特性，称为 *directive* 。它可以被应用于数据域和 fragment 中，它会影响服务端对query的执行。GraphQL 核心规范只包含以下两个指令，任何与规范兼容的服务器都要支持这两个指令（服务器端可以自定义增加指令）：
- `@include(if: Boolean)` 只在参数为 `true` 的时候包含这个数据域
- `@skip(if: Boolean)` 在参数为 `true` 的时候省略这个数据域

## Mutations
简单来说，query是从服务器获取数据，mutation 是向服务器发送数据。与 query 相同，mutation 也可以返回数据。这对于你获取数据更新后的状态很有用：
```json
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
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

### Multiple fields in mutations
与query一样，一个query中可以包含多个数据域，一个 mutation 中也可以包含多个数据域。但是它们有一个不同点是 query 中的每个数据域都是并行执行的，而mutation中的多个数据域是串行依次执行的。只有前一个执行完成后下一个才能执行。

## Inline fragments
如果你的query返回的是接口或 union 类型，那么需要使用 *inline fragment* 来访问底层的数据，参考下边的例子：
```json
# query
query HeroForEpisode($ep: Episode!) {
    hero(episode: $ep) {
        name
        ... on Droid {
            primaryFunction
        }
        ... on Human {
            height
        }
    }
}
# variables
{
    "ep": "HEDI"
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
在上边的例子中，`hero` 数据域返回值类型是 `Character`。根据参数的类型，它可能是 `Human` 或 `Droid` 中的一个。在老的查询中，你只能要求返回 `Character` 中存在的数据，比如 `name`。

为了请求底层具体的数据类型，你必须使用指定数据类型的 `inline fragment`  来实现。如果 `Character` 是 `Droid` 类型的，由于 `... on Droid` 的存在，会返回 `primaryFunction` 数据域；如果 `Character` 是 `Human类型`， 返回 `height` 数据域。

有自己命名的 fragment 也可以这样使用，因为fragment 永远都是类型明确的。

### Meta fields
假设某些情况下，你无法预知 GraphQL 服务器端返回的数据的类型，为了处理返回的数据，你需要在客户打断判断数据的类型。 GraphQL 允许请求 `__typename` 这个 meta field 来获取对象的类型。
```json
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Droid {
      name
    }
    ... on Starship {
      name
    }
  }
}
```
```json
{
  "data": {
    "search": [
      {
        "__typename": "Human",
        "name": "Han Solo"
      },
      {
        "__typename": "Human",
        "name": "Leia Organa"
      },
      {
        "__typename": "Starship",
        "name": "TIE Advanced x1"
      }
    ]
  }
}
```
上边的例子中， `search` 返回一个 union 类型，这个 union 可能是三种数据类型中的一种。那么客户端就无法在没有 `__typename` 的情况下区分返回的数据类型。

GraphQL 服务提供一系列的 meta fields,参考 [Introspection System](https://graphql.org/learn/introspection/)。

## GraphQL VS REST
| 项目     | GraphQL                                                                         | REST                        |
| -------- | ------------------------------------------------------------------------------- | --------------------------- |
| 参数传递 | 每个数据域、内嵌对象都可以包含自己的参数，使一个GraphQL查询可以代替多个REST调用 | 只能在URL、查询参数中传递低 |

## 参考
[HOW TO GRAPHQL](https://www.howtographql.com/)

[GraphQL](https://graphql.org/learn/)


TO BE CONTINUED ...