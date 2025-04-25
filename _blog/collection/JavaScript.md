# JavaScript

## 变量与作用域

# Q1: JavaScript 有哪些数据类型？它们在存储上的区别是什么？
A: **JavaScript 数据类型**可分为两大类：**基本类型**（也称原始类型）和**引用类型**。ES6 前共有5种基本类型：`Undefined`、`Null`、`Boolean`、`Number`、`String`，ES6 新增第6种基本类型：`Symbol`。基本类型的值是**不可变**的，直接按值存储在**栈内存**中；引用类型（如 `Object`、`Array`、`Function` 等对象）存储的是对象引用，保存在**堆内存**，变量保存的是指向对象在堆中位置的引用。

- **基本类型**：存储简单的数据段，其值直接存储在栈上。由于栈内存空间小且大小固定，基本类型适合存放简单、大小固定的数据，例如布尔值、数字、字符串等。复制基本类型的变量，就是复制其值的副本，不会相互影响。
- **引用类型**：存储复杂的数据结构（对象、数组、函数等）。实际的数据保存在堆内存，变量保存的是指向堆内存中该数据的**引用地址**。复制引用类型变量，复制的是引用地址，多个变量可能指向同一对象，因此改变其中一个变量引用对象的内容，其他变量也会受影响。引用类型值可以有不固定大小，堆内存可动态分配更大空间。

**存储区别**：基本类型值保存在栈中，按值访问；引用类型的对象存在堆中，栈中变量存储的是指向对象的引用。由于这个区别，在比较两个变量是否相等时，基本类型比较的是值本身（`5 === 5` 为真）；引用类型比较的是引用是否指向同一对象（即使两个对象内容相同，引用不同也不相等）。

额外注意：`Undefined` 和 `Null` 都表示“无值”或“空”，但含义不同：`undefined` 表示变量已声明但未赋值，`null`表示一个空对象指针。二者在==比较时被认为相等（`undefined == null` 为 `true`），但在 === 比较时不相等（类型不同）。此外，`typeof null` 返回 `"object"`，这是 JavaScript 早期历史遗留的一个 Bug，需要注意。一般认为 `null` 应用于主动赋值表示“空”对象引用，而不要对未赋值变量显式赋 `undefined`。

# Q2: 说说你了解的 JavaScript 数据结构有哪些？
A: JavaScript 中常用的数据结构有：

- **对象（Object）**：键值对的集合，可用来表示字典、映射等。ES6 提供了 `Map` 和 `WeakMap` 作为更特殊的键值存储结构：`Map`允许键为任意类型且保留插入顺序，`WeakMap`的键必须是对象且为弱引用（不影响垃圾回收）。
- **数组（Array）**：有序列表，可以包含任意类型的元素。数组在 JS 中本质也是对象（索引被当作键），但引擎对连续整数索引的数组进行了优化。ES6 引入了 `Set` 和 `WeakSet` 用于存放唯一值的集合：`Set`保证元素不重复且保留插入顺序，`WeakSet`中的元素必须是对象且弱引用。
- **栈（Stack）** 和 **队列（Queue）**：虽然没有对应的原生类型，但可以使用数组的方法实现栈（后进先出，如使用 `push`/`pop`）和队列（先进先出，如使用 `push`/`shift`）。
- **链表、树、图** 等复杂数据结构需要通过对象和引用手动构建（例如用对象的 `next` 指针构造链表）。
- **TypedArray（类型化数组）**：ES6 引入，用于处理二进制数据的数组，如 `Uint8Array`、`Float32Array` 等，存放特定类型的数值，常用于 WebGL 等场景。

总之，JS 提供了基础的对象和数组来构建各种数据结构，并在 ES6+ 中增加了 `Map/Set` 等结构以方便地表示集合和映射关系。

# Q3: 解释 JavaScript 中的作用域和作用域链是什么？
A: **作用域（Scope）**指程序中定义变量的区域，决定了变量的**可见性**和生命周期。JavaScript 使用词法作用域（静态作用域），即作用域在代码书写时就确定，与函数调用位置无关。主要有三种作用域：

- **全局作用域**：在任何函数体之外定义的变量拥有全局作用域，在整个脚本执行期间存在，并可在任何地方访问。在浏览器中，全局作用域的顶层对象是 `window`，通过 `window.变量名` 可以访问全局变量。
- **函数作用域**：在函数内使用 `var` 声明的变量，其作用域局限于该函数内部（包括嵌套的子作用域），函数执行完毕后其内部用 `var` 声明的局部变量会被销毁（除非形成了闭包）。ES5 只有函数作用域，没有块级作用域。
- **块级作用域**：ES6 引入，用 `{ }` 包裹的一块代码区域。使用 `let` 或 `const` 声明的变量具有块级作用域，只在所在的块（以及其子块）内有效，块结束即销毁。典型块级作用域包括 `if/else`、`for/while` 等代码块和单独用花括号 `{ }` 包裹的代码段。

**作用域链（Scope Chain）**是由多个作用域按层次链接形成的结构。当代码在某个作用域中需要访问一个变量时，JavaScript 引擎会**沿着作用域链逐级查找**变量声明：先在当前作用域查找，如果未找到，就向上一层父作用域继续查找，如此反复，直到全局作用域。如果仍未找到，则变量被判定为未定义 (`ReferenceError`)。作用域链的顶端是当前执行上下文的本地作用域，末端是全局作用域。例如：

```js
var globalVar = 1;
function outer() {
  var outerVar = 2;
  function inner() {
    var innerVar = 3;
    console.log(innerVar);   // 找到 innerVar 于当前作用域
    console.log(outerVar);   // 当前作用域无 outerVar，沿作用域链到 outer 函数作用域找到
    console.log(globalVar);  // 沿链到全局作用域找到 globalVar
    console.log(nonExist);   // 所有作用域均无此变量，抛出 ReferenceError
  }
  inner();
}
outer();
```

上例中，在 `inner` 函数内访问变量时，JS 引擎按照作用域链顺序：先找 `innerVar`（在 inner 函数作用域找到），再找 `outerVar`（在 inner 没找到，则去上层 outer 函数作用域找到），`globalVar` 则在再上层的全局作用域找到。如果某层作用域有同名变量，会**遮蔽（Shadow）**上层同名变量。例如在 `inner` 中如果也声明了 `var globalVar`，那么 `inner` 内访问 `globalVar` 将使用自身的变量，不会再访问全局的同名变量。这体现了作用域链的“就近访问”原则。

# Q4: 说明 var 与 let/const 声明变量在作用域、提升等方面的差异？
A: **`var`、`let` 和 `const`** 是 JavaScript 中声明变量的三种方式，它们在作用域规则和行为上有明显区别：

- **作用域不同**：`var` 声明的变量存在函数作用域或全局作用域，没有块级作用域的概念；`let` 和 `const` 声明的变量具有块级作用域，只在所在的 `{ }` 代码块内部有效。
- **变量提升**：使用 `var` 声明的变量会发生**提升（Hoisting）**，即变量声明被提升到所在作用域的顶端。在代码执行前，JS 引擎会将所有 `var` 声明的变量名预先登记为 `undefined`。这意味着可以在声明之前就访问 `var` 变量（值为 `undefined`）。而 `let` 和 `const` **不存在提升**——实际上，`let/const` 声明也被处理了，但处于**暂时性死区（TDZ, Temporal Dead Zone）**，在声明之前访问会抛错。在变量实际声明之前，`let/const` 变量不可访问，也不能使用 `typeof` 检测（访问就报 `ReferenceError`）。
- **重复声明**：在同一作用域中，`var` 允许重复声明同名变量（后声明的会覆盖前面的）；`let` 和 `const` 不允许重复声明同名变量（否则报错）。
- **初始赋值要求**：`var` 和 `let` 声明后变量的初始值可以为 `undefined`（可稍后赋值）。`const` 声明的是**常量**，声明时**必须立即初始化**，且其引用不能再被重新赋值。同一块作用域内 `const` 值在后续不能改变。不过如果 `const` 指向的是对象，仍可以修改对象的属性（因为常量保存的是引用地址，对象内容可变）。
- **全局对象属性**：在全局作用域中使用 `var` 声明的变量会成为全局对象（浏览器中是 `window`）的属性，例如 `var x = 1;` 会让 `window.x === 1`。而用 `let` 和 `const` 声明的全局变量不会作为全局对象属性存在。

**示例**：
```js
console.log(a); // undefined （变量提升，虽未赋值但已声明）
var a = 10;

if(true){
  console.log(b); // ReferenceError （暂时性死区，不能在声明前访问）
  let b = 20;
}
```
上例中，`a` 用 `var` 声明，访问它时虽然代码顺序在声明之前，但不会报错而是得到 `undefined`，因为 `a` 的声明被提升了。而 `b` 用 `let` 声明，在声明之前访问会直接报错。**总结**：`var` 适用于需要在整个函数范围使用并可能多次声明的变量，但容易产生变量提升带来的逻辑混乱；`let` 用于一般可变变量声明，更安全且有块级作用域；`const` 用于常量或不需要重新赋值的变量，能明确保证不会被修改。

# Q5: JavaScript 中的“闭包”是什么？它有什么作用？
A: **闭包（Closure）**是指**某个函数能够访问其词法作用域中变量的现象**，即使函数在其定义的作用域之外执行。这通常发生在**内部函数**在外部函数返回后依然被保存并调用的情况下。简单来说，闭包由“函数+函数能够访问的外部变量”组合而成。

闭包产生的典型场景是一个内部函数引用了其外层作用域的变量，并在外层函数返回后，该内部函数被外部引用，例如通过返回值或事件回调等方式，使得内部函数的生命周期延长。此时内部函数依然**保有对外层作用域变量的引用**，这些变量不会随外层函数退出而销毁，形成闭包。

**作用**：
1. **延长变量生命周期**：按正常作用域规则，函数执行完毕后其局部变量会被回收销毁。但闭包可以使外层函数中的局部变量因为被内部函数引用而长期保存在内存中。例如，可以用闭包**模拟私有变量**：外层函数创建一些局部变量并返回内部函数，内部函数可以读写这些变量，而外部无法直接访问它们，从而起到保护作用。
2. **创建函数工厂或保存执行环境**：闭包可以记住创建时的环境，例如根据外层函数的参数配置返回不同功能的内部函数。常用于工厂函数或回调函数中，封装计算细节。例如：
   ```js
   function makeAdder(x) {
     return function(y) {
       return x + y;   // 内部函数引用了外部x，形成闭包
     };
   }
   const add5 = makeAdder(5);
   console.log(add5(3)); // 输出8，内部函数记住了x=5
   ```
   上面 `makeAdder` 返回的函数始终能访问创建时的 `x` 值，即使每次调用时 `makeAdder` 早已执行完毕，这就是闭包的威力。
3. **在异步执行中保存状态**：例如在事件处理、定时器、Promise 回调中，闭包可以让回调函数记住其定义时的环境，从而访问当时的局部变量值。

**闭包的示例**：
```js
function counterFactory() {
  let count = 0; // 外层函数的局部变量
  return function() {
    // 内层函数是闭包，能够访问外层的 count
    count++;
    console.log("当前计数为:", count);
  };
}
const counter = counterFactory();
counter(); // 输出: 当前计数为: 1
counter(); // 输出: 当前计数为: 2 （count 保持在闭包中）
```
在这个例子中，`counterFactory` 返回了内部函数 `increment`（匿名），并被赋给 `counter`。每次调用 `counter()` 都能访问并修改外层作用域的 `count` 变量。因为 `counter` 对内部函数的引用存在，`counterFactory` 的执行上下文并未完全销毁，`count` 始终保存在内存中，这就是闭包的效果。**注意**：由于闭包会使作用域中的变量长期驻留内存，可能导致**内存泄漏**，要注意适当释放不再需要的闭包引用。

闭包是 JS 强大的特性，但使用时应注意避免滥用导致的性能问题。理解闭包的关键在于理解作用域链：闭包使得即使外层作用域已结束，变量依然通过被引用而存在。

# Q6: 在 JavaScript 中，函数声明和函数表达式有何区别？它们的提升行为一样吗？
A: **函数声明（Function Declaration）**和**函数表达式（Function Expression）**是定义函数的两种方式。主要区别包括：

- **语法形式**：函数声明采用形如 `function 函数名(形参){...}` 的语法，在独立的语句中定义；函数表达式则是将一个匿名函数（或有名函数）赋值给变量，如 `var foo = function(形参){...}` 或使用箭头函数赋值，如 `let foo = (形参) => {...}`。
- **提升行为**：函数声明会被提升。在代码执行前，JS 引擎会将函数声明提升至所在作用域顶部，并提前赋值为函数体。这意味着可以在函数声明出现之前就直接调用该函数（所谓“提前调用”）。而函数表达式只会提升变量声明部分，但**不会提升赋值**，因此在赋值之前调用会导致错误。例如：
  ```js
  console.log(sum(2,3));        // 可以正常调用，输出5
  function sum(a,b){ return a+b; }

  console.log(multiply(2,3));   // 报错：multiply is not a function
  var multiply = function(a,b){ return a*b; };
  ```
  上述 `sum` 使用函数声明，调用可以在定义之前。而 `multiply` 是函数表达式，变量 `multiply` 提升了但为 `undefined`，调用时不是函数导致错误。
