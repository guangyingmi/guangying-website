### TypeScript

由于JavaScript是弱类型语言，导致许多地方不严谨，所以强类型语言的TS发展迅速！可能很多同学会觉得增加了TS会增加很大的学习负担或者难度，但是我想说真的只有自己系统的使用了才知道项目中使用TS是多么的香！

> 1、TypeScript 中 any、unknown、never、void、null和undefined 有什么区别？为什么最好不要使用any？

用过TS的都知道，解决报错的万能大法，一般出现TS类型报错，使用any就可以让我们爆红的编辑器瞬间好起来，但是回首一看发现类型定义全是使用any定义，那这样的话其实就违背了我们引入TS的初衷了，所以我们应该尽可能的少使用any(除非是真的没办法)，那减少使用any首先我们应该需要先了解any类型，对症下药才能解决问题

- any类型：该类型用于描述一个不知道的类型的变量，简单点说就是任意类型的变量，他不会做任何的类型约束，而且类型检查器不会对这些值进行检查而是直接让它们通过编译阶段的检查，所以如果我们大范围的使用any类型的话，那我们引入TS的意义就不大了

我们可以看到，只要我们定义了一个变量为any，那么无论后续将其赋值为什么类型的值都是没问题的，所以我们可能就会在项目中为了方便，随手就定义了大片的any，为了项目以后可以更好的维护，所以建议不要太多使用！
```js
let notSure: any = 4
notSure = "maybe a string instead"
notSure = false

let list: any[] = [1, true, "free"]
list[1] = 100
```
- unknown类型：该类型表示的是未知类型，即写代码的时候可能并不知道具体会是什么数据类型，是3.0之后引入的新类型，和any类型一样的是，所有的类型也都可以分配给unknown类型；但是，unknown类型的变量是不允许被any或者unknown以外的变量赋值的，也不能执行unknown类型变量的方法：
```js
let notSure: unknown = 4
notSure = "maybe a string instead"
 
// OK, definitely a boolean
notSure = false
```
```js
const maybe: unknown = 'string'
const aNumber: number = maybe
// Type 'unknown' is not assignable to type 'number'
maybe.toLowerCase()
// error: Object is of type 'unknown'
```


那我们如果想要使用unknown类型来代替any使用，上面的问题我们怎么来解决呢？
我们常用的其实有两种方法：那就是类型断言和类型守卫


**方式一：使用类型断言缩小未知范围**
```js
let maybe: unknown = 'string'
(maybe as string).toLowerCase()   // OK
```


**方式二：使用类型守卫进行类型收缩**
```js
let maybe: unknown = 'string'

if (typeof maybe === 'string') {
  const aString: string = maybe   //OK
  maybe.toLowerCase()  // OK
  (maybe as string).toLowerCase()   // OK
}
```
- never类型：该类型表示的是那些永不存在的值的类型，那什么时候是永不存在的，比如当变量被永不为真的类型保护所约束时；还有就是never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型

**⚠️注意：never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。**

```js
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```
- void类型：某种程度上来说，void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void
```js
function warnUser(): void {
    console.log("This is my warning message");
}
```
我们也可以声明一个void类型的变量（但是其实没有什么大用），因为你只能为它赋予undefined和null：
```js
let unusable: void = undefined;
let unusable: void = null;  // 当你指定了--strictNullChecks标记，此项会报错
```

- null和undefined类型：TypeScript里，undefined和null两者各自有自己的类型分别叫做undefined和null。 和 void相似（它们的本身的类型用处也不是很大）
```js
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
```
**⚠️注意：默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。
然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自**

最后我们来看一张总结的图：
![](_media/../../../_media/collection/%20TS/WX20210830-215046.png)