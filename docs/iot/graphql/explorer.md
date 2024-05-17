在前端开发过程中，`GraphQL` 是一个非常常用的数据查询语言。在使用 `GraphQL` 时，可以使用 `graphiql-explorer` 包来探索 `GraphQL` API 的结构和类型。本文将介绍如何使用 npm 包 `graphiql-explorer`，详细了解其功能和用法。

## 基本介绍
`graphiql-explorer` 是 `GraphQL` API 的可视化探索工具。它被设计成一个 React 组件，可以快速集成到前端应用中。`graphiql-explorer` 通过使用 `GraphQL` Schema 的元数据信息，为 `GraphQL` API 提供自动生成的搜索功能和可视化查询构建器。用户可以使用它来浏览 API 的结构，并探索 API 的功能。

## 安装配置
首先，需要在项目中安装 ``graphiql-explorer`` 依赖包。可以使用 npm 安装方式：

```js
npm install `graphiql-explorer`
```
安装完成后，可以在项目中使用默认的 `graphiql-explorer` 实现：
```js
import React from 'react';
import ReactDOM from 'react-dom';
import GraphiQLExplorer from '`graphiql-explorer`';

ReactDOM.render(<GraphiQLExplorer />, document.getElementById('root'));
```
在代码中，可以将 `GraphiQLExplorer` 组件渲染到 HTML 元素中。默认情况下，`graphiql-explorer` 会显示一个包含 API 根节点组件的列表。每个组件都包含一个可折叠的属性列表，其中包含查询和变量参数。

探索 API 信息
如果已经定义了 `GraphQL` Schema，那么 ``graphiql-explorer`` 可以自动读取并显示所有的类型和字段。可以通过向 `GraphiQLExplorer` 组件传递一个 schema 属性来指定 Schema：
```js
import React from 'react';
import ReactDOM from 'react-dom';
import GraphiQLExplorer from '`graphiql-explorer`';
import { buildSchema } from 'graphql';

const schema = buildSchema(`
  type Query {
    books: [Book]
  }
  type Book {
    title: String
    author: String
  }
`);

ReactDOM.render(<GraphiQLExplorer schema={schema} />, document.getElementById('root'));
```
在这个示例中，我们指定了一个非常简单的 `GraphQL` Schema，其中包含一个 books 查询和一个 Book 类型。当渲染 GraphiQLExplorer 组件时，它会自动读取 Schema 并显示所有类型和字段。

用户可以通过展开每个类型和字段来查看其详细信息，包括类型、描述、字段参数和标记等。

查询构建器
`graphiql-explorer` 还提供了一个查询构建器，用户可以使用它来轻松创建和编辑 `GraphQL` 查询。可以通过调用 GraphiQLExplorer 组件上的 buildQuery 方法来打开查询构建器：
```js
import React from 'react';
import ReactDOM from 'react-dom';
import GraphiQLExplorer from '`graphiql-explorer`';
import { buildSchema } from 'graphql';

const schema = buildSchema(`
  type Query {
    books: [Book]
  }
  type Book {
    title: String
    author: String
  }
`);

function openExplorer() {
  const query = '{ books { title author } }';
  const explorer = GraphiQLExplorer.buildExplorer(schema, query, result => {
    console.log(result);
  });
  ReactDOM.render(explorer, document.getElementById('root'));
}

ReactDOM.render(<button onClick={openExplorer}>Open Explorer</button>, document.getElementById('root'));
```
在这个示例中，我们定义了一个打开查询构建器的函数 openExplorer。在该函数中，我们首先定义了一个查询，然后使用 `GraphiQLExplorer`.buildExplorer 方法来创建查询构建器。buildExplorer 方法接受三个参数：`GraphQL` Schema、查询字符串和查询完成后的回调函数。

用户可以在查询构建器中选择想要查询的字段和类型、添加查询参数和变量，然后单击“生成查询”来生成查询字符串。生成的查询字符串将通过回调函数返回。

结论
本文简明介绍了 npm 包 `graphiql-explorer` 的基本用法。使用 `graphiql-explorer`，用户可以轻松探索 `GraphQL` API 的元数据信息，并通过查询构建器轻松创建和编辑 `GraphQL` 查询。此外，`graphiql-explorer` 还提供了详细的文档和示例，用户可以借此进一步学习 `GraphQL` 和 `graphiql-explorer` 的使用。