- **是否必须命名**：函数声明必须有函数名；函数表达式可以是匿名的，也可以赋值一个具名函数用于在函数内部递归调用。常见地，我们使用匿名函数表达式赋给变量。
- **执行上下文**：在严格模式下，函数声明不能在块级作用域中（如 if 块）按以前浏览器的行为提升到外层作用域；ES6 块级作用域中声明的函数在块外不可见。

**简单总结**：函数声明有提升机制，可以在定义前调用；函数表达式遵循变量提升规则（声明提升但赋值不提升），必须先赋值再调用。建议在代码风格上**避免函数声明与变量声明混用**（例如不要在 if 中有条件函数声明），必要时可用函数表达式代替，以确保行为一致。

# Q7: 什么是立即执行函数 (IIFE)？它有什么用途？
A: **立即执行函数**（IIFE, Immediately Invoked Function Expression）是指**定义后马上调用**的函数表达式。典型语法是在函数声明外加括号使其成为表达式，并紧跟一对括号执行它，例如：
```js
(function(){
   // ...代码...
})();
// 或者使用箭头函数 IIFE
(() => { 
   // ...代码...
})();
```
这两种写法都会立即执行函数内部代码。

**用途和好处**：
1. **创建独立的作用域，避免变量污染全局**：IIFE 内部使用 `var` 声明的变量只存在于函数作用域，不会污染全局命名空间。这在没有块级作用域的 ES5时代尤为重要，常用于封装模块或库。例如，许多早期JS库都会用IIFE包裹整个代码，以避免内部实现细节泄露到全局。
2. **封装私有变量和函数**：IIFE 可以返回一些接口（对象或函数）供外部使用，而将内部实现细节（变量、辅助函数）隐藏在闭包中，实现类似模块模式。例如：
   ```js
   var CounterModule = (function(){
     let count = 0;
     return {
       increment: function(){ count++; },
       getCount: function(){ return count; }
     };
   })();
   CounterModule.increment();
   console.log(CounterModule.getCount()); // 1
   ```
   上面通过 IIFE 创建了一个模块 `CounterModule`，使用闭包隐藏了内部变量 `count`，只暴露需要的接口函数。
3. **执行一次性的初始化代码**：如果需要在脚本加载时执行一些操作，同时不留下额外的函数或变量，IIFE 是很好的选择。例如运行一些配置或初始化逻辑，然后不会再用到其中的变量，就可以用 IIFE 封装执行。

**注意**：在 ES6+ 中，块级作用域（`let/const`）已经能够很好地限制变量范围，但 IIFE 仍然常用于**异步立即执行**（比如定义并立即调用一个 async 函数）或**模块封装**（如果不使用 ES6 import/export）。立即执行函数体现了一个重要的技巧：**用函数创建作用域**，结合闭包可以实现许多强大的模式。

## 类型判断与转换

# Q8: `==` 和 `===` 有何区别？在什么情况下分别使用它们？
A: **`==`（宽松相等）**和 **`===`（严格相等）**都是比较运算符。区别在于：`==` 在比较时会进行**类型转换**，然后再比较值；而 `===` 比较时要求**类型必须相同**且值相等才返回 true，不会做隐式类型转换。

具体区别：
- `===` 严格比较：类型不同则直接返回 false；类型相同再比较值本身。比如 `5 === "5"` 为 false，因为类型不同；`5 === 5` 为 true。
- `==` 宽松比较：会在比较前尝试将两侧转换为相同类型，然后再比较。转换规则相当复杂，但常见情况包括：字符串和数字比较时，将字符串转换为数字再比；布尔和非布尔比较时，将布尔转为数字（true->1, false->0）；`null` 和 `undefined` 在宽松比较中被认为相等（`null == undefined` 为 true）。例如：`5 == "5"` 为 true（字符串 "5" 转成数字 5），`true == 1` 为 true。

**容易混淆的情况**：
- `0 == false` 为 true（false 转换为 0）；但 `0 === false` 为 false（类型不同）。
- 空字符串和0：`"" == 0` 为 true（"" 转换为数字0）；但 `"    " == 0` 也为 true（字符串会被 `.trim()` 再转数字）。
- `null` 和 `undefined`: `null == undefined` 为 true，但除此之外 `null` 与任何其他值比较（包括 0、"" 等）都为 false；`undefined` 和任何其他值（包括 0、false）比较也为 false。
- 特殊的 `NaN`：`NaN` 不与任何值相等，包括自身，用 `==` 或 `===` 比较 `NaN == NaN` 都是 false。如需判断 NaN，需要使用 `Number.isNaN()` 或 `isNaN()`。

**使用建议**：
在绝大多数情况下，**应使用 `===` 做比较**，因为它避免了隐式类型转换的不确定性，比较结果更符合直觉。只有在明确需要允许类型转换的场景下才使用 `==`。例如，有时我们想判断一个变量是否为 null 或 undefined，可以用 `if (x == null)` 来同时匹配两种情况（因为 null 和 undefined 宽松相等）。但这也可能掩盖潜在的逻辑错误。总之，除非有充分理由使用 `==`，默认用严格相等 `===` 更安全。

# Q9: `typeof` 与 `instanceof` 有何区别？它们分别适用于什么场景？
A: **`typeof`** 和 **`instanceof`** 都是JavaScript中用于类型检查的运算符，但功能和适用场景不同：

- **`typeof` 操作符**：返回一个表示操作数数据类型的字符串。它可区分基本类型：
  - `typeof undefined` 返回 `"undefined"`
  - `typeof true` 返回 `"boolean"`
  - `typeof 123` 返回 `"number"`
  - `typeof "abc"` 返回 `"string"`
  - `typeof Symbol()` 返回 `"symbol"`
  - 以及引用类型：
  - `typeof function(){} ` 返回 `"function"`（函数被单独归为 function 类型）
  - `typeof {}` 返回 `"object"`
  
  注意：`typeof null` 返回 `"object"`（这是语言的一个历史遗留 Bug）。`typeof` 对于数组、对象、`null` 都返回 `"object"`，无法细分具体对象类型；对自定义类实例也返回 `"object"`。`typeof` 常用于判断基本类型（尤其是未声明变量或 undefined）和函数。例如：
  ```js
  if(typeof someVar === "undefined"){
    // 检查变量是否未定义
  }
  if(typeof callback === "function"){
    callback();
  }
  ```
  这种用法可以避免直接访问未声明变量导致的 ReferenceError（因为 `typeof` 未声明变量也不会报错）。
  
- **`instanceof` 操作符**：用于判断一个对象是否为某个**构造函数的实例**，其语法是 `对象 instanceof 构造函数`，根据原型链检查返回 true 或 false。例如：
  ```js
  let arr = [1,2,3];
  console.log(arr instanceof Array);    // true
  console.log(arr instanceof Object);   // true（Array 原型链上有 Object）
  ```
  `instanceof` 向上追溯对象的原型链，如果对象的原型链中出现了构造函数的 `prototype`，则返回 true。因此它主要用于判断**复杂引用类型**，比如数组、特定类的实例等。
  
  **注意**：`instanceof` 只能用于对象与构造函数之间的判断，基本类型不适用（如 `5 instanceof Number` 返回 false，因为5不是Number对象）。而且在不同 iframe 或窗口之间，由于不同全局环境有各自的构造函数，`instanceof` 判断可能失效（比如从另一个 frame 来的数组，用本窗口的 Array 构造函数判断会返回 false）。

**总结**：
- 用 `typeof` 判断**基本数据类型**最方便（undefined、boolean、number、string、symbol、function）。对于 `null` 值判断可以直接 `x === null`，因为 `typeof null` 的结果 `"object"` 具有迷惑性。
- 用 `instanceof` 判断**某对象属于哪种类**或内建类型（Array、Date等）。比如 `obj instanceof Array` 来确定是否为数组，比 `typeof` 更准确（因为 `typeof` 数组是 object）。
- 对于纯对象的判断，可以用 `instanceof Object`（不过几乎所有非 null 对象都满足）。如果需要判断对象字面量，可以使用 `obj.constructor === Object` 等方式。
- 还有一种常用方法判断数组或对象类型是 `Object.prototype.toString.call(value)`，它会返回 `"[object Type]"` 格式的字符串，比如 `"[object Array]"`，也可用来区分内建对象类型。

# Q10: 如何准确判断一个变量是数组类型？
A: 虽然 `typeof` 对于数组返回 `"object"`，不能直接区分，但有以下几种方法准确判断数组：

1. **`Array.isArray(value)`**：ES5 提供的原生方法，专门用于判断一个值是否为数组。若是数组返回 true，否则返回 false。例如：
   ```js
   Array.isArray([1,2,3]); // true
   Array.isArray({});      // false
   ```
   这是**最推荐**的方法，简单可靠，并且能跨 iframe 判断数组。

2. **`value instanceof Array`**：利用 `instanceof` 检查对象是否是 Array 构造函数的实例。这在同一全局环境下有效：
   ```js
   [] instanceof Array; // true
   ```
   但要注意如果数组是从其他 frame 或者 iframe 中来的（不同 JavaScript 执行环境），`instanceof Array` 可能返回 false，因为此时构造函数不同。所以在跨窗口环境下不可靠。

3. **`Object.prototype.toString.call(value)`**：用 `Object.prototype.toString` 可以区分类别：
   ```js
   Object.prototype.toString.call([1,2,3]); // "[object Array]"
   Object.prototype.toString.call({});      // "[object Object]"
   ```
   通过判断结果字符串是否为 `"[object Array]"` 来判定。这个方法也能正确识别跨环境的数组，因为它检查的是内部 `[[Class]]` 标签。

综上，**优先使用 `Array.isArray`** 来判断数组，因为其语义明确且跨环境正确。如果需要兼容非常旧的环境没有此方法，可以使用 `instanceof` （不跨域情况下）或者 `toString` 方法作为替代。

# Q11: `null` 和 `undefined` 有什么区别？如何判断变量是 null 还是 undefined？
A: 两者都表示“没有有意义的值”，但有所区别：

- **`undefined`** 表示变量**未初始化**或者不存在的属性。变量声明了但未赋值时默认值是 `undefined`，访问对象不存在的属性或调用函数未提供的参数也是 `undefined`。可以用 `typeof` 检查：`typeof someVar === "undefined"` 可以判断变量是否为 undefined（包括尚未声明的变量，因为 typeof 未声明变量不会报错，而直接比较会 ReferenceError）。需要注意的是，不要用 `undefined` 作为变量名，也尽量不要直接赋值为 `undefined`，而应使用 `null` 来清空变量值。
- **`null`** 是一个表示**空对象引用**的特殊值，一般用于表示“此处应有对象，但目前为空”。例如期望返回对象未取到时，可以返回 null；DOM 操作没找到元素时返回 null 等。`null`需要开发者主动赋值，例如初始化一个变量为 null 表示将来会赋予对象值。可以直接通过 `x === null` 判断是否为 null。

**区别**：
1. 类型：`typeof undefined` 是 `"undefined"`；`typeof null` 是 `"object"`（这是一个 Bug，但一直保留至今）。所以用 typeof 无法区分 null 和 object，需要用严格等于判断 null。
2. 语义：undefined 更偏向“缺值”或“未定义”；null 更偏向“空值”。对象属性不存在时通常返回 undefined；而函数期望返回对象却没有结果时多返回 null。
3. 转换：在宽松相等下，`null == undefined` 为 true，它们被认为相等；但严格相等下 `null !== undefined`。在数值上下文中，`Number(null)` 为 0，而 `Number(undefined)` 为 NaN。

**判断**：
- 判断是否为 undefined：推荐使用 `typeof x === "undefined"`。也可以直接 `x === undefined` 前提是变量已声明。但注意在局部作用域中，可能有人用 `undefined` 当变量名覆盖了全局 undefined，因此 typeof 更稳妥。ES5 之后全局 undefined 不可写，可以放心使用严格等于 undefined。
- 判断是否为 null：直接 `x === null` 即可。
- 判断两者之一：可以用 `x == null`（宽松相等）同时判断 null 或 undefined，因为只有这两者互相宽松相等。这是少数推荐使用 `==` 的场景。

# Q12: JavaScript 中有哪些方法可以将其他类型转换为数字？又有哪些把值转换为字符串？
A: **类型转换**在 JavaScript 中非常常见。将值转换为**数字**主要有：

