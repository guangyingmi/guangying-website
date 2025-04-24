# TypeScript

# 一、基础语法与类型系统

## 1. TypeScript 与 JavaScript / ES6 的关系

TypeScript 是 JavaScript 的超集，在原有 JS 能力之上引入了**类型系统**与**面向对象特性支持**，最显著的优势是可以在开发阶段发现类型错误。

### 实战中常见优势：
- 类型检查让 refactor 更安全可靠
- 良好的 IDE 支持（自动补全、跳转定义、错误提示）
- 更易读、易维护的大型项目开发

### 示例对比：
```ts
// TypeScript
const msg: string = "Hello";

// JavaScript
const msg = "Hello"; // 变量类型未明确
```

在开发中，我们推荐 **明确类型声明** 而非依赖类型推导，尤其是在公共方法、库函数或接口定义中。

---

## 2. 基本类型：number、string、boolean、any、void、null、undefined、never

### ✅ 常用类型定义
```ts
let age: number = 30;
let userName: string = "Alice";
let isOnline: boolean = true;
```

### ❌ 典型错误
```ts
let age: number = "30"; // 报错：不能将 string 赋值给 number
```

### any 类型：放弃类型保护，慎用！
```ts
let data: any = 123;
data = "a string"; // 合法但不推荐
```
> 建议仅在处理第三方库、动态结构数据、原型调试时使用 `any`。

### void 类型
通常用于无返回值的函数：
```ts
function logMessage(): void {
  console.log("Logged.");
}
```

### null 和 undefined
TypeScript 默认允许它们作为所有类型的子类型（除非开启严格检查）：
```ts
let a: number | undefined;
let b: string | null;
```

### never 类型：**不会发生的类型**
用于：函数抛出异常、死循环、类型穷尽检查等。
```ts
function throwError(msg: string): never {
  throw new Error(msg);
}

function infiniteLoop(): never {
  while (true) {}
}
```

### 面试延伸：如何理解 never 与 void 的区别？
> `void` 表示没有返回值，而 `never` 表示**根本不会返回**。两者不能混用。

```ts
function testVoid(): void {
  return; // ✅ 允许
}

function testNever(): never {
  return; // ❌ 报错：不能从类型 never 函数中返回
}
```

---

## 3. 类型注解与类型推导

### 明确类型注解的好处：
- 增强代码自解释能力
- 提高 IDE 支持度与团队协作一致性

```ts
// 注解方式
let username: string = "Alice";

// 推导方式
let count = 10; // 类型为 number
```

### 推荐实践：
- 在函数参数、返回值、对象结构中使用注解
- 局部变量可采用类型推导

### 面试延伸：
> Q: 类型推导有哪些潜在风险？
>
> A: 在类型复杂、上下文不明确时可能导致推导不准确，尤其是解构赋值与函数返回值时。

---

## 4. 数组与元组类型

### 数组写法有两种：
```ts
let arr1: number[] = [1, 2, 3];
let arr2: Array<string> = ["a", "b"];
```

### 元组：固定长度、明确位置类型
```ts
let tuple: [string, number] = ["age", 18];
```

### 易错点：
```ts
tuple = [18, "age"]; // ❌ 类型顺序不对
```

### 实战用法：
元组常用于函数返回多个类型值：
```ts
function useCounter(): [number, () => void] {
  return [0, () => {}];
}
```

---

## 5. 枚举 Enum

### 数值枚举
```ts
enum Direction {
  Up = 1,
  Down,
  Left,
  Right
}
```

### 字符串枚举
```ts
enum Status {
  Success = "success",
  Fail = "fail"
}
```

### 反向映射（数值枚举特性）
```ts
console.log(Direction[1]); // "Up"
```

### 面试延伸：
> Q: 枚举类型与联合字面量类型的区别？
>
> A: 枚举是运行时可访问的对象，联合字面量更轻量但无运行时表现。

---

## 6. 联合类型 & 交叉类型

### 联合类型（OR）
```ts
let value: string | number;
value = "hi";
value = 123;
```

### 交叉类型（AND）
```ts
type A = { name: string };
type B = { age: number };
type C = A & B;

const person: C = {
  name: "Tom",
  age: 25
};
```

### 使用技巧：
- 联合类型常用于参数输入
- 交叉类型用于合并多个类型结构

---

## 7. 类型别名 type & 接口 interface

### 类型别名
```ts
type ID = number | string;
type Callback = () => void;
```

