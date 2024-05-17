> 泛型

软件工程的一个重要部分就是构建组件，组件不仅需要有定义良好和一致的 API，也需要是可复用的（reusable）。好的组件不仅能够兼容今天的数据类型，也能适用于未来可能出现的数据类型，这在构建大型软件系统时会给你最大的灵活度。

在比如 C# 和 Java 语言中，用来创建可复用组件的工具，我们称之为泛型（generics）。利用泛型，我们可以创建一个支持众多类型的组件，这让用户可以使用自己的类型消费（consume）这些组件。

让我们开始写第一个泛型，一个恒等函数（identity function）。所谓恒等函数，就是一个返回任何传进内容的函数。你也可以把它理解为类似于 `echo `命令。

不借助泛型，我们也许需要给予恒等函数一个具体的类型：
```js
function identity(arg: number): number {
  return arg;
}
```

所以我们需要一种可以捕获参数类型的方式，然后再用它表示返回值的类型。这里我们用了一个**类型变量**（type variable），一种用在类型而非值上的特殊的变量。
```js
function identity<Type>(arg: Type): Type {
  return arg;
}
```

现在我们已经给恒等函数加上了一个类型变量 Type，这个 Type 允许我们捕获用户提供的类型，使得我们在接下来可以使用这个类型。这里，我们再次用 Type 作为返回的值的类型。在现在的写法里，我们可以清楚的知道参数和返回值的类型是同一个。

现在这个版本的恒等函数就是一个泛型，它可以支持传入多种类型。不同于使用 any，它没有丢失任何信息，就跟第一个使用 number 作为参数和返回值类型的的恒等函数一样准确。

在我们写了一个泛型恒等函数后，我们有两种方式可以调用它。第一种方式是传入所有的参数，包括类型参数：

```js
let output = identity<string>("myString"); // let output: string
```

第二种方式可能更常见一些，这里我们使用了**类型参数推断**（type argument inference），我们希望编译器能基于我们传入的参数自动推断和设置 Type 的值。

```js
let output = identity("myString"); // let output: string
```
注意这次我们并没有用 <> 明确的传入类型，当编译器看到 myString 这个值，就会自动设置 Type 为它的类型（即 string）。

类型参数推断是一个很有用的工具，它可以让我们的代码更短更易阅读。而在一些更加复杂的例子中，当编译器推断类型失败，你才需要像上一个例子中那样，明确的传入参数。

## 泛型类型变量

当你创建类似于 identity 这样的泛型函数时，你会发现，编译器会强制你在函数体内，正确的使用这些类型参数。这就意味着，你必须认真的对待这些参数，考虑到他们可能是任何一个，甚至是所有的类型（比如用了联合类型）。

让我们以 identity 函数为例：
```js
function identity<Type>(arg: Type): Type {
  return arg;
}
```
如果我们想打印 arg 参数的长度呢？我们也许会尝试这样写：
```js
function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length);
	// Property 'length' does not exist on type 'Type'.
  return arg;
}
```
如果我们这样做，编译器会报错，提示我们正在使用 arg 的 .length属性，但是我们却没有在其他地方声明 arg 有这个属性。我们前面也说了这些类型变量代表了任何甚至所有类型。所以完全有可能，调用的时候传入的是一个 number 类型，但是 number 并没有 .length 属性。

现在假设这个函数，使用的是 Type 类型的数组而不是 Type。因为我们使用的是数组，.length 属性肯定存在。我们就可以像创建其他类型的数组一样写：
```js
function loggingIdentity<Type>(arg: Type[]): Type[] {
  console.log(arg.length);
  return arg;
}
```

现在我们使用类型变量 Type，是作为我们使用的类型的一部分，而不是之前的一整个类型，这会给我们更大的自由度。

我们也可以这样写这个例子，效果是一样的：

```js
function loggingIdentity<Type>(arg: Array<Type>): Array<Type> {
  console.log(arg.length); // Array has a .length, so no more error
  return arg;
}
```

## 泛型类型

在上个章节，我们已经创建了一个泛型恒等函数，可以支持传入不同的类型。在这个章节，我们探索函数本身的类型，以及如何创建`泛型接口`。

`泛型函数`的形式就跟其他非泛型函数的一样，都需要先列一个`类型参数列表`，这有点像函数声明：

```js
function identity<Type>(arg: Type): Type {
  return arg;
}
 
let myIdentity: <Type>(arg: Type) => Type = identity;
```

泛型的`类型参数`Input可以使用不同的名字，只要数量和使用方式上一致即可：
```js
function identity<Type>(arg: Type): Type {
  return arg;
}
 
let myIdentity: <Input>(arg: Input) => Input = identity;
```
泛型接口，让我们使用上个例子中的对象字面量，然后把它的代码移动到接口里：

```js
interface GenericIdentityFn {
  <Type>(arg: Type): Type;
}
 
function identity<Type>(arg: Type): Type {
  return arg;
}
 
let myIdentity: GenericIdentityFn = identity;

```

有的时候，我们会希望将泛型参数作为整个接口的参数，这可以让我们清楚的知道传入的是什么参数 (举个例子：Dictionary<string> 而不是 Dictionary)。而且接口里其他的成员也可以看到。
```js
interface GenericIdentityFn<Type> {
  (arg: Type): Type;
}
 
function identity<Type>(arg: Type): Type {
  return arg;
}
 
let myIdentity: GenericIdentityFn<number> = identity;
```
注意在这个例子里，我们只做了少许改动。不再描述一个泛型函数，而是将一个非泛型函数签名，作为泛型类型的一部分。

现在当我们使用 GenericIdentityFn 的时候，需要明确给出参数的类型。(在这个例子中，是 number)，有效的锁定了调用签名使用的类型。

当要描述一个包含泛型的类型时，理解什么时候把类型参数放在调用签名里，什么时候把它放在接口里是很有用的。

除了泛型接口之外，我们也可以创建泛型类。注意，不可能创建泛型枚举类型和泛型命名空间。

## 泛型类

泛型类写法上类似于泛型接口。在类名后面，使用尖括号中 `<> `包裹住类型参数列表：
```js
class GenericNumber<NumType> {
  zeroValue: NumType;
  add: (x: NumType, y: NumType) => NumType;
}
 
let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};
```

在这个例子中，并没有限制你只能使用 number 类型。我们也可以使用 string 甚至更复杂的类型：
```js
let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function (x, y) {
  return x + y;
};
 
console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```
## 泛型约束

我们需要创建一个接口，用来描述约束。这里，我们创建了一个只有 .length 属性的接口，然后我们使用这个接口和 extends 关键词实现了约束：
```js
interface Lengthwise {
  length: number;
}
 
function loggingIdentity<Type extends Lengthwise>(arg: Type): Type {
  console.log(arg.length); // Now we know it has a .length property, so no more error
  return arg;
}
```
### 在泛型约束中使用类型参数

你可以声明一个类型参数，这个类型参数被其他类型参数约束
举个例子，我们希望获取一个对象给定属性名的值，为此，我们需要确保我们不会获取 obj 上不存在的属性。所以我们在两个类型之间建立一个约束：

```js
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key];
}
 
let x = { a: 1, b: 2, c: 3, d: 4 };
 
getProperty(x, "a");
getProperty(x, "m");

// Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.
```