在 JavaScript 中，最基本的将数据成组和分发的方式就是通过对象。在 TypeScript 中，我们通过对象类型（object types）来描述对象。

对象类型可以是匿名的：
```js
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name;
}
```
也可以使用接口进行定义：

```js
interface Person {
  name: string;
  age: number;
}
 
function greet(person: Person) {
  return "Hello " + person.name;
}

```
或者通过类型别名：
```js
type Person = {
  name: string;
  age: number;
};
 
function greet(person: Person) {
  return "Hello " + person.name;
}

```

## 属性修饰符

对象类型中的每个属性可以说明它的类型、属性是否可选、属性是否只读等信息。

可选属性
我们可以在属性名后面加一个 ? 标记表示这个属性是可选的：

```js
interface PaintOptions {
  shape: Shape;
  xPos?: number;
  yPos?: number;
}
 
function paintShape(opts: PaintOptions) {
  // ...
}
 
const shape = getShape();
paintShape({ shape });
paintShape({ shape, xPos: 100 });
paintShape({ shape, yPos: 100 });
paintShape({ shape, xPos: 100, yPos: 100 });

```


在这个例子中，xPos 和 yPos 就是可选属性。因为他们是可选的，所以上面所有的调用方式都是合法的。

我们也可以尝试读取这些属性，但如果我们是在 strictNullChecks 模式下，TypeScript 会提示我们，属性值可能是 undefined。

```js
function paintShape(opts: PaintOptions) {
  let xPos = opts.xPos;              
  // (property) PaintOptions.xPos?: number | undefined
  let yPos = opts.yPos;
  // (property) PaintOptions.yPos?: number | undefined
}

```

```js
function paintShape({ shape, xPos = 0, yPos = 0 }: PaintOptions) {
  console.log("x coordinate at", xPos); // (parameter) xPos: number
  console.log("y coordinate at", yPos); // (parameter) yPos: number
  // ...
}
```
这里我们使用了解构语法以及为 xPos 和 yPos 提供了默认值。现在 xPos 和 yPos 的值在 paintShape 函数内部一定存在，但对于 paintShape 的调用者来说，却是可选的。

### readonly 属性

在 TypeScript 中，属性可以被标记为 readonly，这不会改变任何运行时的行为，但在类型检查的时候，一个标记为 readonly的属性是不能被写入的

```js
interface SomeType {
  readonly prop: string;
}
 
function doSomething(obj: SomeType) {
  // We can read from 'obj.prop'.
  console.log(`prop has the value '${obj.prop}'.`);
 
  // But we can't re-assign it.
  obj.prop = "hello";
  // Cannot assign to 'prop' because it is a read-only property.
}

```

### 索引签名

有的时候，你不能提前知道一个类型里的所有属性的名字，但是你知道这些值的特征。
这种情况，你就可以用一个索引签名 (index signature) 来描述可能的值的类型，举个例子：

```js
interface StringArray {
  [index: number]: string;
}
 
const myArray: StringArray = getStringArray();
const secondItem = myArray[1]; // const secondItem: string
```

这样，我们就有了一个具有索引签名的接口 StringArray，**这个索引签名表示当一个 StringArray 类型的值使用 number 类型的值进行索引的时候，会返回一个 string类型的值**。

一个索引签名的属性类型必须是 string 或者是 number。

虽然 TypeScript 可以同时支持 string 和 number 类型，但数字索引的返回类型一定要是字符索引返回类型的子类型。这是因为当使用一个数字进行索引的时候，JavaScript 实际上把它转成了一个字符串。这就意味着使用数字 100 进行索引跟使用字符串 100 索引，是一样的。

```js
interface Animal {
  name: string;
}
 
interface Dog extends Animal {
  breed: string;
}
 
// Error: indexing with a numeric string might get you a completely separate type of Animal!
interface NotOkay {
  [x: number]: Animal;
  // 'number' index type 'Animal' is not assignable to 'string' index type 'Dog'.
  [x: string]: Dog;
}

```
尽管字符串索引用来描述字典模式（dictionary pattern）非常的有效，但也会强制要求所有的属性要匹配索引签名的返回类型。这是因为一个声明类似于 obj.property 的字符串索引，跟 obj["property"]是一样的。在下面的例子中，name 的类型并不匹配字符串索引的类型，所以类型检查器会给出报错：
然而，如果一个索引签名是属性类型的联合，那各种类型的属性就可以接受了：
```js
interface NumberDictionary {
  [index: string]: number;
 
  length: number; // ok
  name: string;
	// Property 'name' of type 'string' is not assignable to 'string' index type 'number'.
}

```

