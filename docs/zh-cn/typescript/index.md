**Typescript是进入20年以来最火的语言**
话不多说，直接进入正题  
## 基础类型
`Javascript`的每个执行不同的操作会有不同的行为。这听起来有点抽象，所以让我举个例子，假设我们有个名为`message`的变量，试想一下我们可以做哪些操作：
```js
// Accessing the property 'toLowerCase'
// on 'message' and then calling it
message.toLowerCase();
// Calling 'message'
message();
```

第一行代码是获取属性 `toLowerCase` ，然后调用它。第二行代码则是直接调用 `message` 。

但其实我们连 `message` 的值都不知道呢，自然也不知道这段代码的执行结果。每一个操作行为都先取决于我们有什么样的值。

`message` 是可调用的吗？
`message` 有一个名为 `toLowerCase` 的属性吗？
`如果有，toLowerCase` 是可以被调用的吗？
如果这些值都可以被调用，它们会返回什么？
当我们写 `JavaScript` 的时候，这些问题的答案我们需要谨记在心，同时还要期望处理好所有的细节。

让我们假设 `message` 是这样定义的：

```js
const message = "Hello World!";
```
你完全可以猜到这段代码的结果，如果我们尝试运行 `message.toLowerCase() `，我们可以得到这段字符的小写形式。

那第二段代码呢？如果你对 JavaScript 比较熟悉，你肯定知道会报如下错误：
```js
TypeError: message is not a function

```
如果我们能避免这样的报错就好了。

当我们运行代码的时候，JavaScript 会在运行时先算出值的类型（type），然后再决定干什么。所谓值的类型，也包括了这个值有什么行为和能力。当然 TypeError 也会暗示性的告诉我们一点，比如在这个例子里，它告诉我们字符串 Hello World 不能作为函数被调用。

于一些值，比如基本值 `string` 和 `number`，我们可以使用 `typeof` 运算符确认他们的类型。但是对于其他的比如函数，就没有对应的方法可以确认他们的类型了，举个例子，思考这个函数：

```js
function fn(x) {
  return x.flip();
}
```
我们通过阅读代码可以知道，函数只有被传入一个拥有可调用的 `flip` 属性的对象，才会正常执行。但是 `JavaScript` 在代码执行时，并不会把这个信息体现出来。在 `JavaScript` 中，唯一可以知道 `fn` 在被传入特殊的值时会发生什么，就是调用它，然后看会发生什么。这种行为让你很难在代码运行前就预测代码执行结果，这也意味着当你写代码的时候，你会更难知道你的代码会发生什么。

从这个角度来看，类型就是描述什么样的值可以被传递给 `fn`，什么样的值则会导致崩溃。`JavaScript` 仅仅提供了`动态类型`（dynamic typing），这需要你先运行代码然后再看会发生什么。

替代方案就是使用`静态类型系统`（static type system），**在代码运行之前就预测需要什么样的代码**。

> 静态类型检查(static type-checking)

让我们再回想下这个将 `string` 作为函数进行调用而产生的 `TypeError` ，大部分的人并不喜欢在运行代码的时候得到报错。这些会被认为是 `bug`。当我们写新代码的时候，我们也尽力避免产生新的 bug。  

如果我们添加一点代码，保存文件，然后重新运行代码，就能立刻看到错误，我们可以很快的定位到问题，但也并不总是这样，比如如果我们没有做充分的测试，我们就遇不到可能出错的情况。或者如果我们足够幸运看到了这个错误，我们也许不得不做一个大的重构，然后添加很多不同的代码，才能找出问题所在  

理想情况下，我们应该有一个工具可以帮助我们，在代码运行之前就找到错误。这就是静态类型检查器比如 TypeScript 做的事情。**静态类型系统（Static types systems）描述了值应有的结构和行为**。一个像 TypeScript 的类型检查器会利用这个信息，并且在可能会出错的时候告诉我们：

```js
const message = "hello!";
 
message();

// This expression is not callable.
// Type 'String' has no call signatures.
```
在这个例子中，TypeScript 会在运行之前就会抛出错误信息。

## 非异常报错（Non-exception 失败）