- 使用 **Number(...) 函数**：可将任意类型转换为数字。对于字符串，会尝试解析数字字面量（忽略前后空白）；对于布尔值，true->1, false->0；`null->0`，`undefined->NaN`。例如：`Number("123")` 得到 123，`Number("123px")` 得到 NaN（无法完整解析为数）。
- **一元加号运算符（+）**：对非数字值应用 `+`，相当于调用 `Number()` 进行转换。例如：`+"3.14"` 得到 3.14，`+true` 得到 1。常用来快速把字符串转数字。但对于对象会先调用 `valueOf` 或 `toString`。
- **parseInt() 和 parseFloat()**：专门解析字符串中的数字，逐字符解析到不能解析为止。不要求整个字符串完全是数字。`parseInt("123px")` 可返回 123，`parseFloat("3.14em")` 返回 3.14。这两个函数会忽略字符串开头的空白，`parseInt` 可以解析不同进制（通过第二个参数指定基数，如 `parseInt("FF", 16)` -> 255）。注意 `parseInt` 在遇到非数字字符后停下，不同于 Number 会返回 NaN。
- **位运算符**：例如按位或0（`x | 0`）可以将 `x` 转为32位整数（小数部分截掉）。`~~x`（双按位非）也能达到类似效果。这些利用底层二进制转换，速度快但可读性差，且超过32位范围会溢出，不常用于一般场景。

将值转换为**字符串**主要有：
- **String(value) 函数**：返回参数的字符串表示。例如 `String(123)` -> `"123"`，`String(true)` -> `"true"`。对 `null` 和 `undefined` 会返回 `"null"` 和 `"undefined"`.
- **`+ ""` 拼接空字符串**：任何值和字符串相加都会触发**字符串拼接**，这会将值转换为字符串。例如：`123 + ""` 得到 `"123"`。这是一个简便技巧，相当于调用了 `ToString` 操作。
- **toString() 方法**：大多数对象和原始值（除了 null 和 undefined）都有 `toString` 方法，可显式调用。如 `(123).toString()` 得到 `"123"`，`true.toString()` -> `"true"`。对数组、对象则返回默认字符串形式（数组是元素拼接，对象默认返回 `"[object Object]"` 除非自定义了 `toString`）。
- **模板字符串**：使用模板字面量 ``${value}`` 也会将插入的值转换为字符串。这是一种现代且可读性好的方式。例如 `` `${value}` `` 产生与 `String(value)` 相同的结果。

**建议**：在将字符串转数字时，如果要求严格匹配整个字符串是数值格式，用 `Number()`（或一元+）比较合理，它对于不纯粹的数字字符串返回 NaN。而 `parseInt`/`parseFloat` 更宽松，在处理带单位或部分数值的字符串时有用。将数字转字符串则随处可见，例如 console.log 或字符串拼接中 JS 会自动转换，一般直接用模板字符串或空串拼接就好。

# Q13: `isNaN()` 和 `Number.isNaN()` 有什么区别？
A: `NaN`（Not-a-Number）是一个特殊的数值，表示无效的数字运算结果，比如 `0/0` 或 `Number("abc")`。判断一个值是否是 NaN 可以使用以下两种方法：

- **全局函数 `isNaN(value)`**：它会先尝试将参数转换为数字，然后判断转换结果是否为 NaN。因为会先转换，这导致一些非数字值也可能被判断为 NaN：
  ```js
  isNaN("foo");   // true，因为 "foo" 转换为数字是 NaN
  isNaN(undefined); // true，因为 undefined 转数字是 NaN
  isNaN({});     // true，对象转数字结果为 NaN
  ```
  这种行为有时不是我们想要的，因为我们通常只想判断值本身是不是 NaN，而不是“能否转换成有效数字”。

- **`Number.isNaN(value)`**（ES6）**：它不会对值做类型转换，只有在值**类型是 Number 且值为 NaN**时返回 true，其他情况下返回 false。例如：
  ```js
  Number.isNaN("foo"); // false，"foo"不是数字，直接返回false
  Number.isNaN(NaN);   // true
  Number.isNaN(undefined); // false，不做转换，undefined不是 Number 类型
  ```
  因此 `Number.isNaN` 更严格，也更准确地判断一个值是否**真的就是** NaN。

**总结**：推荐使用 `Number.isNaN()` 来判断一个值是否为 NaN，因为它避免了意料之外的类型转换。全局 `isNaN` 在早期用于判断任意值是否“不是合法数字”，但在 ES6 提供更严谨的方法后，现在更多使用 `Number.isNaN`。另外判断 NaN 也可以用值不等于自身这个特性：只有 NaN 满足 `value !== value`（因为 NaN 是唯一一个不等于自身的值）。例如：
```js
function isActuallyNaN(x) {
  return x !== x;
}
```
这种技巧也可以判断 NaN，但可读性不如 `Number.isNaN` 明确。

## 原型与继承

# Q14: 什么是 JavaScript 的原型（prototype）和原型链？它有什么特点？
A: 在 JavaScript 中，每个对象都有一个**原型对象**（prototype）。当我们访问对象的某个属性或方法时，若该对象自身没有定义，JS 引擎会沿着其原型链向上查找，即访问对象的原型对象，再看原型对象的原型，如此层层上溯，直到找到该属性或到达原型链尽头（原型链最终的终点是 `Object.prototype` 的原型，它为 `null`）。这个由对象及其原型构成的链状关系被称为**原型链**。

**原型**：
- 对象的原型可通过内部属性 `__proto__`（在规范中称为 [[Prototype]]）访问，在一些运行环境也提供这个属性（尽管不是正式标准的一部分，但已被广泛实现）。标准中获取原型可以使用 `Object.getPrototypeOf(obj)`。
- 函数作为构造函数有一个 `prototype` 属性（注意和对象的 `__proto__` 区别）。当用 `new` 调用函数构造对象时，新对象的原型即被设置为该构造函数的 `prototype` 对象。例如：
  ```js
  function Person(name){ this.name = name; }
  Person.prototype.sayHi = function(){ console.log("Hi, I'm " + this.name); };
  let alice = new Person("Alice");
  alice.sayHi(); // 输出 "Hi, I'm Alice"
  ```
  在这个例子中，`alice` 对象自身没有 `sayHi` 属性，但通过其原型链（`alice.__proto__ === Person.prototype`）找到了 `sayHi` 方法。
- JavaScript 的继承是基于原型链实现的，不像一些面向对象语言通过类继承。**所有对象最终继承自 `Object` 的原型**（除了 `Object.prototype` 本身），因此像 `toString`、`hasOwnProperty` 这类方法在任意对象上都能调用，因为它们其实是来自 `Object.prototype`。

**原型链**的特点：
- 原型链是一种**链式查找机制**。当访问 `obj.prop` 时，JS 引擎先在 obj 自身找 prop 属性，找不到则查找 `obj.__proto__`（即 obj 的原型）是否有该属性，如果还没有则继续查找原型的原型，直到 `Object.prototype`，再没有则返回 `undefined`。这就是原型链的属性解析过程。
- **继承性**：原型链使得对象可以“继承”其原型上的属性和方法。一个对象的原型可以有自己的原型，这样就形成一条链，使得下层对象可以复用上层原型定义的功能。例如上例中 `alice` 继承了 `Person.prototype` 的方法，而 `Person.prototype` 默认继承自 `Object.prototype`，因此 `alice.toString()` 其实来自 `Object.prototype.toString`。
- **共享性**：同一构造函数创建的实例对象共享其原型上的属性。当修改原型对象上的属性或方法时，所有实例通过原型链访问时都会反映出修改。但如果在实例上创建同名属性，会遮蔽原型上的属性（实例自己的属性优先级更高）。
- **动态性**：原型链在运行时是动态查找的。如果在原型对象上添加新方法，即使对象已经创建，也可以通过原型链访问到新增的方法。反之，删除或修改原型属性也会影响所有实例的访问结果。
- **原型链的尽头**：`Object.prototype.__proto__` 是 `null`，表示原型链的终点，没有继续。对任何对象执行 `Object.getPrototypeOf(Object.getPrototypeOf(...(Object.getPrototypeOf(obj))...))` 直到返回 null 为止，就遍历完了原型链。

**简而言之**，原型链是 JavaScript 实现继承的基础机制，它使对象之间形成了**链式的继承关系**，允许下层对象自动拥有上层对象定义的属性/方法。这种继承是基于引用的，如果要判断某个属性是对象自身还是来自原型，可以使用 `obj.hasOwnProperty(prop)` 方法。正确理解原型和原型链，对于掌握 JS 面向对象编程非常重要。

# Q15: 如何实现 JavaScript 对象的继承？有哪些继承方式？
A: 在 ES6 引入 `class` 之前，JavaScript 主要通过原型链来实现继承。常见的继承方式包括：

1. **原型链继承**：将子构造函数的原型指向父构造函数的实例。这使子类型的原型上拥有父类型实例的属性和方法，从而实现继承。例如：
   ```js
   function Parent(name) { this.name = name; this.colors = ["red","blue"]; }
   Parent.prototype.getName = function(){ return this.name; };

   function Child(name, age) {
     Parent.call(this, name); // 借用构造函数，传参
     this.age = age;
   }
   Child.prototype = new Parent(); // 核心：Child原型指向Parent实例
   Child.prototype.constructor = Child; // 修正构造函数指向

   let kid = new Child("Tom", 5);
   console.log(kid.getName()); // "Tom"，继承了Parent.prototype.getName
   ```
   在这个例子中，通过 `Child.prototype = new Parent()` 建立了 Child 原型到 Parent 实例的链路。优点是简单，可以继承父类原型上的方法。缺点是：①调用 `new Parent()` 会调用一次父构造函数，父构造函数中如果有引用类型属性，将作为 Child.prototype 的属性被所有 Child 实例共享，这可能不是所需的（如上例中所有 Child 实例共享 `colors` 数组，修改一个会影响其他）；②无法向 Parent 传递参数（以上用了组合继承来 call Parent）。

2. **借用构造函数（经典继承）**：在子构造函数内部调用父构造函数，使用 `Parent.call(this, ...args)`，这样父构造函数中定义的属性都会添加到子实例上。优点：可以传参给父构造函数，每个实例都有自己的副本，不共享引用属性。缺点：不能继承父类原型上的方法，只能继承父构造里的属性，需要在子构造函数中再定义同名方法或配合原型链继承使用。
   ```js
   function Child(name, age) {
     Parent.call(this, name); // 继承父构造的属性
     this.age = age;
   }
   ```

3. **组合继承**（原型链 + 借用构造函数）：这是最常用的传统继承方式，结合了上两者的优点。子构造函数通过 `Parent.call(this)` 继承父类属性，再通过 `Child.prototype = Object.create(Parent.prototype)` 或 `new Parent()` 继承父类原型方法。这样每个实例有自己的父属性副本，又共享父类原型的方法，实现了功能和效率的平衡。上例其实就是组合继承的实现（借用构造函数 + 设置原型）。**注意**：组合继承中 `Child.prototype = new Parent()` 会调用两次父构造（一次在设置原型时，一次在子构造 call 时），存在效率问题。

4. **原型式继承**：不使用构造函数，直接以某个对象为原型创建新对象。ES5 提供了 `Object.create(proto)` 方法，可以基于传入的原型对象创建一个新对象。比如：
   ```js
   let parentObj = { name: "p", colors: ["red"] };
   let childObj = Object.create(parentObj);
   childObj.name = "c";
   ```
   这样 `childObj` 的原型就是 `parentObj`，childObj 可以访问 parentObj 上的属性。原型式继承本质就是**浅拷贝原型对象**。缺点是和直接用原型链类似，共享引用类型属性，且无法传参。

5. **寄生式继承**：在原型式继承的基础上，创建一个辅助函数，在其中增强新对象，添加额外方法，然后返回对象。比如：
   ```js
   function createAnother(original) {
     let clone = Object.create(original);
     clone.sayHi = function(){ console.log("Hi"); };
     return clone;
   }
   ```
   这种模式主要用于在不改变原有对象的情况下，动态地为其创建的副本添加功能。

6. **寄生组合式继承**：这是对组合继承的优化，被认为是较好的继承模式。核心思想是：使用 `Object.create` 来继承父类原型，而不调用父构造函数，从而避免组合继承的父构造函数被调用两次。
   ```js
   function inheritPrototype(SubType, SuperType) {
     SubType.prototype = Object.create(SuperType.prototype);
     SubType.prototype.constructor = SubType;
   }
   function Child(name, age) {
     Parent.call(this, name);
     this.age = age;
   }
   inheritPrototype(Child, Parent);
   ```
   这样，父构造函数只在 `Parent.call` 执行一次，`Child.prototype` 的原型指向了 `Parent.prototype` 的一个副本，效率较高。这种方式是 ES5 中模拟类式继承的最佳实践。

7. **ES6 Class 继承**：ES6 引入了 `class` 语法和 `extends`关键字来更方便地实现继承，本质上仍是基于原型的。使用 `class Child extends Parent { ... }` 可以建立子类原型指向父类原型的继承关系，子类构造函数中必须调用 `super(...args)` 调用父类构造函数。ES6 类语法更接近传统面向对象语言，看起来像按顺序执行的，但实际转译后就是以前的寄生组合继承方式。