### 接口
```ts
interface Person {
  name: string;
  age: number;
}
```

### 区别总结：
- `interface` 更适合描述对象结构
- `type` 更适合组合类型、联合类型

### 互补使用：
```ts
interface Animal {
  name: string;
}

type Dog = Animal & { breed: string };
```

---

## 8. 类型保护与断言

### 类型保护
使用 `typeof`、`instanceof` 或自定义函数：
```ts
function isString(val: unknown): val is string {
  return typeof val === "string";
}
```

### 类型断言（强制告诉编译器）
```ts
let input: unknown = "hello";
let strLen: number = (input as string).length;
```

### 面试延伸：
> Q: 类型断言是否一定安全？
>
> A: 不安全，仅在明确确定类型时使用，否则会隐藏 bug。

---

## 9. keyof / in / extends

### keyof
```ts
interface User {
  name: string;
  age: number;
}
type UserKey = keyof User; // "name" | "age"
```

### in 用于映射类型
```ts
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};
```

### extends 作用：
1. 限制泛型约束
2. 条件类型判断
```ts
function getValue<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

---

## 10. 可选属性、只读属性、索引签名

### 可选属性（?）
```ts
interface Profile {
  name: string;
  bio?: string;
}
```

### 只读属性（readonly）
```ts
interface Point {
  readonly x: number;
  readonly y: number;
}
```

### 索引签名（[key: string]）
```ts
interface Flexible {
  [key: string]: string | number;
}
```

### 面试延伸：
> Q: 可选属性和 undefined 有何不同？
>
> A: 可选属性可以不传，undefined 是传了但值为空，两者语义不同。

---

# 二、函数相关

## 1. 函数类型定义

### 两种常见定义方式

#### 1）长写法（完整结构体）
```ts
type AddFn = {
  (x: number, y: number): number;
};
```

#### 2）简写方式
```ts
type AddFn = (x: number, y: number) => number;
```

> 在函数作为参数或返回值传递时，简写更常见；而长写法在定义函数接口类型时更清晰。

### 使用场景示例：
```ts
function calc(fn: (a: number, b: number) => number): number {
  return fn(10, 5);
}
```

---

## 2. 可选参数、默认参数、剩余参数

### 可选参数（使用 ? 标记）
```ts
function greet(name: string, age?: number): string {
  return age ? `${name} is ${age}` : `Hello, ${name}`;
}
```

### 默认参数（在形参中赋值）
```ts
function greet(name = "Guest"): string {
  return `Welcome, ${name}`;
}
```

### 剩余参数（...rest）
```ts
function sum(...nums: number[]): number {
  return nums.reduce((acc, cur) => acc + cur, 0);
}
```

> 注意：可选参数必须放在必选参数之后；剩余参数必须在最后。

### 面试延伸：可选参数与 undefined 的区别？
```ts
function test(a?: string) {}

// 与下面这两种方式等价：
test();           // a 是 undefined
test(undefined);  // a 也是 undefined
```

---

## 3. 函数重载（Overload）

函数重载允许定义多个同名函数，但参数类型或个数不同。

### 示例：
```ts
function format(input: string): string;
function format(input: number): string;
function format(input: any): string {
  if (typeof input === 'number') return input.toFixed(2);
  return input.trim();
}
```

### 编译限制：
- 只有最后一个函数体可包含实现，前面的为签名声明
- 参数类型必须兼容所有声明

### 使用场景：
- 封装库方法（比如处理不同输入类型）
- 类型安全的工具函数

---

## 4. 泛型函数与泛型接口

### 泛型函数
```ts
function identity<T>(arg: T): T {
  return arg;
}
```

### 泛型接口
```ts
interface GenericFn<T> {
  (arg: T): T;
}

const fn: GenericFn<number> = (x) => x * 2;
```

### 多个泛型参数
```ts
function merge<T, U>(a: T, b: U): T & U {
  return { ...a, ...b };
}
```

### 实战示例：创建可配置结构体
```ts
function createOption<T>(config: T): { default: T; current: T } {
  return {
    default: config,
    current: config,
  };
}
```

---

## 5. 泛型约束（extends）

泛型默认可以是任意类型，我们可以通过 `extends` 添加限制：
```ts
function getLength<T extends { length: number }>(val: T): number {
  return val.length;
}