至今为止，我们已经讨论的都是运行时的错误，所谓运行时错误，就是 JavaScript 会在运行时告诉我们它认为的一些没有意义的事情。这些事情之所以会出现，是因为 [ECMAScript](https://tc39.es/ecma262/)规范已经明确的声明了这些异常时的行为。 

举个例子，规范规定，当调用一个非可调用的东西时应该抛出一个错误。也许听起来像是理所当然的，由此你可能认为，如果获取一个对象不存在的属性也应该抛出一个错误，但是 JavaScript 并不会这样，它不报错，还返回值 undefined。

```js
const user = {
  name: "Daniel",
  age: 26,
};
user.location; // returns undefined
```
一个静态类型需要标记出哪些代码是一个错误，哪怕实际生效的 JavaScript 并不会立刻报错。在 TypeScript 中，下面的代码会产生一个 `location` 不存在的报错：
```js
const user = {
  name: "Daniel",
  age: 26,
};
 
user.location;
// Property 'location' does not exist on type '{ name: string; age: number; }'.
```
尽管有时候这意味着你需要在表达的时候上做一些取舍，但目的还是找出我们项目中一些合理的错误。TypeScript 现在已经可以捕获很多合理的错误。

举个例子，比如拼写错误：
```js
const announcement = "Hello World!";
 
// How quickly can you spot the typos?
announcement.toLocaleLowercase();
announcement.toLocalLowerCase();
 
// We probably meant to write this...
announcement.toLocaleLowerCase();
```
函数未被调用：
```js
function flipCoin() {
  // Meant to be Math.random()
  return Math.random < 0.5;
// Operator '<' cannot be applied to types '() => number' and 'number'.
}
```
基本的逻辑错误：
```js
const value = Math.random() < 0.5 ? "a" : "b";
if (value !== "a") {
  // ...
} else if (value === "b") {
  // This condition will always return 'false' since the types '"a"' and '"b"' have no overlap.
  // Oops, unreachable
}
```
> 类型工具（Types for Tooling）

TypeScript 不仅在我们犯错的时候，可以找出错误，还可以防止我们犯错。

类型检查器因为有类型信息，可以检查比如说是否正确获取了一个变量的属性。也正是因为有这个信息，它也可以在你输入的时候，列出你可能想要使用的属性。

这意味着 TypeScript 对你编写代码也很有帮助，核心的类型检查器不仅可以提供错误信息，还可以提供代码补全功能。这就是 TypeScript 在工具方面的作用。 

TypeScript 的功能很强大，除了在你输入的时候提供补全和错误信息。还可以支持“快速修复”功能，即自动的修复错误，重构成组织清晰的代码。同时也支持导航功能，比如跳转到变量定义的地方，或者找到一个给定的变量所有的引用。

所有这些功能都建立在类型检查器上，并且跨平台支持。有可能你最喜欢的编辑器 已经支持了 TypeScript

> `tsc` TypeScript 编译器（tsc，the TypeScript compiler）

至今我们只是讨论了类型检查器，但是还一直没有用过。现在让我们了解下我们的新朋友 tsc —— TypeScript 编译器。首先，我们可以通过 `npm` 安装它：


让我们创建一个空文件夹，然后写下我们第一个 TypeScript 程序: `hello.ts`：

```js
// Greets the world.
console.log("Hello world!");
```
注意这里并没有什么多余的修饰，这个 hello world 项目就跟你用 `JavaScript` 写是一样的。现在你可以运行 `tsc` 命令，执行类型检查：
```js
tsc hello.ts
```
我们就会发现，我们得到了一个新的文件。查看一下当前目录，我们会发现 hello.ts 同级目录下还有一个 hello.js，这就是 hello.ts 文件编译输出的文件， tsc 会把 ts 文件编译成一个纯 JavaScript 文件。让我们查看一下编译输出的文件：  
```js
我们就会发现，我们得到了一个新的文件。查看一下当前目录，我们会发现 hello.ts 同级目录下还有一个 hello.js，这就是 hello.ts 文件编译输出的文件， tsc 会把 ts 文件编译成一个纯 JavaScript 文件。让我们查看一下编译输出的文件：
```

在这个例子中，因为 TypeScript 并没有什么要编译处理的内容，所以看起来跟我们写的是一样的。编译器会尽可能输出干净的代码，就像是正常开发者写的那样，当然这并不是容易的事情，但 TypeScript 会坚持这样做，比如保持缩进，注意跨行代码，保留注释等。

如果我们执意要产生一个类型检查错误呢？我们可以这样写 hello.ts:

```js
// This is an industrial-grade general-purpose greeter function:
function greet(person, date) {
  console.log(`Hello ${person}, today is ${date}!`);
}
 
greet("Brendan");
```

此时我们再运行下` tsc hello.ts `。这次我们会在命令行里得到一个错误：

```js
Expected 2 arguments, but got 1.
```

> 报错时仍产出文件（Emitting with Errors）


在刚才的例子中，有一个细节你可能没有注意到，那就是如果我们打开编译输出的文件，我们会发现文件依然发生了改动。这是不是有点奇怪？tsc 明明已经报错了，为什么还要再编译文件？这就要讲到 `TypeScript` 一个核心的观点：大部分时候，你要比 TypeScript 更清楚你的代码。

举个例子，假如你正在把你的代码迁移成 `TypeScript`，这会产生很多类型检查错误，而你不得不为类型检查器处理掉所有的错误，这时候你就要想了，明明之前的代码可以正常工作，TypeScript 为什么要阻止代码正常运行呢？

所以 TypeScript 并不会阻碍你。当然了，你如果想要 TypeScript 更严厉一些，你可以使用 `noEmitOnError`编译选项，试着改下你的 hello.ts 文件，然后运行 tsc:
```js
tsc --noEmitOnError hello.ts

```
你会发现 `hello.js` 并不会得到更新。也就是说当第一次编译后生成js文件，代码改动后不会再进行编译

2020/3