**总结**：JavaScript 的继承可以通过以上方式实现，但归根结底都是**利用原型链**。常用的是**组合继承**或**寄生组合继承**，在 ES6 时代，则直接使用 `class` 关键字实现继承。这些继承方式各有细节差异，但关键是要理解每种方式下属性和方法的共享与复制情况、构造函数调用次数等，从而根据场景选择最合适的继承实现。

# Q16: ES6 的 `class` 是怎么实现继承的？与传统原型继承相比有什么不同？
A: ES6 `class` 引入了一种更接近传统面向对象语法的定义类和继承的方式，但是**本质上仍然使用原型链和构造函数**来实现继承。`class` 更像是语法糖。关键要点：

- 定义一个类：
  ```js
  class Parent {
    constructor(name) {
      this.name = name;
    }
    sayHello() {
      console.log("Hello, I'm " + this.name);
    }
  }
  class Child extends Parent {
    constructor(name, age) {
      super(name);  // 调用父类构造函数，必须在使用 this 之前
      this.age = age;
    }
    sayAge() {
      console.log("I'm " + this.age + " years old");
    }
  }
  ```
  这里定义了 `Parent` 类和 `Child` 子类，通过 `extends` 关键字建立继承关系，并在子类构造函数中调用 `super(...)` 调用父类的构造函数。这其实在内部做了两件事：
  1. 设置 `Child.prototype.__proto__` 指向 `Parent.prototype`，以便子类实例能够访问父类原型的方法（这个由 `extends Parent` 完成）。
  2. 调用 `Parent` 构造函数初始化继承的属性（由 `super(name)` 完成，相当于 `Parent.call(this, name)`）。

- `class` 的继承与传统寄生组合式继承几乎一样：在 ES6 之前我们手工做的是：
  ```js
  Child.prototype = Object.create(Parent.prototype);
  Child.prototype.constructor = Child;
  Child.__proto__ = Parent; // 为了继承静态属性
  ```
  ES6 自动帮我们完成了这些。特别是 `Child.__proto__ = Parent` 这个静态属性的继承也是 class 做的（class 的静态方法也能被继承）。

- **区别**：
  1. **语法简洁**：使用 `class` 和 `extends` 使继承关系清晰可见，省去了大量显式设置原型的代码，更易读易写。
  2. **必须调用 super**：在子类构造函数中，如果定义了 constructor，就**必须**先调用 `super()`，否则无法使用 `this`。这一点与传统构造函数不同（传统方式下可以选择是否调用父构造函数，但不调用可能导致未继承父属性）。ES6 强制要求，以确保父类初始化逻辑执行。
  3. **`class` 定义不会提升**：与函数声明不同，`class` 声明不会变量提升，这意味着必须先定义类再使用它。
  4. **`class` 内部默认为严格模式**：所有在 class 内部定义的代码自动采用严格模式，这跟普通函数需要 `'use strict'` 明确开启不同。
  5. **原型方法的枚举性**：在 `class` 内定义的方法是不可枚举的（`enumerable: false`），这和用构造函数的 `prototype` 上添加方法有所不同（默认可枚举）。不可枚举更符合经典 OOP 对类方法的期望，也避免了 for...in 枚举时遍历到原型方法。
  6. **实质**：`class` 目前并没有引入新的对象模型，其继承仍然通过 `Child.prototype.__proto__` 指向 `Parent.prototype` 实现，实例 `instanceof Child` 和 `instanceof Parent` 依然通过原型链判断。`class` 只是让继承关系的建立更直观。

**概括**：ES6 `class/extends` 与传统原型继承**没有本质区别**，只是提供了一层语法糖，更符合面向对象直觉，使代码更简洁。同时在一些行为上做了规范（比如构造必须 super、默认严格模式等）。因此理解 class 继承背后的原型运作对于真正掌握 JS 继承仍是必要的。可以认为**ES6 类是构造函数的另一种写法**，大部分情况下结果是等价的。

## this 绑定与执行上下文

# Q17: 在 JavaScript 中，`this` 关键字的指向取决于什么？请举例说明不同情况下 `this` 的值。
A: **`this`** 是 JavaScript 函数运行时自动生成的一个绑定，指向调用该函数的对象（执行上下文）。`this` 的指向规则取决于函数调用的**方式**，主要有以下几种情况：

1. **作为对象方法调用**：如果函数被当做对象的属性方法调用，那么 `this` 指向该对象。
   ```js
   const obj = {
     x: 42,
     getX: function() {
       console.log(this.x);
     }
   };
   obj.getX(); // 输出42，此时this指向obj
   ```
   在 `obj.getX()` 调用中，getX 前有调用者 `obj`，因此 getX 内的 `this` 指向 `obj` 本身。

2. **普通函数调用**：没有指定调用对象时，非严格模式下 `this` 默认指向全局对象（浏览器中是 `window`；严格模式下为 `undefined`）。
   ```js
   function foo() {
     console.log(this);
   }
   foo(); // 非严格模式下输出 window（global），严格模式下输出 undefined
   ```
   由于没有调用者，默认 this 为全局。在严格模式 `'use strict'` 下为了安全，this 保持 undefined。

3. **构造函数调用**：当使用 `new` 调用函数（构造函数）时，会创建一个新对象并将该函数的 `this` 绑定到这个新对象上。
   ```js
   function Person(name) {
     this.name = name;
   }
   const p = new Person("Alice");
   console.log(p.name); // "Alice"
   ```
   调用 `new Person("Alice")` 时，Person 内部的 `this` 指向新创建的 `p` 对象。如果构造函数没有显式返回对象，则返回 this（即新对象）。

4. **`call` / `apply` / `bind` 显式绑定**：可以通过函数的 `call`、`apply` 方法来指定调用时 `this` 的值，或用 `bind` 返回一个绑定了 this 的新函数。
   ```js
   function greet() { console.log("Hi, " + this.name); }
   const user = { name: "Bob" };
   greet.call(user);  // 输出 "Hi, Bob"，this 被 call 显式设置为 user
   ```
   - `func.call(context, arg1, arg2, ...)` 会用 `context` 作为 this 来调用 `func`，并传入后续参数。
   - `func.apply(context, [args])` 类似，但以数组形式传参。
   - `func.bind(context)` 则返回一个新的函数，调用时 this 恒为 context（可用于回调中保留 this）。
   显式绑定可以让我们灵活地改变函数执行时的上下文对象。

5. **箭头函数**：箭头函数没有自己独立的 `this`，它的 `this`取决于其定义时所处的上下文，**继承外层（词法作用域）`this`**。也就是说，箭头函数的 this 与其外层最近的常规函数的 this 相同。
   ```js
   const obj = {
     name: "Carol",
     regularFunc: function() {
       const arrowFunc = () => { console.log(this.name); };
       arrowFunc();
     }
   };
   obj.regularFunc(); // 输出 "Carol"，arrowFunc内的this继承自regularFunc的this（即obj）
   ```
   在上例中，`arrowFunc` 内没有自己的 this，它捕获 `regularFunc` 内的 this，而 `regularFunc` 是作为 obj 方法调用的，所以 this 是 obj。因此箭头函数打印出了 "Carol"。箭头函数非常适合在需要将回调中的 this 固定为外部对象的场景（例如 DOM 事件内使用箭头函数，则 this 不是事件元素而是外层作用域）。

**注意特殊情况**：
- 如果用对象的方法赋给一个普通变量然后调用，该函数的 this 不会自动指向原对象，而会成为普通调用：
  ```js
  const obj = { name: "Dio", foo() { console.log(this.name); } };
  const bar = obj.foo;
  bar(); // 非严格模式下输出 undefined 或在浏览器中输出空字符串，因为 this 是 window 对象，window.name 可能为空
  ```
  这里 `bar()` 不带调用者，所以 this 不是 obj 而是默认全局。
- 事件处理函数中，普通函数的 this 指向触发事件的元素（例如 DOM element），但如果用了箭头函数则不会。
- `bind` 返回的绑定函数可以再次被 new 调用，这时 new 的优先级高于 bind，this 会是新实例（但这个函数可能无效，需要注意）。
- 关于 call/apply：`null` 或 `undefined` 作为 call/apply 的第一个参数时，函数内 this 会指向默认全局（非严格）或 undefined（严格模式）。如果传入的是原始值如数字、字符串，this 会被装箱成对应的对象类型（如 Number 对象）。

**总结**：`this` 的指向完全取决于函数**调用方式**：有对象调用就指向那个对象，没有则全局/undefined，new 则新实例，显式绑定则按我们指定的，箭头函数则继承外围作用域。理解和掌握 this，有助于避免在不同上下文中调用函数时出现意外的 this 值。

# Q18: 请解释 call、apply 和 bind 方法的作用，它们之间有什么区别？
A: `call`、`apply` 和 `bind` 都是**函数的方法**，它们可以改变函数执行时的 `this` 指向，也就是**显式绑定**函数的 `this`。区别在于调用时机和参数传递方式：

- **`func.call(thisArg, arg1, arg2, ...)`**：立即调用函数 `func`，并将 `thisArg` 设置为函数内部的 `this`。后续的参数 `arg1, arg2, ...` 是按顺序传递给函数的普通参数。例：
  ```js
  function greet(greeting) {
    console.log(greeting + ", " + this.name);
  }
  const user = { name: "Alice" };
  greet.call(user, "Hello"); // 输出 "Hello, Alice"
  ```
  在这里，`greet` 函数通过 call 立即被执行，`this` 指向 `user` 对象，参数 `"Hello"` 作为 greeting 传入。

- **`func.apply(thisArg, [argsArray])`**：和 call 类似，区别是第二个参数传入一个数组或类数组，将该数组中的元素作为参数依次传给函数。例：
  ```js
  function sum(a, b, c) {
    return a + b + c;
  }
  console.log( sum.apply(null, [1, 2, 3]) ); // 输出 6
  ```
  这里 `thisArg` 为 `null`（表示函数内部不使用 this），通过 apply 传递了数组 `[1,2,3]` 作为参数，相当于执行 `sum(1,2,3)`。**常见用途**：使用 `Math.max/min` 找数组最大最小值：`Math.max.apply(null, [arr])` 传入数组。

- **`func.bind(thisArg, arg1, arg2, ...)`**：与 call/apply 不同，bind 并不会立即调用函数，而是**返回一个新的函数**，这个新函数永久地绑定了指定的 thisArg，并预设了一些参数（可选）。调用这个新函数时，会以绑定的 this 和参数加上新调用时传入的参数执行原函数。例：
  ```js
  const module = {
    x: 42,
    getX: function() { return this.x; }
  };
  const getX = module.getX;
  console.log( getX() ); // undefined，因为此时this为全局

  const boundGetX = module.getX.bind(module);
  console.log( boundGetX() ); // 42，bind绑定了module作为this
  ```
  `bind` 非常有用，例如在事件监听或定时器中，传入回调时保持 this 指向：
  ```js
  element.addEventListener('click', obj.handleClick.bind(obj));
  ```
  这样无论事件回调怎样调用，内部 this 都是 `obj`。**Note**: 若在 `bind` 时传了参数，如 `func.bind(obj, arg1)`, 则调用绑定后函数时会首先用 `arg1`，再接收后续参数。

**区别总结**：
- **调用时机**：`call` 和 `apply` 是**立即调用**函数，`bind` 则是**返回绑定后的函数**需要稍后手动调用。
- **参数形式**：`call` 以**参数列表**形式传参，`apply` 以**单一数组**形式传参。两者在 JavaScript 现代环境下效率差异不大，选择通常取决于哪种方式获取参数更方便。如果参数原本在数组中，用 apply 直接传较方便；否则 call 可能更直观。
- **返回值**：`call` 和 `apply` 返回函数执行结果，`bind` 返回改变了 this 绑定的新函数。
- **用处**：call/apply常用于**借用别的对象的方法**（如数组方法应用在类数组对象上：`Array.prototype.slice.call(arguments)`），或在**一次性**地指定 this 后马上调用函数。apply尤其在函数需要可变参数时有用。bind 则常用于**函数柯里化（预先填充部分参数）**和**保持函数的 this 上下文**（如事件回调、定时调用等），可以多次调用返回的新函数。

# Q19: 看以下代码，分别输出什么？为什么？
```js
var name = "lucy";
var obj = {
    name: "martin",
    say: function() {
        console.log(this.name);
    }
};
obj.say();               // (1)
setTimeout(obj.say, 0);  // (2)
setTimeout(obj.say.bind(obj), 0); // (3)
```
A: 这段代码考察了 `this` 在不同调用情况下的指向：

- (1) `obj.say();` 作为对象方法直接调用，`this` 指向调用者 `obj`，因此输出 `obj.name` 的值 `"martin"`。