getLength("hello");         // ✅
getLength([1, 2, 3]);        // ✅
getLength({ length: 10 });   // ✅
// getLength(123);          // ❌ 报错：number 没有 length 属性
```

> 实战应用：确保传入对象具有特定结构（如 DOM、数组、类实例等）

---

## 6. infer 的基本使用

`infer` 是 TypeScript 条件类型中用于推导类型变量的关键字。

### 场景：从函数类型中提取返回类型
```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type R1 = ReturnType<() => number>; // number
```

### 示例：提取 Promise 的值类型
```ts
type Awaited<T> = T extends Promise<infer R> ? R : T;

type Data = Awaited<Promise<string>>; // string
```

### 应用：泛型工具类型的底层机制
许多 TS 官方类型如 `ReturnType`、`Awaited`、`Parameters` 都是基于 `infer` 构建的。

---

# 三、类与面向对象编程

TypeScript 完全支持 ES6 的类语法，并在此基础上引入了更强的类型系统、访问控制符、抽象类等特性，使其成为构建大型应用时更为稳健的选择。

---

## 1. class 基础与构造函数

TypeScript 中定义类使用 `class` 关键字，同时支持构造函数、成员方法、属性初始化：

```ts
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  sayHello(): string {
    return `Hello, my name is ${this.name}`;
  }
}

const tom = new Person("Tom", 25);
```

### 补充说明：
- 构造函数参数需显式声明类型，否则类型推导将不生效
- 可在构造函数中初始化默认值或依赖注入

---

## 2. 继承、super 与方法重写

通过 `extends` 实现类继承，子类中通过 `super` 调用父类构造函数或方法。

```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }

  move(distance: number): void {
    console.log(`${this.name} moved ${distance}m.`);
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name);
  }

  bark(): void {
    console.log("Woof!");
  }

  move(distance: number): void {
    console.log("Dog is moving...");
    super.move(distance);
  }
}
```

### 面试延伸：子类中不调用 `super` 会怎样？
> 编译错误。TS 要求在子类构造函数中必须先调用 `super()` 才能使用 `this`。

---

## 3. 访问修饰符：public / private / protected / readonly

### public
默认修饰符，所有地方都能访问。
```ts
public name: string;
```

### private
仅在类的内部可以访问，不可被继承或外部访问。
```ts
private age: number;
```

### protected
在类及其子类中可以访问，外部不可访问。
```ts
protected score: number;
```

### readonly
初始化后不可再次赋值，只读。
```ts
readonly id: number;

constructor(id: number) {
  this.id = id;
}
```

### 示例：
```ts
class BankAccount {
  private balance: number;
  readonly accountNumber: string;

  constructor(accountNumber: string, balance: number) {
    this.accountNumber = accountNumber;
    this.balance = balance;
  }

  deposit(amount: number) {
    this.balance += amount;
  }
}
```

### 面试陷阱：
> private 和 # 私有字段有何区别？
>
> `private` 是 TS 层面限制，运行时仍可访问；`#field` 是 ES 的硬私有字段，运行时不可访问。

---

## 4. 静态成员（static）与抽象类（abstract）

### 静态成员 static
静态属性或方法不依赖于实例，而是挂载在类本身上：
```ts
class Counter {
  static count = 0;

  static increment() {
    Counter.count++;
  }
}

Counter.increment();
console.log(Counter.count); // 1
```

### 抽象类 abstract
抽象类不能被实例化，只能被继承。用于定义规范性接口。
```ts
abstract class Shape {
  abstract getArea(): number;

  describe(): void {
    console.log("This is a shape.");
  }
}

class Circle extends Shape {
  radius: number;
  constructor(radius: number) {
    super();
    this.radius = radius;
  }

  getArea(): number {
    return Math.PI * this.radius ** 2;
  }
}
```

### 实战建议：
- 抽象类适用于组件基类、策略模式、模板方法等设计模式
- 静态方法常用于工具类封装或全局状态管理

---

# 四、模块与命名空间

TypeScript 支持基于 ES6 的模块系统，并提供了独有的命名空间机制来支持大型项目中代码逻辑的组织与隔离。正确理解两者的差异与适用场景，是架构设计中的重要一环。

---

## 1. ES6 模块化语法（import / export / export default）

### 基本使用
#### 导出变量、函数、类型
```ts
// math.ts
export const PI = 3.14;
export function add(a: number, b: number): number {
  return a + b;
}
export type Point = { x: number; y: number };
```

#### 导入使用
```ts
import { PI, add, Point } from "./math";
```

#### 默认导出
```ts
// config.ts
export default {
  port: 8080,
  debug: true,
};

// 使用
import config from "./config";
```