```js
interface NumberOrStringDictionary {
  [index: string]: number | string;
  length: number; // ok, length is a number
  name: string; // ok, name is a string
}

```

### 属性继承

有时我们需要一个比其他类型更具体的类型。举个例子，假设我们有一个 BasicAddress 类型用来描述在美国邮寄信件和包裹的所需字段。

```js
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}

```
这在一些情况下已经满足了，但同一个地址的建筑往往还有不同的单元号，我们可以再写一个 AddressWithUnit：
```js
interface AddressWithUnit {
  name?: string;
  unit: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}

```
这样写固然可以，但为了加一个字段，就是要完全的拷贝一遍。

我们可以改成继承 BasicAddress的方式来实现：
```js
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}
 
interface AddressWithUnit extends BasicAddress {
  unit: string;
}

```
对接口使用 extends关键字允许我们有效的从其他声明过的类型中拷贝成员，并且随意添加新成员。

接口也可以继承多个类型：
```js
interface Colorful {
  color: string;
}
 
interface Circle {
  radius: number;
}
 
interface ColorfulCircle extends Colorful, Circle {}
 
const cc: ColorfulCircle = {
  color: "red",
  radius: 42,
};
```

### 交叉类型

TypeScript 也提供了名为交叉类型（Intersection types）的方法，用于合并已经存在的对象类型。

交叉类型的定义需要用到` & `操作符：

```js
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
 
type ColorfulCircle = Colorful & Circle;
```

这里，我们连结 Colorful 和 Circle 产生了一个新的类型，新类型拥有 Colorful 和 Circle 的所有成员。
```js
function draw(circle: Colorful & Circle) {
  console.log(`Color was ${circle.color}`);
  console.log(`Radius was ${circle.radius}`);
}
 
// okay
draw({ color: "blue", radius: 42 })
```

## 接口继承与交叉类型

这两种方式在合并类型上看起来很相似，但实际上还是有很大的不同。最原则性的不同就是在于冲突怎么处理，这也是你决定选择那种方式的主要原因。

```js
interface Colorful {
  color: string;
}

interface ColorfulSub extends Colorful {
  color: number
}

// Interface 'ColorfulSub' incorrectly extends interface 'Colorful'.
// Types of property 'color' are incompatible.
// Type 'number' is not assignable to type 'string'.

```
使用继承的方式，如果重写类型会导致编译错误，但交叉类型不会：
```js
interface Colorful {
  color: string;
}

type ColorfulSub = Colorful & {
  color: number
}

```

## 泛型对象类型
让我们写这样一个 Box 类型，可以包含任何值：
```js
interface Box {
  contents: any;
}

```
现在 contents 属性的类型为 any，可以用，但容易导致翻车。

我们也可以代替使用 unknown，但这也意味着，如果我们已经知道了 contents 的类型，我们需要做一些预防检查，或者用一个容易错误的类型断言。

```js
interface Box {
  contents: unknown;
}
 
let x: Box = {
  contents: "hello world",
};
 
// we could check 'x.contents'
if (typeof x.contents === "string") {
  console.log(x.contents.toLowerCase());
}
 
// or we could use a type assertion
console.log((x.contents as string).toLowerCase());

```

我们可以创建一个泛型 Box ，它声明了一个类型参数 (type parameter)：
```js
interface Box<Type> {
  contents: Type;
}
let box: Box<string>;

```

不过现在的 Box 是可重复使用的，如果我们需要一个新的类型，我们完全不需要再重新声明一个类型。

```js
interface Box<Type> {
  contents: Type;
}
 
interface Apple {
  // ....
}
 
// Same as '{ contents: Apple }'.
type AppleBox = Box<Apple>;

```