- (2) `setTimeout(obj.say, 0);` 这里传给 `setTimeout` 的回调是 `obj.say` 函数的引用，没有括号因此**没有立即执行**，而是由 `setTimeout`在全局环境调用它。这样调用时，`this` 并不会自动是 obj，因为调用的时候相当于普通函数调用。实际上 `setTimeout` 会执行类似 `callback()`，此时没有明确调用者，在非严格模式下 `this` 将指向全局对象（浏览器中为 `window`）。因此输出相当于 `window.name`。代码中全局定义了 `var name = "lucy"`，这相当于给 `window.name` 赋值 `"lucy"`（在浏览器，全局 var 声明成为 window 对象的属性）。所以 (2) 会输出 `"lucy"`。如果在严格模式下，全局this为 undefined，那么 this.name 会报错。不过默认情况下是非严格模式且 var 定义挂在 window 下。

- (3) `setTimeout(obj.say.bind(obj), 0);` 在这里，我们用 `bind` 创建了一个绑定了 `obj` 的新函数并传给 setTimeout。这样回调执行时内部的 `this` 已被固定为 `obj`。因此 (3) 的输出与 (1) 相同，都是 `"martin"`。

**总结**：
1号输出 "martin"，因为以 obj 调用 say 方法，this->obj；  
2号输出 "lucy"，因为函数作为普通函数由 setTimeout调用，this->window，全局 name 是 "lucy"；  
3号输出 "martin"，因为通过 bind 将 this 锁定为 obj 后延迟调用，this->obj。

这也说明了：直接把方法引用传递给异步调用会丢失原对象的上下文，需要用 bind 保持或用箭头函数等方式，否则默认 this 会指向全局或 undefined。

# Q20: 箭头函数的 `this` 与普通函数的 `this` 有何不同？箭头函数可以用作构造函数吗？
A: **箭头函数（Arrow Function）**在 ES6 中引入，具有与普通函数不同的 `this` 行为和特性：

- **`this` 绑定规则**：箭头函数**不会创建自身的 `this`**。它的 `this` 是在定义时**静态地继承自外层作用域**（即最近一层普通函数的 this）。换言之，箭头函数内的 this 与其词法作用域中 this 相同。相比之下，普通函数的 this 是在调用时动态决定的。
  - 这意味着箭头函数的 this 一旦确定就不会变化，后续无论如何调用、也不能用 call/apply 改变箭头函数内部的 this。`Function.prototype.call`/`apply` 对箭头函数只会传参，不影响 this。
  - 例如：
    ```js
    const obj = { 
      name: "Zoe",
      regular: function() { console.log("regular:", this.name); },
      arrow: () => { console.log("arrow:", this.name); }
    };
    obj.regular(); // 输出 "regular: Zoe" （this指向obj）
    obj.arrow();   // 输出 "arrow: undefined" （箭头函数定义时this为外层作用域（全局），全局没有name或为undefined）
    ```
    在上例中，`arrow` 是用箭头函数定义在对象的字面量中，但注意它**不绑定 obj**：它的外层作用域是全局（这里假设非严格模式下 this 为 window），所以它输出 undefined（或 window.name 的值）。相反，regular 是普通方法，调用时 this 是 obj。
  
- **不能用作构造函数**：箭头函数没有 `[[Construct]]` 方法，所以无法使用 `new` 调用，否则会抛出错误。普通函数则可以当构造器 `new Func()` 创建对象。
  
- **没有 `arguments` 对象**：箭头函数内部也没有自己的 `arguments` 对象，引用 arguments 会沿作用域链查找外层函数的 arguments。普通函数在调用时会有对应的 arguments 列表。
  
- **不能改变 this**：除了 call/apply无效外，箭头函数也没有 `bind` 属性（或说 bind 对它无效），对箭头函数调用 bind返回的仍是原函数。因为箭头函数的 this 是在定义时确定并无法改变的。
  
- **其他区别**：箭头函数不能使用 `yield`（不能作为 Generator），也没有 `prototype` 属性（因为不能当构造函数）。

**应用**：
- 箭头函数常用于需要保持外部 this 的回调中，避免书写 `var self = this` 或使用 `.bind(this)`。比如 DOM 事件或定时器里，如果用箭头函数定义，this将和外面保持一致：
  ```js
  class Timer {
    constructor() {
      this.seconds = 0;
    }
    start() {
      setInterval(() => {
        this.seconds++;
      }, 1000);
    }
  }
  ```
  这里用箭头函数传给 setInterval，内部 this 就指向 Timer 实例（继承自 start 方法作用域），如果用普通函数则每次 this 为 window，需要用 bind绑定，否则 `this.seconds` 会是 undefined。
  
- 箭头函数因无自身 this，也无法 new 来实例化，因此更适合作为**纯函数**或小型函数使用，不适合定义对象的方法（除非明确想要外层this）。

**结论**：箭头函数 `this` 固定地继承外层上下文，不会因为调用方式改变，这大大简化了在复杂回调嵌套中保持正确 this 的问题。但也意味着箭头函数不能动态绑定不同 this，更不能用 new 创建对象。理解这一点有助于选择何时用箭头函数，何时必须用普通函数。

## 事件循环与异步

# Q21: 什么是事件循环（Event Loop）？JavaScript 是如何处理异步回调的？
A: **事件循环（Event Loop）**是 JavaScript 执行模型的核心机制。由于 JavaScript 是单线程执行的语言，需要通过事件循环来协调异步任务的执行，防止阻塞主线程。理解事件循环可以从以下几点：

- **调用栈（Call Stack）**：JS 引擎执行同步代码时使用调用栈管理函数调用顺序。当调用函数时入栈，执行完毕出栈。JavaScript 单线程意味着同一时间只能执行一个栈中的任务。
- **任务队列（Task Queue）**：当异步操作（如定时器、网络请求、DOM事件等）完成后，其对应的回调不会直接执行，而是被放入一个任务队列（也称消息队列）。常见任务队列可以区分为**宏任务（Macro Task）**队列和**微任务（Micro Task）**队列，两者优先级不同。宏任务包括setTimeout、setInterval回调、DOM事件、消息事件等。微任务包括 `Promise.then/catch/finally` 回调、`MutationObserver` 回调、`queueMicrotask` 等。
- **事件循环**：事件循环不断地检查调用栈是否为空，当调用栈空闲时（当前无同步任务执行），事件循环会从任务队列中取出下一个任务并将其回调推入调用栈执行。这个过程不断循环进行。
- **执行顺序**：
  1. 先执行全局同步代码，所有同步代码进调用栈依次执行，可能期间发起异步请求，但不会等待异步完成就继续运行后续同步。
  2. 当同步代码执行完，调用栈清空，事件循环开始从任务队列挑任务执行。
  3. **微任务优先**：在每一个宏任务执行完后，事件循环会**先检查并执行微任务队列中的所有任务**，然后再执行下一个宏任务。这意味着 `Promise` 回调（微任务）会在同一轮事件循环中、在当前宏任务结束后立即执行，而不像 setTimeout 那样至少下一轮才执行。
  4. 宏任务之间穿插着微任务处理：通常一个事件循环轮次会处理一个宏任务，然后处理尽可能多的微任务，然后再下一个宏任务，如此反复。
  
**举例**：
```js
console.log("script start");

setTimeout(() => {
  console.log("timeout");
}, 0);

Promise.resolve().then(() => {
  console.log("promise1");
}).then(() => {
  console.log("promise2");
});

console.log("script end");
```
执行顺序：
- 首先执行同步代码：打印 "script start"，设置 setTimeout 回调，创建 Promise 及其 then 回调（不会马上执行），打印 "script end"。
- 同步完后，调用栈空，检查微任务队列：Promise 的第一个 then 回调进入微任务队列并执行，打印 "promise1"。它完成后又添加了第二个 then 回调到微任务队列。
- 继续执行微任务队列的下一个，也就是 "promise2"，打印。
- 微任务清空后，再执行宏任务队列的下一个任务：setTimeout 的回调，打印 "timeout"。
- 所以输出顺序是: script start -> script end -> promise1 -> promise2 -> timeout。

这个例子体现了微任务 (`then`) 在 0ms 定时器之前执行。

**简而言之**：事件循环机制保证了 JavaScript 可以在处理异步任务时依然保持单线程模型，通过将异步任务的回调推迟到主线程空闲时执行，并区分不同优先级（微任务更早执行）。当我们编写异步代码时，理解事件循环有助于预测代码执行顺序、避免竞态条件和性能问题。

# Q22: 什么是宏任务和微任务？常见的宏任务、微任务举例？
A: **宏任务（Macro Task）**和**微任务（Micro Task）**是事件循环中两类不同优先级的任务。区分它们很重要，因为微任务总是在当前宏任务结束后尽快执行，在下一个宏任务之前。 

- **宏任务**：可以理解为在主线程上排队执行的**独立、完整的任务**。常见的宏任务来源有：
  - 主代码块（script整体）也是一个宏任务。
  - DOM 事件回调（如 click、load 等事件触发后对应的回调）。
  - 定时器回调：`setTimeout`、`setInterval` 的回调。
  - 网络请求回调：如 XMLHttpRequest、fetch 的回调（Fetch API 返回的是 Promise，所以最终执行回调在微任务，但XHR的readystatechange则作为宏任务）。
  - `postMessage` 消息、`setImmediate`（Node.js环境）等也是宏任务。
  每执行完一个宏任务后，事件循环会检查微任务队列，清空所有微任务，然后再开始下一个宏任务。

- **微任务**：发生在当前任务执行后、下一个宏任务开始前，需要立即处理的小任务。典型的微任务包括：
  - Promise 的回调（`.then/.catch/.finally`）。
  - `MutationObserver` 回调（监视DOM变化的API）。
  - `process.nextTick`（Node.js 特有，在Node中microtask优先级甚至高于Promise）。
  - ES2020 新增的 `queueMicrotask` 接口也能将函数加入微任务队列。
  在执行宏任务的过程中可以产生多个微任务，这些微任务会被加入微任务队列。当宏任务执行完毕后，事件循环会**立即执行所有微任务**，按顺序直到清空微任务队列。如果在执行微任务过程中又产生新的微任务（例如一个 then 回调又返回一个 Promise resolved 引发另一个 then），这些新微任务也会在本轮继续执行，避免饿死，直到微任务队列彻底清空才返回事件循环。

**重要的区别**：
微任务执行时机比宏任务更早。在同一轮事件循环中，微任务会在当前宏任务结束后全部执行完才进行下一个宏任务。因此微任务适合执行一些需要尽快完成的操作，比如 DOM 更新后立即处理，或 Promise 结果就绪后尽可能快地执行后续逻辑。

**举例说明**：
```js
console.log(1);

setTimeout(()=>console.log('macro'), 0);

Promise.resolve().then(()=> {
  console.log('micro1');
}).then(()=> {
  console.log('micro2');
});

console.log(2);
```
输出顺序：1、2（同步先） -> micro1、micro2（then 微任务，在宏任务之前全部执行） -> macro（最后执行宏任务 setTimeout 回调）。由此看出微任务队列在宏任务之前完成。

**总结**：了解宏任务和微任务有助于调试异步行为。一般来说：
- **微任务**（Promise 回调等）在一轮事件循环内尽早执行，适合承接需要紧跟前一步操作的后续逻辑。
- **宏任务**（setTimeout 等）则至少要等下一轮循环，常用于延迟执行或避免长任务阻塞。
合理利用微任务/宏任务，可以优化性能（比如减少不必要的重排），并确保代码按照期望的顺序执行。

# Q23: 为什么以下代码中 `setTimeout` 输出的总是最后一个索引？如何修复这个问题？
```js
for(var i = 1; i <= 5; i++) {
  setTimeout(() => {
    console.log(i);
  }, 1000);
}
```
A: 这段代码的意图可能是每隔1秒输出1,2,3,4,5。然而，实际执行会在1秒后连续输出五次 `6`（因为循环结束时 i 的值是6）。原因如下：

- `for(var i = 1; i <= 5; i++)` 使用了 **var** 声明，`var` 没有块级作用域，整个循环体中其实只有一个 `i`。当 `setTimeout` 的回调执行时，`for` 循环早已结束，`i` 已被自增到6，不再满足循环条件跳出循环。因此所有延迟执行的箭头函数访问的都是同一个全局 `i`，其最终值是6。
- `setTimeout` 将回调排入任务队列，并不会阻塞 for 循环。for 循环在几乎瞬间就把 i 从1加到了6，并安排了5个定时器。然后 1秒后，这5个回调依次执行，由于它们共享同一个 `i`，都输出6。

**解决方法**：需要为每个定时器的回调提供其各自作用域内的 `i` 值拷贝。常见方案有：

1. **使用 IIFE（立即执行函数）或块级作用域捕获变量**：
   用一个立即执行函数，把当前 i 作为参数传入，从而在其作用域内固定这个值。例如：
   ```js
   for(var i = 1; i <= 5; i++) {
     ((j) => {
       setTimeout(() => {
         console.log(j);
       }, j * 1000);
     })(i);
   }
   ```
   这里使用箭头函数作为 IIFE 参数也可以，用传统 IIFE：
   ```js
   for(var i = 1; i <= 5; i++) {
     (function(j) {
        setTimeout(function() {
          console.log(j);
        }, j * 1000);
     })(i);
   }
   ```
   这样每次循环都会调用函数并创建一个局部变量 j 保存了当时的 i 值，定时回调闭包引用的就是各自函数内的 j，所以输出期望的序列1,2,3,4,5。

