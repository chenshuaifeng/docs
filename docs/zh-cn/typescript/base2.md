## 显示类型（Explicit Types）
直到现在，我们还没有告诉 TypeScript，`person` 和 `date` 是什么类型，让我们编辑一下代码，告诉 TypeScript，person 是一个 string 类型，date 是一个 Date 对象。同时我们使用 date 的 toDateString() 方法。

```js
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
```

我们所做的就是给 person 和 date 添加了**类型注解（type annotations）**，描述 greet 函数可以支持传入什么样的值。你可以如此理解这个**签名 (signature)**： greet 支持传入一个 string 类型的 person 和一个 Date 类型的 date 。

添加类型注解后，TypeScript 就可以提示我们，比如说当 greet 被错误调用时：

```js
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
 
greet("Maddison", Date());
// Argument of type 'string' is not assignable to parameter of type 'Date'.
```
TypeScript 提示第二个参数有错误，这是为什么呢？

这是因为，在 JavaScript 中调用 Date() 会返回一个 string 。使用 new Date() 才会产生 Date 类型的值。

我们快速修复下这个问题：
```js
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
 
greet("Maddison", new Date());

```
记住，我们并不需要总是写类型注解，大部分时候，TypeScript 可以自动推断出类型：

```js
let msg = "hello there!";
// let msg: string
```
尽管我们并没有告诉 `TypeScript，` `msg` 是 string 类型的值，但它依然推断出了类型。这是一个特性，如果类型系统可以正确的推断出类型，最好就不要手动添加类型注解了。

## 类型抹除（Erased Types）

上一个例子里的代码，TypeScript 会编译成什么样呢？我们来看一下：

```js
function greet(person, date) {
    console.log("Hello ".concat(person, ", today is ").concat(date.toDateString(), "!"));
}
greet("Maddison", new Date());


```
注意两件事情：

- 我们的 person 和 date 参数不再有类型注解
- 使用字符串方法


让我们先看下第一点。**类型注解并不是 JavaScript 的一部分**。所以并没有任何浏览器或者运行环境可以直接运行 TypeScript 代码。**这就是为什么 TypeScript 需要一个编译器，它需要将 TypeScript 代码转换为 JavaScript 代码，然后你才可以运行它**。所以大部分 TypeScript 独有的代码会被抹除，在这个例子中，像我们的类型注解就全部被抹除了。

## 降级（Downleveling）

我们再来关注下第二点，原先的代码是：

```js
`Hello ${person}, today is ${date.toDateString()}!`;
```

被编译成了:
```js
function greet(person, date) {
    console.log("Hello ".concat(person, ", today is ").concat(date.toDateString(), "!"));
}
greet("Maddison", new Date());
```
为什么要这样做呢？

这是因为模板字符串是 ECMAScript2015（也被叫做 ECMAScript 6 ,ES2015, ES6 等）里的功能，TypeScript 可将新版本的代码编译为老版本的代码，比如 ECMAScript3 或者 ECMAScript5 。这个将高版本的 ECMAScript 语法转为低版本的过程就叫做降级（downleveling）

TypeScript 默认转换为 ES3，一个 ECMAScript 非常老的版本。我们也可以使用 target (opens new window)选项转换为比较新的一些版本，**比如执行 --target es2015 会转换为 ECMAScript 2015**, 这意味着转换后的代码可以在任何支持 ECMAScript 2015 的地方运行。

执行 tsc --target es2015 hello.ts ，让我们看下编译成 ES2015 后的代码：
```js
function greet(person, date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
greet("Maddison", new Date());

```

## 严格模式（Strictness）

不同的用户使用 TypeScript 会关注不同的事情。一些用户会寻找较为宽松的体验，既可以帮助检查他们程序中的部分代码，也可以享受 TypeScript 的工具功能。这就是 TypeScript 默认的开发体验，类型是可选的，推断会兼容大部分的类型，对有可能是 null/ undefined 值也不做强制检查。就像 tsc 在编译报错时依然会输出文件，这些默认选项并不会阻碍你的开发。如果你正在迁移 JavaScript 代码，最一开始就可以使用这种方式。

与之形成鲜明对比的是，还有很多用户希望 TypeScript 尽可能多地检查代码，这就是为什么这门语言会提供严格模式设置。但不同于切换开关的形式（要么检查要么不检查），TypeScript 提供的形式更像是一个刻度盘，你越是转动它，TypeScript 就会检查越多的内容。这需要一点额外的工作，但是是值得的，它可以带来更全面的检查和更准确的工具功能。如果可能的话，新项目应该始终开启这些严格设置。

TypeScript 有几个严格模式设置的开关。除非特殊说明，文档里的例子都是在严格模式下写的。CLI 里的 `strict` 配置项，或者 tsconfig.json"strict": true 可以同时开启，也可以分开设置。在这些设置里，你最需要了解的是 `noImplicitAny`  `strictNullChecks` 

> noImplicitAny


在某些时候，TypeScript 并不会为我们推断类型，这时候就会回退到最宽泛的类型：any 。这倒不是最糟糕的事情，毕竟回退到 any就跟我们写 JavaScript 没啥一样了。

但是，经常使用 any 有违背我们使用 TypeScript 的目的。你程序使用的类型越多，你在验证和工具上得到的帮助就会越多，这也意味着写代码的时候会遇到更少的 bug。启用 noImplicitAny配置项后，当类型被隐式推断为 any 时，会抛出一个错误。

> strictNullChecks

默认情况下，像 null 和 undefined 这样的值可以赋值给其他的类型。这可以让我们更方便的写一些代码。但是忘记处理 null 和 undefined 也导致了不少的 bug，甚至有些人会称呼它为价值百万的错误  strictNullChecks 选项会让我们更明确的处理 null 和 undefined，也会让我们免于忧虑是否忘记处理 null 和 undefined 。

2020/1