### 注意事项：
- `export default` 每个文件只能有一个
- 命名导出（非 default）可以有多个
- `import * as xxx` 可引入所有命名导出为命名空间

### 面试延伸：ES Module 与 CommonJS 的核心差异
| 特性            | ES Module         | CommonJS             |
|-----------------|-------------------|----------------------|
| 导入方式        | 静态（编译时确定）| 动态（运行时解析）    |
| this 指向       | undefined         | module.exports       |
| 兼容性           | 现代浏览器/Node14+ | Node.js 默认支持     |

---

## 2. TypeScript 命名空间（Namespace）

命名空间是 TypeScript 提供的组织代码的机制，适用于未使用模块系统的环境，如浏览器中的全局脚本开发。

```ts
namespace Utils {
  export function log(message: string) {
    console.log("[LOG]", message);
  }

  export const version = "1.0.0";
}

Utils.log("Hello");
console.log(Utils.version);
```

### 特性：
- 所有成员默认是私有的，必须 `export` 才能在外部访问
- 命名空间之间可嵌套使用，实现逻辑分区

### 命名空间 VS 模块区别：
| 对比点        | 命名空间 (namespace) | 模块 (module)      |
|---------------|------------------------|---------------------|
| 是否需要构建工具 | 否                     | 是（通常需打包）     |
| 是否有作用域     | 否，全局作用域          | 是，文件级作用域     |
| 是否推荐使用     | ❌ 旧项目保留支持       | ✅ 新项目推荐         |

> 现代项目推荐使用模块化（import/export），命名空间主要用于旧版 JS 脚本场景或特定库设计

---

## 3. 声明合并（Declaration Merging）

声明合并是 TypeScript 的独特特性，它允许多个同名接口或命名空间在编译阶段被合并。

### 接口合并
```ts
interface User {
  name: string;
}

interface User {
  age: number;
}

const user: User = {
  name: "Alice",
  age: 30
};
```

### 命名空间与函数合并
```ts
function greet(name: string): string {
  return `Hello, ${name}`;
}

namespace greet {
  export const version = "1.0";
}

console.log(greet("TypeScript"));
console.log(greet.version);
```

### 使用场景：
- 扩展第三方库类型定义（例如为函数添加静态属性）
- 某些设计模式下的语义增强（例如具名构造器）

---

# 五、装饰器（Decorators）

装饰器是 TypeScript 提供的元编程特性，允许你通过注解形式修改类的行为。它是一种函数，用于在类声明、方法、属性、参数被定义时进行拦截、扩展或替换。

要启用装饰器，必须在 `tsconfig.json` 中开启以下选项：
```json
{
  "experimentalDecorators": true
}
```

---

## 1. 类装饰器（Class Decorator）

类装饰器用于扩展或替换类定义。

### 示例：为类添加静态属性
```ts
function addVersion(constructor: Function) {
  constructor.prototype.version = "1.0.0";
}

@addVersion
class MyService {}

console.log((new MyService() as any).version); // "1.0.0"
```

### 实战应用场景：
- 为类注入默认配置（如日志标签、缓存标记）
- 集成依赖注入框架（如 NestJS）

---

## 2. 方法装饰器（Method Decorator）

用于修改类的方法行为，典型应用是拦截调用、添加日志、绑定上下文等。

### 示例：禁止方法被覆盖
```ts
function frozen(target: any, key: string, descriptor: PropertyDescriptor) {
  descriptor.writable = false;
}

class Logger {
  @frozen
  log() {
    console.log("Logging...");
  }
}

// Logger.prototype.log = () => {}; // ❌ 报错，方法不可写
```

### 应用示例：自动日志装饰器
```ts
function logCall(_: any, methodName: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${methodName} with`, args);
    return original.apply(this, args);
  };
}
```

---

## 3. 属性装饰器（Property Decorator）

用于监听属性定义，不能修改属性初始值。

```ts
function logProperty(target: any, key: string) {
  console.log(`Property defined: ${key}`);
}

class User {
  @logProperty
  name: string;
}
```

### 实战建议：
- 可结合 getter/setter 实现数据劫持
- 与 `Reflect.metadata` 结合使用以实现属性类型注解

---

## 4. 参数装饰器（Parameter Decorator）

用于标记方法参数，可用于依赖注入、参数校验、元信息记录等。

```ts
function logParam(target: Object, methodName: string, index: number) {
  console.log(`Parameter ${index} in method ${methodName}`);
}