2. **使用 `let` 替代 `var`**：
   ES6 中，`let` 声明具有块级作用域，每次迭代都会有一个新的 i。这是语言层面提供的解决方案：
   ```js
   for(let i = 1; i <= 5; i++) {
     setTimeout(() => { console.log(i); }, i * 1000);
   }
   ```
   用 `let` 后，循环每次迭代的 i 都是新的绑定，setTimeout 回调闭包捕获的是当次迭代的 i 值，从而正确输出 1,2,3,4,5。**这是最简洁的修复方式**。
   
3. **绑定参数**：
   有时也可以使用 `Function.prototype.bind` 传入固定参数，例如：
   ```js
   for(var i = 1; i <= 5; i++) {
     setTimeout(console.log.bind(console, i), i * 1000);
   }
   ```
   这里用 bind 将 `console.log` 的第一个参数固定为当前 i，这样输出的也是 1-5。不过这种方式依赖 console.log 的 this，可以简化为 `setTimeout(() => console.log(i), ...)` 前提是能用箭头函数或你想打印的内容是当前 i 值。
   
**本质**：需要理解**闭包捕获变量**的机制。原来的代码中所有回调共享一个变量导致问题，通过额外的作用域或 `let` 创建**独立的变量副本**即可解决。使用 `let` 是现代 JavaScript 中推荐的方式。

# Q24: Promise 有哪些状态？如何通过 Promise 处理异步流程？
A: **Promise** 是 ES6 引入的一种用于表示和处理异步操作结果的对象。一个 Promise 对象有以下**三种状态**：

1. **pending（待定）**：初始状态，既不是成功也不是失败，异步操作尚未完成。
2. **fulfilled/resolved（已完成/已解决）**：表示异步操作成功完成，Promise 有了一个**成功值**（可以是任何类型的值，甚至是另一个 Promise）。状态一旦变为 fulfilled，就不会再改变，结果值被固定下来。
3. **rejected（已拒绝）**：表示异步操作失败，Promise 有了一个**失败原因**（通常是 Error 对象）。状态变为 rejected 后也不可再改变。

Promise 状态的变化具有**不可逆**性：从 pending 可转移到 fulfilled 或 rejected，一旦转为 fulfilled 或 rejected，就终结了，不能再转为其它状态。

**通过 Promise 处理异步**：
- 创建 Promise：使用 `new Promise((resolve, reject) => { ... })`。在执行器函数中编写异步操作，完成时调用 `resolve(value)` 标记成功并提供结果，发生错误时调用 `reject(error)` 标记失败。
  ```js
  function asyncTask() {
    return new Promise((resolve, reject) => {
      // 模拟异步操作，例如1秒后得到结果
      setTimeout(() => {
        let success = true;
        if(success) resolve("OK");
        else reject(new Error("Something went wrong"));
      }, 1000);
    });
  }
  ```
- 使用 Promise：Promise 实例提供 `.then`、`.catch`、`.finally` 方法来处理结果：
  - `.then(onFulfilled, onRejected)`：注册成功和失败回调。Promise变为fulfilled时调用 onFulfilled，变为rejected时调用 onRejected（可选）。`then` 会返回一个新的 Promise，可以链式调用，以串联多个异步操作。
  - `.catch(onRejected)`：相当于 `.then(null, onRejected)`，用于统一捕获前面链条中的错误。
  - `.finally(onFinally)`：无论成功/失败都执行一些清理动作，不接收参数（因为不知道成功还是失败）。
  
  例如：
  ```js
  asyncTask()
    .then(result => {
      console.log("Result:", result);
      return doAnotherTask(result); // 返回另一个Promise或值
    })
    .then(nextResult => {
      console.log("Next:", nextResult);
    })
    .catch(err => {
      console.error("Error:", err);
    })
    .finally(() => {
      console.log("Task completed (either success or fail)");
    });
  ```
  这段代码展示了Promise链式调用的流程：`asyncTask` 成功则拿到result并用它触发 `doAnotherTask`（可能返回Promise），然后处理下一步结果；如果任何一步出现错误（reject），会跳到 `.catch`；最后 `.finally` 总会执行一次。

**Promise 异步流程优点**：
- 避免“回调地狱”：通过链式 `.then` 调用，可以按照逻辑顺序书写异步步骤，减少嵌套层次。
- 错误捕获统一：`catch` 可以捕获链条中任意一步的异常，不需要在每个回调里分别 try-catch。
- Promise 还可以通过 `Promise.all`、`Promise.race` 等方法组合并行异步操作：
  - `Promise.all([p1, p2, ...])` 返回一个新的 Promise，在全部 Promise 完成时fulfilled（结果按数组顺序），只要有一个失败就立即reject。
  - `Promise.race([p1, p2, ...])` 返回第一个决议（无论成功或失败）的Promise的结果。

**总结**：Promise 提供了更直观、可靠的异步编程模型。理解 Promise 的状态变化和方法，有助于编写复杂的异步逻辑并保持代码清晰。最终在 ES8 (ES2017) 中，又提供了 `async/await` 语法对 Promise 进行语法糖封装，使异步代码看似同步流程，但其底层依然基于 Promise。

# Q25: `async/await` 是如何基于 Promise 实现异步的？与直接用 Promise then 有什么不同？
A: **`async/await`** 是 ES2017 (ES8) 引入的语法，用于以更接近同步的方式书写异步代码。它是对 Promise 的进一步封装和优化，本质上 `await` 会暂停函数执行，等待 Promise 完成，并返回结果，就像同步等待一样，但不会阻塞整个线程。详细说明：

- `async` 关键字用于声明一个异步函数。异步函数调用时会返回一个 Promise，无需显式返回 Promise，`async` 函数内部**return** 的值会被自动包装成 Promise 解析的结果，如果抛出错误则包装成 Promise 拒绝的原因。
  ```js
  async function foo() {
    return 42;
  }
  foo().then(x => console.log(x)); // 输出42
  ```
  虽然 `foo` 函数返回的是 42（普通值），但调用 `foo()` 实际得到 Promise，通过 then 可以拿到42。这说明 async 函数始终返回 Promise。

- `await` 关键字只能出现在 `async` 函数内部，用来等待一个 Promise 完成。其作用是：暂停当前异步函数的执行，等待 `await` 后面的 Promise **fulfilled** 并获取其值，或如果 Promise **rejected** 则抛出异常。然后继续执行函数体后面的代码。
  ```js
  async function getData() {
    try {
      let response = await fetch('https://api.example.com/data');
      let result = await response.json();
      console.log("Result:", result);
    } catch(err) {
      console.error("Error:", err);
    }
  }
  ```
  在这个例子中，`await fetch(...)` 会暂停 `getData` 函数，直到网络请求 Promise resolved，拿到 response 对象，再赋值给 response 变量。然后执行下一行 `await response.json()`, 等解析 JSON 的 Promise 完成获得 result。错误用 try/catch 捕获，就像同步代码抛异常时被捕获一样。
  
- `async/await` 相对于直接使用 `.then` 的区别和优点：
  1. **代码结构更清晰**：可以直接用同步的写法表达异步流程，省去了层层 then 回调的嵌套，使逻辑顺序一目了然。例如多个异步步骤按顺序执行，用 await 串起来比 then 链条更直观。
  2. **错误处理简单**：可以使用常规的 try/catch 来捕获 await 表达式中 Promise 的拒绝，相当于捕获链条中的错误，而不需要像 Promise 那样在链尾写 `.catch` 或在每个 then 里处理错误。
  3. **调试方便**：在调试时，async/await 写法可以逐行跟踪，不像 .then 回调跳出函数外层，相对更容易设置断点。
  4. **性能**：在大多数情况下 async/await 与原生 Promise then 性能相当。await 本身不是新的并发模型，它只是把后面的代码放在 Promise 回调中实现。因此**行为上** async/await 完全等价于基于 Promise 的实现，它没有引入额外的事件循环机制。

- 需注意的是，虽然 `await` 写起来像是阻塞的同步等待，但它只阻塞当前 async 函数的执行，不会阻塞其他并发操作和整个主线程。当遇到 await，async 函数将挂起让出线程处理其他任务，Promise 完成时再继续该函数。因此 async/await 仍然是**非阻塞**的。

- 一个 async 函数里的多个 await 默认是串行执行的（上一行 await 结束再执行下一行），如果希望并行执行多个异步任务，可以将 Promise 同时创建，然后用 `await Promise.all([...])` 一起等待：
  ```js
  async function parallelTasks() {
    let [res1, res2] = await Promise.all([fetch(url1), fetch(url2)]);
    // 两个请求并行，res1, res2 都拿到
  }
  ```
  这样不会因为两个 await 写两行就变成串行（即先等第一个 fetch，再发第二个），通过 Promise.all 保持并发。

**总结**：`async/await` 是 Promise 的语法糖，让代码更接近同步逻辑直觉，减少回调嵌套。其背后仍由 Promise 驱动，没有改变 Promise 状态和事件循环的原理。好的做法是将复杂的 Promise 链改写为 async 函数，使代码更可读可维护，同时充分利用 Promise 的并行能力（Promise.all 等）优化性能。

## DOM 与 BOM 操作

# Q26: DOM 常见的操作有哪些？如何创建、获取、修改 DOM 元素？
A: **DOM（文档对象模型）**提供了操作网页文档结构的接口。常见的 DOM 操作包括**创建节点**、**查找节点**、**修改节点内容或属性**、**插入和删除节点**等：

- **查找/获取元素**：
  - `document.getElementById("id")`：根据元素的 id 获取唯一匹配的元素节点，返回 `HTMLElement` 或 `null`。
  - `document.getElementsByClassName("cls")`：获取具有特定类名的所有元素，返回`HTMLCollection`实时集合。
  - `document.getElementsByTagName("div")`：按标签名获取元素集合。
  - **推荐**使用 `document.querySelector(selector)` 获取第一个匹配 CSS 选择器的元素，`document.querySelectorAll(selector)` 获取所有匹配元素的静态`NodeList`集合。比如：
    ```js
    let firstItem = document.querySelector("#list li.item");
    let allItems = document.querySelectorAll("ul#list > li.item");
    ```
    querySelectorAll 返回的 NodeList 可以通过索引访问或 for..of 遍历，不具备 live 特性（不会自动更新），通常比 getElementsByClassName 等更灵活强大。
  - 还可以使用元素的方法如 `element.querySelector(...)` 在特定容器下查找子元素。

- **创建元素**：
  - `document.createElement(tagName)`：创建指定标签名的新元素节点，例如 `document.createElement("div")`。
  - `document.createTextNode("text")`：创建文本节点。也可以直接设置元素的 `textContent` 或 `innerText` 来生成文本节点。
  - 现代浏览器可以用模板字符串和 `insertAdjacentHTML` 方法快速插入 HTML 字符串为元素：
    ```js
    container.insertAdjacentHTML('beforeend', '<p>新段落</p>');
    ```
    这会创建并插入 `<p>` 元素及文本。

- **插入节点**：
  - `parent.appendChild(newNode)`：将新节点加为 parent 的最后一个子节点。如果 newNode 是已有节点，会移动它（从原位置移除，然后追加到新父节点）。
  - `parent.insertBefore(newNode, referenceNode)`：将 newNode 插入到 referenceNode 之前。如果 referenceNode 为 null，相当于 appendChild到末尾。
  - ES6 新增的 `parent.append(...nodes)` 和 `parent.prepend(...nodes)` 可以同时插入多个节点或字符串（字符串会自动转为文本节点）。
  - `node.insertAdjacentElement(position, elem)` / `node.insertAdjacentHTML(position, html)`：可相对于 node 自身插入（position 可以是 'beforebegin','afterend','afterbegin','beforeend'，分别表示在元素外部前/后或元素内部前/后插入）。

- **删除节点**：
  - `parent.removeChild(child)`：从 parent 移除一个子节点，返回被移除的节点。
  - `element.remove()`：直接移除自身（较新的标准方法）。
  
- **修改内容和属性**：
  - 修改文本：`element.textContent = "新文本"` 可以改变元素的文本内容（替换其所有子节点为一个文本节点），`innerText` 类似但会受CSS影响（如不获取隐藏元素文本等差异）。
  - 修改HTML：`element.innerHTML = "<b>HTML字符串</b>"` 会解析字符串为DOM树替换当前所有子内容。注意 innerHTML 插入未经转义的HTML，需确保字符串安全（防止XSS）。也可以读取 innerHTML 获取元素内部序列化的HTML。
  - 属性操作：`element.setAttribute(name, value)` 设置属性，`getAttribute(name)` 获取属性值，`removeAttribute(name)` 删除属性。例如：
    ```js
    img.setAttribute("src", "pic.png");
    div.getAttribute("data-id");
    ```
    对于标准属性，也可以直接用属性对应的属性（property）赋值：
    ```js
    element.id = "header";
    element.className = "main highlight";
    element.value = "hello";
    ```
    这些属性改变会同步反映到DOM属性上。需要注意对于 `class` 属性，可以用 `element.classList` 来方便地添加、删除、切换类，如 `element.classList.add("active")`。
  - 样式操作：可以直接设置 `element.style` 属性（内联样式），如 `element.style.color = "red"`。或修改 CSS 类以应用外部样式表定义的规则。
  
