> 函数（More On Functions）

函数是任何应用的基础组成部分，无论它是局部函数（local functions），还是从其他模块导入的函数，亦或是类中的方法。当然，函数也是值 (values)，而且像其他值一样，TypeScript 有很多种方式用来描述，函数可以以怎样的方式被调用。让我们来学习一下如何书写描述函数的类型（types）。

## 函数类型表达式（Function Type Expressions）

最简单描述一个函数的方式是使用**函数类型表达式（function type expression）。**它的写法有点类似于箭头函数：

```js
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}
 
function printToConsole(s: string) {
  console.log(s);
}
 
greeter(printToConsole);

```
语法 (a: string) => void 表示一个函数有一个名为 a ，类型是字符串的参数，这个函数并没有返回任何值。

如果一个函数参数的类型并没有明确给出，它会被隐式设置为 any。

当然了，我们也可以使用类型别名（type alias）定义一个函数类型：

```js
type GreetFunction = (a: string) => void;
function greeter(fn: GreetFunction) {
  // ...
}
```
> 调用签名（Call Signatures）

在 JavaScript 中，函数除了可以被调用，自己也是可以有属性值的。然而上一节讲到的函数类型表达式并不能支持**声明属性**，如果我们想描述一个带有属性的函数，我们可以在一个对象类型中写一个**调用签名**（call signature）。

```js
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}

```

注意这个语法跟函数类型表达式稍有不同，在参数列表和返回的类型之间用的是 : 而不是 =>。


> 构造签名

JavaScript 函数也可以使用 new 操作符调用，当被调用的时候，TypeScript 会认为这是一个构造函数(constructors)，因为他们会产生一个新对象。你可以写一个构造签名，方法是在调用签名前面加一个 new 关键词：

```js
JavaScript 函数也可以使用 new 操作符调用，当被调用的时候，TypeScript 会认为这是一个构造函数(constructors)，因为他们会产生一个新对象。你可以写一个构造签名，方法是在调用签名前面加一个 new 关键词：
```
一些对象，比如 Date 对象，可以直接调用，也可以使用 new 操作符调用，而你可以将调用签名和构造签名合并在一起：

```js
interface CallOrConstruct {
  new (s: string): Date;
  (n?: number): number;
}

```

## 泛型函数

我们经常需要写这种函数，即函数的输出类型依赖函数的输入类型，或者两个输入的类型以某种形式相互关联。让我们考虑这样一个函数，它返回数组的第一个元素：

```js
function firstElement(arr: any[]) {
  return arr[0];
}
```
注意此时函数返回值的类型是 any，如果能返回第一个元素的具体类型就更好了。

在 TypeScript 中，泛型就是被用来描述两个值之间的对应关系。我们需要在函数签名里声明一个**类型参数**

```js
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```
通过给函数添加一个类型参数 Type，并且在两个地方使用它，我们就在函数的输入(即数组)和函数的输出(即返回值)之间创建了一个关联。现在当我们调用它，一个更具体的类型就会被判断出来：

```js
// s is of type 'string'
const s = firstElement(["a", "b", "c"]);
// n is of type 'number'
const n = firstElement([1, 2, 3]);
// u is of type undefined
const u = firstElement([]);
```
> 推断

注意在上面的例子中，我们没有明确指定 Type 的类型，类型是被 TypeScript 自动推断出来的。

我们也可以使用多个类型参数，举个例子：

```js
function map<Input, Output>(arr: Input[], func: (arg: Input) => Output): Output[] {
  return arr.map(func);
}
 
// Parameter 'n' is of type 'string'
// 'parsed' is of type 'number[]'
const parsed = map(["1", "2", "3"], (n) => parseInt(n));

```

> 约束

有的时候，我们想关联两个值，但只能操作值的一些固定字段，这种情况，我们可以使用**约束（constraint）**对类型参数进行限制。

让我们写一个函数，函数返回两个值中更长的那个。为此，我们需要保证传入的值有一个 number 类型的 length 属性。我们使用 extends 语法来约束函数参数：

```js
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
 
// longerArray is of type 'number[]'
const longerArray = longest([1, 2], [1, 2, 3]);
// longerString is of type 'alice' | 'bob'
const longerString = longest("alice", "bob");
// Error! Numbers don't have a 'length' property
const notOK = longest(10, 100);
// Argument of type 'number' is not assignable to parameter of type '{ length: number; }'.

```
TypeScript 会推断 longest 的返回类型，所以返回值的类型推断在泛型函数里也是适用的。

正是因为我们对 Type 做了 { length: number } 限制，我们才可以被允许获取 a b参数的 .length 属性。没有这个类型约束，我们甚至不能获取这些属性，因为这些值也许是其他类型，并没有 length 属性。