class Greeter {
  greet(@logParam msg: string) {
    return `Hello ${msg}`;
  }
}
```

---

## 5. 装饰器工厂与执行顺序

### 装饰器工厂：用于传参
```ts
function withLabel(label: string) {
  return function (constructor: Function) {
    constructor.prototype.label = label;
  }
}

@withLabel("Service")
class Demo {}
```

### 执行顺序说明：
- 多个装饰器时，从下到上调用装饰器工厂，再从上到下调用装饰器函数本体

```ts
function f() {
  console.log("f(): evaluated");
  return function () {
    console.log("f(): called");
  };
}

function g() {
  console.log("g(): evaluated");
  return function () {
    console.log("g(): called");
  };
}

@f()
@g()
class Example {}

// 输出：
// g(): evaluated
// f(): evaluated
// f(): called
// g(): called
```

---

## 6. 实际案例示例总结

### @addAge
```ts
function addAge(age: number) {
  return function (constructor: Function) {
    constructor.prototype.age = age;
  }
}

@addAge(30)
class Person {
  name = "Tom";
}

console.log((new Person() as any).age); // 30
```

### @property 与 @method 示例
```ts
function property(target: any, key: string) {
  console.log(`Property: ${key}`);
}

function method(target: any, key: string, descriptor: PropertyDescriptor) {
  console.log(`Method: ${key}`);
}

class Example {
  @property
  message: string;

  @method
  say() {
    return "hi";
  }
}
```

---

# 六、React + TypeScript 实战

在现代 React 开发中，结合 TypeScript 能极大提升代码的类型安全、可维护性和开发体验。本章节将围绕常见场景展开深入解析。

---

## 1. React 中使用 Props 和 State 的类型定义

在函数组件和类组件中，分别通过泛型或接口方式约束 props 和 state。

### 函数组件 Props 示例：
```ts
interface ButtonProps {
  text: string;
  onClick: () => void;
}

const Button: React.FC<ButtonProps> = ({ text, onClick }) => {
  return <button onClick={onClick}>{text}</button>;
};
```

### 类组件 Props + State 示例：
```ts
interface CounterProps {
  initialValue?: number;
}
interface CounterState {
  count: number;
}