- **尺寸和位置**：
  - DOM 提供属性如 `element.clientWidth/Height`（内容+内边距大小），`element.offsetWidth/Height`（包含边框），`element.scrollWidth/Height`（内容滚动区域大小），以及 `element.getBoundingClientRect()` 方法获取元素相对于视口的位置和尺寸。
  - 设置元素位置通常通过 CSS 定位样式（如 style.left/top 等）或设置 CSS 类。

**DOM 事件**本质上也是 DOM 操作之一，通过 `element.addEventListener(type, callback)` 来注册事件监听，常见事件类型如 `'click'`, `'input'`, `'submit'`, `'keydown'` 等。

总结来说，DOM 操作允许脚本对页面结构和内容进行**增删查改**：
- 查（获取元素）
- 增（创建并插入节点）
- 删（移除节点）
- 改（改变内容、属性和样式）

这些操作构成了动态更新网页的基础。合理使用 DOM API 可以动态地创建丰富的网页交互内容。

# Q27: 什么是 BOM？常见的 BOM 对象你了解哪些？
A: **BOM（Browser Object Model，浏览器对象模型）**指浏览器提供的、操作浏览器窗口和浏览器外部元素的对象集合。简单说，BOM 包含**浏览器环境**的各个方面的对象，而 DOM 则针对页面文档。本质上，BOM 对象是挂在全局对象 `window` 下的一系列属性和方法，用于与浏览器进行交互。常见的 BOM 对象和功能包括：

- **window** 对象：代表整个浏览器窗口，是 BOM 的核心对象，也是全局对象。所有全局变量和函数都是 `window` 的属性。常用的 `window` 属性和方法：
  - 尺寸和位置：`window.innerWidth/innerHeight`（窗口内容区域宽高），`window.outerWidth/outerHeight`（包括浏览器边框菜单的外窗口尺寸）。`window.screenX/screenY`（窗口相对于屏幕的位置）。
  - 控制浏览器：`window.open(url, target)` 打开新浏览器窗口/标签，`window.close()` 关闭当前窗口（需满足同源策略或由脚本打开的窗口）。
  - 定时调用：`window.setTimeout(fn, delay)` 和 `window.setInterval(fn, interval)` 属于 BOM 提供的计时功能，用于异步延迟或周期执行任务（这两个经常简写为 `setTimeout` 和 `setInterval`，因为在全局作用域下可以直接调用它们）。
  - 交互：`window.alert()`、`window.confirm()`、`window.prompt()` 用于简单的模态对话框交互。
  - `window.scroll(x, y)` 或 `window.scrollTo/scrollBy` 控制页面滚动。
  - 全屏、聚焦等：`window.focus()`、`window.blur()`、`window.moveTo()` 等。
  
- **document** 对象：虽然 DOM 的主要接口也是 document，但从 BOM 角度看，document 属于 window 的属性之一 (`window.document`)。它表示当前加载的网页文档，通过 DOM API 操作页面结构。

- **navigator** 对象：提供浏览器自身的信息，如 `navigator.userAgent`（浏览器的UA字符串），`navigator.platform`（操作系统平台），`navigator.language`（首选语言）。还包含判断浏览器能力的方法，如 `navigator.onLine`（是否联网），`navigator.geolocation`（地理定位API入口），`navigator.mediaDevices` 等。历史上还有 `navigator.appName`、`navigator.appVersion` 等用来判断浏览器厂商的属性，但由于 UA 伪装等原因已不可靠。

- **location** 对象：表示当前窗口的URL信息，允许读取或修改 URL。常用属性：
  - `location.href`：完整URL字符串，可赋值修改以跳转页面。
  - `location.protocol`（协议，如 "https:"），`location.host`（域名和端口），`location.pathname`（路径），`location.search`（查询字符串），`location.hash`（锚点）。
  - 方法：`location.assign(url)` 加载新URL（与设置 href 类似），`location.replace(url)` 替换当前页面（不会产生历史记录），`location.reload()` 刷新页面。

- **history** 对象：管理浏览历史记录栈，允许在用户历史记录中导航。主要方法：
  - `history.back()` 相当于浏览器后退按钮。
  - `history.forward()` 前进。
  - `history.go(n)` 前进或后退 n 步（n可正可负）。
  - HTML5 新增 `history.pushState(stateObj, title, url)` 和 `history.replaceState(...)` 可以不刷新页面地改变地址栏URL并添加历史记录（用于 AJAX 应用路由）。
  - `history.length` 属性表示历史堆栈长度。

- **screen** 对象：提供关于用户屏幕的信息。如 `screen.width/height`（屏幕分辨率），`screen.availWidth/availHeight`（浏览器可用的屏幕空间，不含系统UI占用部分）等等。通常用得少，偶尔用于弹出窗口居中计算或者简单的分辨率统计。

- **frames** 和 **frameElement**：`window.frames` 类数组包含当前窗口中所有 iframe 窗口对象（不同域可能受限于同源策略），`window.frameElement` 则表示当前窗口自己是父窗口中哪个 iframe（如果当前页面在iframe里）。

**总结**：BOM 提供了浏览器环境层面的接口：**窗口 (window)**、**导航 (location, history)**、**浏览器信息 (navigator, screen)** 等。通过 BOM 对象，脚本可以控制浏览器窗口行为、跳转页面、获取浏览器和用户设备的信息等。需要注意，出于安全和用户体验考虑，某些 BOM 操作会有权限或用户交互限制，比如 `window.close()` 只能关闭由脚本打开的窗口，`alert` 等会阻塞JS线程等等。掌握 BOM 能让我们在网页中更好地与浏览器交互和控制环境。

# Q28: 请解释事件冒泡和事件捕获，以及如何使用 event.stopPropagation() 和 event.preventDefault()。
A: **事件冒泡（Event Bubbling）**和**事件捕获（Event Capturing）**是 DOM 事件传播的两个阶段（加上事件触发目标阶段，共三个阶段）。当一个元素触发事件时，事件会按照特定顺序在 DOM 树中传播：

- **事件捕获阶段**：事件从最顶层的祖先（通常是 `window`）开始，向下经过每一级祖先元素，直到到达事件触发的目标元素。这就像事件从页面顶部“捕获”到目标的过程。默认情况下，大多数事件不会在捕获阶段被拦截处理，但可以通过设置事件监听器的 `capture` 选项为 true 来在捕获阶段处理。

- **目标阶段**：事件到达目标元素，本身触发的事件监听器被执行。

- **事件冒泡阶段**：事件从目标元素向上冒泡，依次向上传播到其父节点 -> 再到祖父节点 -> … -> 最终到达 `window` 对象。每经过一个节点，就会触发该节点上类型匹配的事件监听器（如果有，且监听器未在捕获阶段执行）。

默认情况下，大多数 DOM 事件（如点击 click、键盘 keydown 等）都会经历冒泡阶段。这意味着子元素上的事件可以被父元素侦听器捕获到。比如点击一个按钮，会先执行按钮本身的点击处理，然后冒泡到父容器触发容器的点击处理，再冒泡到 document 等。

**事件捕获 vs 冒泡**：
- 捕获阶段很少在日常直接使用，可以用来在更早阶段拦截事件。通过 `element.addEventListener(event, handler, {capture: true})` 或第三参数 true（旧写法）来注册捕获阶段监听。
- 默认 addEventListener 第三个参数为 false，监听的事件在冒泡阶段触发。
- IE旧版不标准实现用 `element.attachEvent` 是只有冒泡没有捕获的。
- 举个过程例子：有结构 `<div id="parent"><button id="child">Click</button></div>`。在 child 按钮上点击：
  - 捕获阶段：window -> document -> div#parent -> button#child（此阶段如果对应元素有捕获型监听则按序执行）
  - 目标阶段：button#child 上注册的非捕获监听执行
  - 冒泡阶段：button#child -> div#parent -> document -> window（此阶段触发冒泡型监听）

**event.stopPropagation()**:
- 调用事件对象的 `stopPropagation()` 方法可以**停止事件在DOM树中进一步传播**。如果在目标阶段或冒泡阶段调用它，则事件不会继续冒泡到父节点。如果在捕获阶段调用，则阻止事件到达目标及后续冒泡。
- 常用于：避免某个元素的事件触发连带触发父元素上相同事件的处理。例如点击一个链接，我们不希望冒泡触发上层容器的点击逻辑，可以在链接点击回调中 `e.stopPropagation()`。
- 注意：stopPropagation 只阻止传播，不会阻止当前元素默认行为的发生，也不影响其他绑定在同一元素上的监听器执行。

**event.preventDefault()**:
- 调用事件对象的 `preventDefault()` 可以**阻止事件的默认动作**发生。默认动作指浏览器对某些事件的预定义行为。例如点击链接的默认行为是跳转URL，点击提交按钮默认提交表单，按键盘输入文本默认在输入框中显示字符等。
- 如果希望阻止这些默认行为（如单页应用中点击链接不真正跳转、或验证失败时阻止表单提交），可以在事件监听中调用 event.preventDefault()。
- 常用的场景如：表单 `onsubmit` 中 `event.preventDefault()` 阻止表单提交然后以 AJAX 处理；超链接 `<a href="#">` 的点击通过 preventDefault 阻止页面跳转等。
- preventDefault 并不停止事件传播，只阻止浏览器自动执行的后续动作。

**综合示例**：
```html
<div id="outer" style="padding:30px; background: lightblue;">
  Outer
  <button id="inner">Inner</button>
</div>
<script>
  document.getElementById('outer').addEventListener('click', (e) => {
    alert('Outer clicked');
  });
  document.getElementById('inner').addEventListener('click', (e) => {
    alert('Inner clicked');
    e.stopPropagation(); // 阻止冒泡
    // e.preventDefault(); 如果 inner 是个链接或提交按钮，可以这样阻止其默认行为
  });
</script>
```
如果点击 inner 按钮：先弹"Inner clicked"，然后由于 stopPropagation 调用了，事件不会冒泡到 outer，所以不会弹"Outer clicked"。如果去掉 stopPropagation，则会先弹Inner再弹Outer。

**事件捕获使用**：
```js
outer.addEventListener('click', () => { console.log('Outer capture'); }, true);
inner.addEventListener('click', () => { console.log('Inner capture'); }, true);
```
假如都用捕获监听，点击 inner，顺序将是 "Outer capture" -> "Inner capture" -> （没有冒泡监听就结束）。如果既有捕获又有冒泡，会按照捕获监听 -> 目标 -> 冒泡监听顺序。

**总结**：事件捕获是从上到下先行触发监听的机制，事件冒泡是从下到上传播事件的机制。大部分情况下我们操作的事件监听都是冒泡阶段，便于事件代理（delegate）等用法。使用 `stopPropagation()` 可以防止事件冒泡传播，使用 `preventDefault()` 可以阻止默认行为。两者经常组合使用在某些场景，比如点击链接既不冒泡也不跳转。理解这些概念有助于精确控制复杂界面交互中的事件处理。 

# Q29: 什么是事件委托（事件代理）？它有什么优点，如何实现？
A: **事件委托（Event Delegation）**也称事件代理，是一种利用事件冒泡机制优化事件处理的技术。其核心思想是：**不在子元素上设置大量事件监听，而是利用父元素监听冒泡上来的事件**，根据事件目标（event.target）判断来自哪个子元素，从而执行相应逻辑。这样可以用一个父级监听器替代多个子元素监听器。

**优点**：
- **减少内存和性能开销**：如果有很多类似的子元素都需要监听同一类型事件，传统做法是为每个元素都绑定监听函数，会消耗较多内存和注册开销。而事件委托只需在父容器上注册一次监听，即可处理任意数量子元素的事件。
- **动态元素支持**：对于运行时动态增加或删除的子元素，如果用事件委托，无需为新元素单独绑定事件，父级监听器依然能捕获其冒泡事件。避免了手动为新节点添加监听或在移除时解绑。
- **维护方便**：逻辑集中在一起便于管理，而不是散落在各子项。

