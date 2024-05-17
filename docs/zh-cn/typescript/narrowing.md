### 类型收窄（Narrowing）
试想我们有这样一个函数，函数名为 padLeft：

```js
function padLeft(padding: number | string, input: string): string {
  throw new Error("Not implemented yet!");
}
```
如果参数 padding 是一个数字，我们就在 input 前面添加同等数量的空格，而如果 padding 是一个字符串，我们就直接添加到 input 前面。

让我们实现一下这个逻辑：

```js
function padLeft(padding: number | string, input: string) {
  return new Array(padding + 1).join(" ") + input;
	// Operator '+' cannot be applied to types 'string | number' and 'number'.
}
```

如果这样写的话，编辑器里 padding + 1 这个地方就会标红，显示一个错误。

这是 TypeScript 在警告我们，如果把一个 number 类型 (即例子里的数字 1 )和一个 number | string 类型相加，也许并不会达到我们想要的结果。换句话说，我们应该先检查下 padding 是否是一个 number，或者处理下当 padding 是 string 的情况，那我们可以这样做：

```js
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return new Array(padding + 1).join(" ") + input;
  }
  return padding + input;
}

```

这个代码看上去也许没有什么有意思的地方，但实际上，TypeScript 在背后做了很多东西。

TypeScript 要学着分析这些使用了静态类型的值在运行时的具体类型。目前 TypeScript 已经实现了比如 if/else 、三元运算符、循环、真值检查等情况下的类型分析。

在我们的 if 语句中，TypeScript 会认为 typeof padding === number 是一种特殊形式的代码，我们称之为**类型保护**，TypeScript 会沿着执行时可能的路径，分析值在给定的位置上最具体的类型。

TypeScript 的类型检查器会考虑到这些类型保护和赋值语句，而这个将类型推导为更精确类型的过程，我们称之为**收窄**

> typeof 类型保护

JavaScript 本身就提供了 typeof 操作符，可以返回运行时一个值的基本类型信息，会返回如下这些特定的字符串：

"string"
"number"
"bigInt"
"boolean"
"symbol"
"undefined"
"object"
"function"

> 真值收窄

在 JavaScript 中，我们可以在条件语句中使用任何表达式，比如 && 、||、! 等，举个例子，像 if 语句就不需要条件的结果总是 boolean 类型

```js
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `There are ${numUsersOnline} online now!`;
  }
  return "Nobody's here. :(";
}
```
是因为 JavaScript 会做隐式类型转换，像 0 、NaN、""、0n、null undefined 这些值都会被转为 false，其他的值则会被转为 true。

当然你也可以使用 Boolean 函数强制转为 boolean 值，或者使用更加简短的!!：

```js
// both of these result in 'true'
Boolean("hello"); // type: boolean, value: true
!!"world"; // type: true,    value: true
```

> never 类型

进行收窄的时候，如果你把所有可能的类型都穷尽了，TypeScript 会使用一个 never 类型来表示一个不可能存在的状态。

never 类型可以赋值给任何类型，然而，没有类型可以赋值给 never （除了 never 自身）。这就意味着你可以在 switch 语句中使用 never 来做一个穷尽检查。

```js
type Shape = Circle | Square;
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```