class Counter extends React.Component<CounterProps, CounterState> {
  state: CounterState = {
    count: this.props.initialValue || 0,
  };

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

---

## 2. React.FC 与 Function Component 泛型约束

React 提供的 `React.FC` 是一种类型声明工具，它自带 `children` 支持，但也存在一定争议。

### 优点：
- 自动推断 props 和 children
- 减少显式声明代码量

### 缺点：
- children 总是可选，可能不符合需求
- defaultProps 类型推导有时不准确

### 推荐方式（现代写法）：
```ts
interface Props {
  label: string;
  children?: React.ReactNode;
}

const Tag = ({ label, children }: Props) => {
  return <span>{label}: {children}</span>;
};
```

> 建议避免过度依赖 `React.FC`，更推荐手动定义 props 类型以获得更精细控制。

---

## 3. 类型安全的事件处理（如 input 事件）

React 事件是合成事件（SyntheticEvent），必须明确指定类型。

### 示例：input change 事件
```ts
function handleInput(e: React.ChangeEvent<HTMLInputElement>) {
  console.log(e.target.value);
}

<input onChange={handleInput} />
```

### 其他常见事件类型：
- 鼠标事件：`React.MouseEvent<HTMLButtonElement>`
- 表单提交：`React.FormEvent<HTMLFormElement>`
- 键盘事件：`React.KeyboardEvent<HTMLInputElement>`

> 不明确指定类型可能导致 e 无法自动提示 target 类型

---

## 4. React 组件 props 默认值处理（defaultProps）

在函数组件中，可以通过对象解构 + 默认值方式设置默认 props：
```ts
interface Props {
  title?: string;
}

const Header = ({ title = "默认标题" }: Props) => {
  return <h1>{title}</h1>;
};
```

类组件中使用 `static defaultProps`：
```ts
class Welcome extends React.Component<{ name?: string }> {
  static defaultProps = {
    name: "访客",
  };
  render() {
    return <h2>欢迎，{this.props.name}</h2>;
  }
}
```

> 在函数组件中不推荐使用 `Component.defaultProps`，解构默认值是更主流做法。

---

## 5. children 的类型定义

React 中 `children` 是非常重要的 props，定义时需要显式指定其类型。

### 几种推荐类型：
```ts
children: React.ReactNode          // 最通用类型（支持 JSX、string、number、null 等）
children: ReactElement             // 只支持单个元素
children: React.ReactNode[]       // 限定为多个节点数组
```

### 示例：
```ts
interface ContainerProps {
  children: React.ReactNode;
}

const Container = ({ children }: ContainerProps) => {
  return <section>{children}</section>;
};
```

> 对于复杂布局建议使用 `children` 明确结构，并结合组合模式提升灵活性。

---

## 6. 类组件中 this.state 和 props 的类型声明方式

```ts
interface AppProps {
  name: string;
}
interface AppState {
  count: number;
}

class App extends React.Component<AppProps, AppState> {
  constructor(props: AppProps) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  render() {
    return <div>Hello, {this.props.name}, count: {this.state.count}</div>;
  }
}
```

### 补充说明：
- 构造函数参数必须显式传入 props 类型
- 初始化 state 时类型会自动推导（也可以显式声明）

---

# 七、Vue + TypeScript 实战

在使用 TypeScript 开发 Vue 项目时，Vue 2.x 可以结合 `vue-class-component` 与 `vue-property-decorator` 使用 class 风格写法，而在 Vue 3 中，推荐使用 Composition API 搭配 `<script setup lang="ts">` 实现更灵活、简洁、类型安全的开发体验。

---

## Vue 2 实践：基于 class 装饰器写法

### 1. 使用 vue-property-decorator 实现 Vue 装饰器语法

安装依赖：
```bash
npm install vue-class-component vue-property-decorator --save
```

### 基础示例：
```ts
import { Vue, Component } from 'vue-property-decorator';

@Component
export default class HelloWorld extends Vue {
  msg: string = 'Hello TypeScript';

  mounted() {
    console.log(this.msg);
  }
}
```

---

## 2. @Component、@Prop、@Emit、@Watch 的用法

### @Component
```ts
@Component({ components: { MyChild } })
export default class Parent extends Vue {}
```

### @Prop
```ts
@Prop(String) title!: string;
@Prop({ default: 0 }) count!: number;
```

### @Emit
```ts
@Emit('submit')
submitHandler(id: number) {
  return id;
}
```

### @Watch
```ts
@Watch('count')
onCountChange(n: number, o: number) {
  console.log(`count: ${o} -> ${n}`);
}
```

---

## 3. 生命周期钩子
```ts
created() {
  console.log('Component created');
}
```

---

## 4. Props 校验配置
```ts
@Prop({
  type: String,
  required: true,
  default: '默认值',
  validator: (val: string) => val.length < 10
})
title!: string;
```

---

## Vue 3 实践：Composition API + `<script setup lang="ts">`

### 1. 定义 props 和 emit 类型
```vue
<script setup lang="ts">
defineProps<{ title: string; count?: number }>()
defineEmits<(e: 'submit', id: number) => void>()
</script>
```

### 2. 使用 reactive、ref 等组合式 API
```ts
import { ref, reactive, watch } from 'vue';

const count = ref(0);
const state = reactive({ name: 'Vue3' });
```

### 3. 事件处理 & 类型提示
```ts
function handleInput(e: Event) {
  const value = (e.target as HTMLInputElement).value;
  console.log(value);
}
```

### 4. 生命周期使用
```ts
import { onMounted, onUnmounted } from 'vue';

onMounted(() => {
  console.log('mounted');
});

onUnmounted(() => {
  console.log('unmounted');
});
```

### 5. 默认值 & 校验建议
- 使用 props 解构 + 默认值
- 使用组合式函数封装校验逻辑更灵活

---

## 小结：Vue 2 vs Vue 3 + TypeScript

| 特性         | Vue 2 + class 装饰器           | Vue 3 + Composition API           |
|--------------|--------------------------------|-----------------------------------|
| 类型推导     | 装饰器模式，借助类声明         | 原生支持 TS 推导                 |
| API 风格     | 类似 Angular、OOP 风格         | 函数式声明，更灵活               |
| 状态隔离     | 所有状态挂在 `this` 上         | 更清晰的逻辑模块化（组合式函数） |
| 默认推荐     | 依赖插件，未来逐步边缘化       | 官方主推，生态更活跃             |

Vue 3 配合 TypeScript 是官方主推的新范式，推荐在新项目中优先使用 Composition API 搭配 `<script setup>`，而 Vue 2 项目中仍可通过 class 风格维持良好的类型支持与结构清晰度。