**实现**：
1. 选择一个共同的祖先元素（通常是直接父容器）来添加事件监听器。
2. 在监听器回调中，通过 `event.target` 或 `event.currentTarget` 来判断事件源。`event.target` 是实际触发事件的最深层子元素，`event.currentTarget` 则是当前监听器绑定的元素（即父容器本身）。一般我们需要 target 来知道是哪一个子元素被点击或操作。
3. 判断 target 是否匹配我们关心的子元素（可以检查 `target.tagName`、`target.classList`或`id`等属性，或使用 `.matches(selector)` 方法匹配CSS选择器）。
4. 执行对应于该子元素的行为。如果希望停止事件进一步冒泡，也可选择调用 stopPropagation，但通常没必要，因为已经在父容器处理了。

**例子**：
假设有一个列表，每项都有删除按钮，如果不用委托，可能为每个按钮绑定 `click` 去删除所在项。用事件委托可如下：

HTML:
```html
<ul id="itemList">
  <li data-id="1">Item 1 <button class="delete-btn">Delete</button></li>
  <li data-id="2">Item 2 <button class="delete-btn">Delete</button></li>
  <li data-id="3">Item 3 <button class="delete-btn">Delete</button></li>
  <!-- ... -->
</ul>
```
JavaScript:
```js
const list = document.getElementById('itemList');
list.addEventListener('click', function(e) {
  // 检查事件源是否为我们想要处理的元素
  if(e.target && e.target.matches('button.delete-btn')) {
    // 找到被点击按钮所属的<li>
    const item = e.target.closest('li');
    console.log('Delete item', item.dataset.id);
    item.remove(); // 删除该项
    // 这里可以 e.stopPropagation() 但一般不需要
  }
});
```
在这个委托中，我们给整个 `<ul>` 绑定了一个点击监听。当点击任意 `<button.delete-btn>` 时，事件冒泡到 ul 触发回调。回调中通过 `e.target.matches('button.delete-btn')` 判断点击来源是不是删除按钮。如果是，则找到最近的 li 项并将其删除。这样，无论有多少 list items，都不需要每个按钮都绑定处理。而且将来若动态添加 `<li><button class="delete-btn"></button></li>` 项，委托仍然有效。

**注意**：
- 某些复杂的UI，事件委托非常实用，比如表格单元格编辑、菜单项选择等。
- 并非所有事件都冒泡，例如 `focus`、`blur` 不冒泡（有对应的 focusin/focusout 会冒泡），因此不能委托；可以借助捕获阶段或特定事件的冒泡替代。
- 委托层级不宜过高，否则要做更多判断筛选，不然所有不相关的子节点事件也冒泡上来增加无谓的计算。
- `event.target` 可能是子元素的子元素，比如点击 `<li>` 里的 `<span>`，target 会是 span。一般用 `Element.closest(selector)` 来找到匹配的祖先子元素是稳妥的方式。

**总结**：事件委托有效利用了事件冒泡，可以减少绑定、处理大量类似元素的事件逻辑，在需要动态增删元素的场景也非常方便。掌握事件委托能够编写出更高效、可维护的前端代码。

## 函数与闭包

# Q30: JavaScript 中函数的参数是按值传递还是按引用传递？
A: 在JavaScript中，所有函数参数都是**按值传递**（pass by value）的。这意味着当函数被调用时，会将参数表达式的值赋给函数内部的参数变量。对于基本类型，这很好理解：传递的就是值的副本，函数内部修改该参数不会影响外部变量。对于引用类型（对象、数组等），“按值传递”意味着传递的是对象的**引用的副本**，即指向同一个对象的引用地址被复制一份传入函数。因此在函数内部通过这个引用可以修改对象的内容，外部也能看到改动；但如果函数内部将参数变量重新赋值为另一个对象，外部原始对象引用不会受影响。

**细分说明**：
- **基本类型（Number, String, Boolean, undefined, null, Symbol）**：按值传递。函数内部参数变量和外部变量没有关联，修改内部不会改变外部。
  ```js
  function increment(x) {
    x = x + 1;
    console.log("inside:", x);
  }
  let a = 5;
  increment(a);
  console.log("outside:", a);
  // 输出：inside: 6, outside: 5 （a未变，因为x是a值的副本）
  ```
- **引用类型（Object, Array, Function 等对象）**：传递对象引用的副本。也就是说，函数参数得到的是指向同一对象的引用（内存地址）。因此函数内部通过这个引用修改对象的属性，会影响原对象；但如果函数内部给参数重新赋值，让它指向另一个对象，外部原来的对象引用不变。
  ```js
  function modifyObj(obj) {
    obj.name = "Bob";
    obj = { name: "Carol" };  // 改变obj指向一个新对象
  }
  let person = { name: "Alice" };
  modifyObj(person);
  console.log(person.name); // 输出 "Bob"
  ```
  解释：传入 modifyObj 时，obj 和 person 指向同一个 `{ name: "Alice" }` 对象。第一行 `obj.name = "Bob"` 修改了对象属性，person 也能看到变为 "Bob"。第二行 `obj = { name: "Carol" }` 仅仅改变了函数内部 obj 变量的指向，让它指向新创建的对象。但并不会改变外部 person 仍指向原来的对象。所以函数返回后，person 仍引用原对象，它的 name 被之前的操作改成 "Bob"。

因此，说“按引用传递”的语言通常意味着函数参数本身引用同一对象并且可以改变外部引用所指向的对象关系，而 JavaScript 并非如此——它永远都是把值传进去，只是当值是一个对象引用时，看起来似乎传地址，所以容易混淆。正确理解是**引用类型也是按值传递，只不过这个值是引用**。

**结论**：JavaScript 没有按引用传递（pass by reference）。函数参数无论基本还是对象，都是按值传递。对象的可变性导致在函数内部修改对象成员会体现到外部，但这仍是通过共享引用实现的“按值传递”。记住这一点可以避免误以为在函数内部重新给对象参数赋值能影响外部变量引用，这其实是无效的。

# Q31: 如何在 JavaScript 中实现函数柯里化 (Currying)？
A: **函数柯里化（Currying）**是指将一个接受多个参数的函数转换成一系列每次接受一个参数的函数的技术。本质上柯里化把多元函数变为一元函数链。例如，一个 `f(a, b, c)` 经过柯里化可以变为 `f(a)(b)(c)` 这种调用形式。柯里化允许固定某些参数产生一个新的函数，这在构建可重用和灵活的函数时非常有用。

**实现方法**：

假设有一个函数需要柯里化，比如：
```js
function add(a, b) {
  return a + b;
}
```
把它柯里化后可以调用为 `add(1)(2)` 得到 3。

简单实现柯里化可以手动返回函数闭包，例如：
```js
function curryAdd(a) {
  return function(b) {
    return a + b;
  };
}
const addOne = curryAdd(1);
console.log(addOne(5)); // 6
console.log(curryAdd(2)(3)); // 5
```
上面 `curryAdd` 固定了第一个参数，返回新的函数接受第二个参数完成求和。这其实就是柯里化思想的应用。然而这是只对特定二元函数手动实现的例子。

更通用的柯里化可以写一个**高阶函数** `curry(fn)`，使之接受任意函数并返回一个柯里化版本。比如：
```js
function curry(fn) {
  return function curried(...args) {
    if(args.length >= fn.length) {
      // 当提供的参数数量达到原函数形参个数时，直接调用原函数
      return fn.apply(this, args);
    } else {
      // 否则返回一个新的函数，继续收集剩余参数
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      }
    }
  };
}
```
解释：
- `fn.length` 在函数未使用剩余参数的情况下，返回定义的形参数量。我们用它判断需要多少参数。
- `curried` 函数每次被调用时检查已接收参数数，如果够了就用 `fn.apply` 执行原函数并返回结果；不够则返回一个新的函数 `function(...args2)`，该函数会把后来传的参数和之前的合并，再递归调用 `curried` 本身。
- 这样实现的 `curried` 函数可以被多次调用累积参数，直到参数数量足够为止。

使用：
```js
function multiply(x, y, z) {
  return x * y * z;
}
const curriedMultiply = curry(multiply);
console.log( curriedMultiply(2)(3)(4) ); // 24
console.log( curriedMultiply(2, 3)(4) ); // 24
console.log( curriedMultiply(2)(3, 4) ); // 24
```
这个 `curry` 实现支持一次传入多个参数或一个参数的灵活调用方式，直到全部参数到齐调用原函数得到结果。

**注意**：柯里化函数在收集参数的过程中，会返回嵌套的函数，这些函数使用闭包存储了已经提供的参数。只有当参数足够时才真正执行原函数。柯里化并不会执行函数，只是重新组织函数调用方式。

**用途**：
- 固定某些参数得到新函数。如有 `let add10 = curriedAdd(10); add10(5)`。
- 延迟计算。柯里化函数可以逐步传参，最后才执行，这可以用在按需求值的场景。
- 组合函数。与函数式编程组合其他高阶函数更方便，比如配合 `.map()` 等，可以用 `array.map(curriedFunc(fixedParam))`。

简言之，柯里化是一种重要的函数式编程技巧，以上实现展示了如何通过闭包和参数检测将一个普通函数转变为柯里化函数。

# Q32: 你如何理解 JavaScript 中的深拷贝（deep copy）和浅拷贝（shallow copy）？
A: **浅拷贝**和**深拷贝**是针对拷贝复杂数据结构（对象/数组）时所复制的层次深度而言的概念：

- **浅拷贝（Shallow Copy）**：拷贝对象时，只复制对象本身的属性和引用，但**不递归复制嵌套的子对象**。也就是说，拷贝得到的新对象的属性中，如果有引用类型（对象、数组），它们仍然指向原来对象中的同一个引用。浅拷贝会导致原对象和拷贝对象共享嵌套的子对象。

  常见浅拷贝的方式：
  - 使用 `Object.assign({}, source)`：将 source 自身可枚举属性拷贝到新对象。但如果 source 有属性是对象，assign 仅复制引用。
  - 使用展开运算符 `let newObj = {...source}` 或数组的 `let newArr = [...sourceArray]`。这在顶层会创建新的对象或数组，但内部元素若为对象引用则共享。
  - `Array.prototype.slice()` 或 `concat()` 复制数组也是浅拷贝，只复制一层。

  **例子**：
  ```js
  let obj1 = { a: 1, arr: [2,3] };
  let obj2 = Object.assign({}, obj1);
  obj2.arr.push(4);
  console.log(obj1.arr); // [2,3,4] 原对象的嵌套数组也被改变，说明只是共享了引用
  ```
  这里 assign 创建了 obj2，并将 obj1.arr 的引用赋给 obj2.arr。修改 obj2.arr 会影响 obj1.arr。

- **深拷贝（Deep Copy）**：递归地拷贝对象的所有嵌套属性，**确保副本与原对象在内存中完全独立**。也就是说，拷贝出的新对象对于任意层级的嵌套子对象，都是新创建的，与原对象不共享引用。修改新对象不会对原对象产生任何影响（只要不是通过共享外部资源如DOM、单例等）。

  实现深拷贝的方法：
  - 手写递归：检查每个属性类型，如果是对象（且不是特殊对象比如 Date/RegExp/函数 等需要特殊处理），就递归复制它内部的属性。
  - 使用 JSON 序列化：简单粗暴的方法 `let newObj = JSON.parse(JSON.stringify(obj))` 可以得到深拷贝（对象值必须可序列化）。但缺点也明显：会丢失函数、`undefined`、`Symbol`等信息，日期会变字符串，正则对象会变空对象等，所以只适合纯数据对象。
  - 使用库：如 Lodash 的 `_.cloneDeep()` 能安全地进行深拷贝，涵盖很多边界情况。
  
  **例子**：
  ```js
  let obj1 = { x: 10, y: { z: 20 } };
  let obj2 = JSON.parse(JSON.stringify(obj1));
  obj2.y.z = 99;
  console.log(obj1.y.z); // 20，原对象未受到影响
  ```
  这里 JSON 方法创建了 obj1 完全独立的副本 obj2。修改 obj2 深层属性不会反映到 obj1。

**需要注意**：JavaScript 中一些对象不能或不宜深拷贝：
- 函数拷贝：函数会丢失闭包，直接拷贝函数引用通常即可，不去深拷贝。
- DOM 节点拷贝：DOM元素深拷贝需特殊方法，比如 `element.cloneNode(deep)`。
- 内置对象（Date、RegExp、Map、Set等）拷贝需要在实现里做特殊判断处理，用 JSON 法会出问题。
- 存在循环引用的对象深拷贝也需要特别处理，否则递归会死循环。

**应用**：
- **浅拷贝**常用于想复制一个对象/数组的顶层结构，且确定内部对象可共享或不包含复杂引用时。例如想通过改变副本来不改变原对象的某些顶层属性，但不介意它们共享子对象。
- **深拷贝**用于需要完全隔离副本与原对象的情形，避免副作用。例如在 Redux 状态更新时，通常通过深拷贝（或immutable数据结构）产生新状态，保证旧状态不变。
- 深拷贝比浅拷贝成本高，因为需要递归处理更多数据，使用时要权衡性能和必要性。

总结：浅拷贝复制对象的一层，嵌套对象仍共享；深拷贝复制对象的所有层，嵌套对象独立。选择哪种拷贝取决于实际需求和数据结构的复杂程度。