基于传入的参数，longerArray和 longerString 中的类型都被推断出来了。记住，所谓泛型就是用一个相同类型来关联两个或者更多的值。


> #泛型约束实战

这是一个使用泛型约束常出现的错误：

```js
function minimumLength<Type extends { length: number }>(
  obj: Type,
  minimum: number
): Type {
  if (obj.length >= minimum) {
    return obj;
  } else {
    return { length: minimum };
    // Type '{ length: number; }' is not assignable to type 'Type'.
    // '{ length: number; }' is assignable to the constraint of type 'Type', but 'Type' could be instantiated with a different subtype of constraint '{ length: number; }'.
  }
}
```

## 声明类型参数

TypeScript 通常能自动推断泛型调用中传入的类型参数，但也并不能总是推断出。举个例子，有这样一个合并两个数组的函数：
```js
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}
```
如果你像下面这样调用函数就会出现错误：

```js
const arr = combine([1, 2, 3], ["hello"]);
// Type 'string' is not assignable to type 'number'.
```
而如果你执意要这样做，你可以手动指定 Type：

```js
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

## 可选参数

JavaScript 中的函数经常会被传入非固定数量的参数，举个例子：number 的 toFixed 方法就支持传入一个可选的参数：

```js
function f(n: number) {
  console.log(n.toFixed()); // 0 arguments
  console.log(n.toFixed(3)); // 1 argument
}
```
我们可以使用 ? 表示这个参数是可选的：

```js
function f(x?: number) {
  // ...
}
f(); // OK
f(10); // OK
```

尽管这个参数被声明为 number类型，x 实际上的类型为 number | undefiend，这是因为在 JavaScript 中未指定的函数参数就会被赋值 undefined。

你当然也可以提供有一个参数默认值：
```js
function f(x = 10) {
  // ...
}
```
现在在 f 函数体内，x 的类型为 number，因为任何 undefined 参数都会被替换为 10。注意当一个参数是可选的，调用的时候还是可以传入 undefined：
```js
declare function f(x?: number): void;
// cut
// All OK
f();
f(10);
f(undefined);
```

> 回调中的可选参数

在你学习过可选参数和函数类型表达式后，你很容易在包含了回调函数的函数中：

```js
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i);
  }
}
```

## 函数重载

一些 JavaScript 函数在调用的时候可以传入不同数量和类型的参数。举个例子。你可以写一个函数，返回一个日期类型 Date，这个函数接收一个时间戳（一个参数）或者一个 月/日/年 的格式 (三个参数)。

在 TypeScript 中，我们可以通过写重载签名 (overlaod signatures) 说明一个函数的不同调用方法。 我们需要写一些函数签名 (通常两个或者更多)，然后再写函数体的内容：

```js
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);

// No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
```
这个例子中，我们写了两个函数重载，一个接受一个参数，另外一个接受三个参数。前面两个函数签名被称为**重载签名** (overload signatures)。

然后，我们写了一个兼容签名的函数实现，我们称之为**实现签名**(implementation signature) ，但这个签名不能被直接调用。尽管我们在函数声明中，在一个必须参数后，声明了两个可选参数，它依然不能被传入两个参数进行调用。

> 重载签名和实现签名

这是一个常见的困惑。大家常会这样写代码，但是又不理解为什么会报错：

```js
function fn(x: string): void;
function fn() {
  // ...
}
// Expected to be able to call with zero arguments
fn();
Expected 1 arguments, but got 0.
```

再次强调一下，写进函数体的签名是对外部来说是“不可见”的，这也就意味着外界“看不到”它的签名，自然不能按照实现签名的方式来调用。

而且实现签名必须和重载签名必须兼容（compatible），举个例子，这些函数之所以报错就是因为它们的实现签名并没有正确的和重载签名匹配。
```js
function fn(x: boolean): void;
// Argument type isn't right
function fn(x: string): void;
// This overload signature is not compatible with its implementation signature.
function fn(x: boolean) {}

```

```js
function fn(x: string): string;
// Return type isn't right
function fn(x: number): boolean;
This overload signature is not compatible with its implementation signature.
function fn(x: string | number) {
  return "oops";
}

```

## 其他类型
`void`
void 表示一个函数并不会返回任何值，当函数并没有任何返回值，或者返回不了明确的值的时候，就应该用这种类型。
```js
// The inferred return type is void
function noop() {
  return;
}

```
`unknown`
unknown 类型可以表示任何值。有点类似于 any，但是更安全，因为对 unknown 类型的值做任何事情都是不合法的：
```js
function f1(a: any) {
  a.b(); // OK
}
function f2(a: unknown) {
  a.b();
  // Object is of type 'unknown'.
}
```
`never`

```js
function fail(msg: string): never {
  throw new Error(msg);
}
```
