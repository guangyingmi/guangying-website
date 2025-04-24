# 设计模式

# 一、工厂模式（Factory Pattern）

## 什么是工厂模式？

工厂模式是一种创建对象的设计模式，**核心目的是将对象的创建逻辑封装在工厂中，而不是直接通过 `new` 的方式在外部创建对象**。这有助于：

- 解耦对象的使用和创建
- 更易于扩展新类型
- 符合开闭原则（OCP）

## 工厂模式的分类

### 1. 简单工厂模式（Simple Factory）

通过一个工厂函数，根据传入的参数决定实例化哪种类型。

```js
function Factory(career) {
  function User(career, work) {
    this.career = career;
    this.work = work;
  }

  let work;
  switch (career) {
    case 'coder':
      work = ['写代码', '修 Bug'];
      break;
    case 'hr':
      work = ['招聘', '打杂'];
      break;
    case 'driver':
      work = ['开车'];
      break;
    case 'boss':
      work = ['投资', '管理', '决策'];
      break;
    default:
      work = ['未知'];
  }

  return new User(career, work);
}

const boss = new Factory('boss');
console.log(boss);
```

✅ **优点**：使用者无需关心具体如何创建对象，只需提供类型。

❌ **缺点**：增加新角色需要改动 `switch-case`，违背开闭原则。

---

### 2. 工厂方法模式（Factory Method）

把创建逻辑交给子类实现，更加灵活、可扩展。

```js
function Factory(career) {
  if (this instanceof Factory) {
    const obj = new this[career]();
    return obj;
  } else {
    return new Factory(career);
  }
}

Factory.prototype = {
  'coder': function() {
    this.careerName = '程序员';
    this.work = ['写代码', '修 Bug'];
  },
  'hr': function() {
    this.careerName = 'HR';
    this.work = ['招聘', '打杂'];
  },
  'driver': function() {
    this.careerName = '司机';
    this.work = ['开车'];
  },
  'boss': function() {
    this.careerName = '老板';
    this.work = ['投资', '管理', '决策'];
  }
};

const coder = new Factory('coder');
console.log(coder);
```

✅ **优点**：每种职业封装为独立构造函数，符合开闭原则。

❌ **缺点**：仍然存在硬编码构造器名的问题。

---

### 3. 抽象工厂模式（Abstract Factory）

定义抽象父类，由子类继承实现。抽象工厂更多用于**创建“产品族”对象**。

```js
let CareerAbstractFactory = function(subType, superType) {
  if (typeof CareerAbstractFactory[superType] === 'function') {
    function F() {}
    F.prototype = new CareerAbstractFactory[superType]();
    subType.prototype = new F();
    subType.prototype.constructor = subType;
  } else {
    throw new Error('未存在该抽象类型');
  }
};
```

这种方式偏向类式继承的风格，使用场景不如简单工厂和工厂方法常见。

---

## 通俗理解：为啥要用工厂模式？

> 想象你要雇一个人来工作（创建对象）——

- **不使用工厂**：你得自己一个个去找人、谈薪资、签合同（即自己 new）。
- **用了工厂**：你只需要告诉中介：“我需要一个司机”，他自动帮你找好合适的人选。

这就是**工厂帮我们屏蔽了复杂的对象创建过程**。

---

## ✅ 适用场景总结

- 创建逻辑复杂时
- 创建多个相似对象时
- 希望解耦使用者与具体类的耦合

---

# 二、单例模式（Singleton Pattern）

## 什么是单例模式？

单例模式是一种**限制类只能创建一个实例**的设计模式，确保某个类在应用中**只有一个实例对象**，并提供全局访问点。

在前端中常见于：Vuex / Redux store、模态框等全局组件。

## 单例模式的实现方式

### 1. 构造函数 + 静态方法实现

```js
function Singleton(name) {
  this.name = name;
}

Singleton.prototype.getName = function () {
  console.log(this.name);
};

Singleton.getInstance = function (name) {
  if (!this.instance) {
    this.instance = new Singleton(name);
  }
  return this.instance;
};

const a = Singleton.getInstance('张三');
const b = Singleton.getInstance('李四');

console.log(a === b); // true
```

✅ **优点**：逻辑直观，容易维护。

❌ **缺点**：污染了构造函数本身，不够封装。

---

### 2. 闭包封装实现

```js
function Singleton(name) {
  this.name = name;
}

Singleton.prototype.getName = function () {
  console.log(this.name);
};

Singleton.getInstance = (function () {
  let instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  };
})();

const a = Singleton.getInstance('张三');
const b = Singleton.getInstance('李四');

console.log(a === b); // true
```

✅ **优点**：实现更加封装、局部变量不会污染全局。

---

### 3. 应用场景实例：创建唯一登录弹窗

```js
const getSingle = function (fn) {
  let result;
  return function () {
    return result || (result = fn.apply(this, arguments));
  };
};

const createLoginLayer = function () {
  const div = document.createElement('div');
  div.innerHTML = '登录框';
  div.style.display = 'none';
  document.body.appendChild(div);
  return div;
};

const createSingleLoginLayer = getSingle(createLoginLayer);

document.getElementById('loginBtn').onclick = function () {
  const loginLayer = createSingleLoginLayer();
  loginLayer.style.display = 'block';
};
```

✅ **实际意义**：只有一个登录弹窗被创建，符合真实业务需求。

---

## 通俗理解：为啥要用单例模式？

> 就像你家只需要一个电表：

- **new 多个实例**：多个电表重复收费，混乱不堪。
- **用了单例**：系统全局只有一个入口、状态统一管理。

---

## ✅ 适用场景总结

- 模态框、弹窗等 UI 组件
- Vuex、Redux 等状态管理
- 数据缓存控制

---

# 三、策略模式（Strategy Pattern）

## 什么是策略模式？

策略模式是一种行为型设计模式，**它将一组可互换的算法封装成独立的策略类，使它们可以灵活替换使用**。使用者无需关心具体算法的实现，只需选择合适的策略即可。

> 本质：用组合代替继承，用封装隔离变化。

---

## JavaScript 中的策略模式实践

### 场景：奖金计算

#### 1. 未使用策略模式（if-else 实现）

```js
var calculateBonus = function (salary, level) {
  if (level === 'A') {
    return salary * 4;
  }
  if (level === 'B') {
    return salary * 3;
  }
  if (level === 'C') {
    return salary * 2;
  }
};

console.log(calculateBonus(4000, 'A')); // 16000
```

❌ **缺点**：耦合度高、扩展困难、难以复用。

---

#### 2. 使用策略对象解耦逻辑

```js
var strategies = {
  'A': function(salary) { return salary * 4; },
  'B': function(salary) { return salary * 3; },
  'C': function(salary) { return salary * 2; }
};

var calculateBonus = function(level, salary) {
  return strategies[level](salary);
};

console.log(calculateBonus('A', 10000)); // 40000
```

✅ **优点**：
- 每种策略封装成独立函数，扩展新策略简单；
- 符合开闭原则，便于维护；
- 使用简单灵活。

---

### 场景：表单校验（复杂验证规则）

#### 策略集合

```js
var strategy = {
  isNotEmpty: function(value, errorMsg) {
    if (value === '') return errorMsg;
  },
  minLength: function(value, length, errorMsg) {
    if (value.length < length) return errorMsg;
  },
  mobileFormat: function(value, errorMsg) {
    if (!/(^1[3|5|8][0-9]{9}$)/.test(value)) return errorMsg;
  }
};
```

#### Validator 校验器封装

```js
var Validator = function() {
  this.cache = [];
};

Validator.prototype.add = function(dom, rule, errorMsg) {
  var arr = rule.split(':');
  this.cache.push(function() {
    var type = arr.shift();
    arr.unshift(dom.value);
    arr.push(errorMsg);
    return strategy[type].apply(dom, arr);
  });
};

Validator.prototype.start = function() {
  for (var i = 0, fn; fn = this.cache[i++]; ) {
    var msg = fn();
    if (msg) return msg;
  }
};
```

#### 使用示例

```js
var validateFunc = function() {
  var validator = new Validator();
  validator.add(registerForm.userName, 'isNotEmpty', '用户名不能为空');
  validator.add(registerForm.password, 'minLength:6', '密码不能少于6位');
  validator.add(registerForm.phoneNumber, 'mobileFormat', '手机号格式不正确');

  return validator.start();
};
```

✅ **优势总结**：
- 策略模式极大提升了可扩展性；
- 各种规则解耦后更利于维护和测试；
- 支持灵活组合与复用。

---

## 通俗理解：为啥要用策略模式？

> 就像你点外卖（下单），不需要自己研究厨艺（算法实现），只需选择口味（策略）就能快速吃到饭。

策略模式就是：
- 把每种处理方式封装成一个“厨师”（策略类）
- 想换口味，换厨师就行，无需修改核心逻辑。

---

## ✅ 适用场景总结

- 存在多种算法或行为可选
- 需要根据条件动态选择算法
- 希望封装可变行为、避免 `if-else` 过多

---

# 四、观察者模式（Observer Pattern）

## 什么是观察者模式？

观察者模式是一种行为型设计模式，**定义了一种一对多的依赖关系，使得当一个对象状态发生变化时，其所有依赖者（观察者）都会自动收到通知并作出响应。**

> 本质：发布者通知订阅者，松耦合事件驱动。

---

## JavaScript 中的观察者模式实践

### 1. 角色划分

- **Subject（主题）**：被观察的对象，维护观察者列表，有添加、删除和通知的功能。
- **Observer（观察者）**：订阅主题，一旦主题发生变化，就会收到通知。

---

### 2. 实现代码

```js
class Subject {
  constructor() {
    this.observers = [];
  }

  addObserver(observer) {
    this.observers.push(observer);
  }

  removeObserver(observer) {
    this.observers = this.observers.filter(o => o !== observer);
  }

  notify(message) {
    this.observers.forEach(observer => observer.update(message));
  }
}

class Observer {
  constructor(name) {
    this.name = name;
  }

  update(message) {
    console.log(`${this.name} 收到消息: ${message}`);
  }
}

const subject = new Subject();
const observerA = new Observer('Observer A');
const observerB = new Observer('Observer B');

subject.addObserver(observerA);
subject.addObserver(observerB);

subject.notify('更新了！');
```

✅ **优点**：
- 实现了对象之间的解耦；
- 支持动态添加、删除观察者；
- 灵活响应事件变化。

❌ **缺点**：
- 观察者过多时，通知可能引发性能问题；
- 调试复杂度略高，尤其是在链式通知中。

---

## 通俗理解：为啥要用观察者模式？

> 就像你关注了一个公众号（Subject），当公众号发布新文章，你（Observer）就会收到通知。

你不用一直去刷新公众号主页（轮询），它会主动告诉你：“我有新动态了”。

这就是观察者模式的核心思想 —— **主动推送、自动更新**。

---

## ✅ 适用场景总结

- 多个对象依赖于一个对象的状态变化（如：表单联动、数据绑定）
- 实现广播机制
- 事件订阅发布模型（如 DOM 事件、Vue 响应式）

---

## 🔄 与发布订阅模式的对比（预埋理解点）

| 特性 | 观察者模式 | 发布订阅模式 |
|------|-------------|----------------|
| 是否有中介者 | ❌ 主题自己管理观察者 | ✅ 一般由事件中心中介 |
| 通知方式 | 直接调用观察者方法 | 事件中心触发所有订阅回调 |
| 场景 | 数据变化通知、UI响应 | 多模块解耦事件通知 |

---

# 五、发布订阅模式（Pub/Sub Pattern）

## 什么是发布订阅模式？

发布订阅模式是一种行为型设计模式，**通过引入事件调度中心，实现发布者与订阅者之间的松耦合通信**。

> 本质：通过“中介者”解耦发布者和订阅者，发布者不直接通知订阅者。

---

## 核心结构说明

- **Publisher（发布者）**：触发事件，发布消息
- **Subscriber（订阅者）**：注册感兴趣的事件，接收消息
- **Event Bus / PubSub（事件中心）**：统一管理订阅和发布逻辑

---

## JavaScript 实现示例

### 1. PubSub 构造函数封装

```js
class PubSub {
  constructor() {
    this.messages = {};   // 消息缓存
    this.listeners = {};  // 监听回调
  }

  publish(type, content) {
    if (!this.messages[type]) {
      this.messages[type] = [];
    }
    this.messages[type].push(content);
  }

  subscribe(type, callback) {
    if (!this.listeners[type]) {
      this.listeners[type] = [];
    }
    this.listeners[type].push(callback);
  }

  notify(type) {
    const msgs = this.messages[type] || [];
    const subs = this.listeners[type] || [];
    subs.forEach((cb, index) => cb(msgs[index]));
  }
}
```

---

### 2. 使用示例：多类型内容管理

```js
const pubsub = new PubSub();

const TYPE_MUSIC = 'music';
const TYPE_MOVIE = 'movie';

// 发布者 A
pubsub.publish(TYPE_MUSIC, 'We Are Young');
pubsub.publish(TYPE_MOVIE, 'Inception');

// 订阅者 A
pubsub.subscribe(TYPE_MUSIC, msg => {
  console.log('音乐订阅者收到：', msg);
});

// 订阅者 B
pubsub.subscribe(TYPE_MOVIE, msg => {
  console.log('电影订阅者收到：', msg);
});

// 通知所有订阅者
pubsub.notify(TYPE_MUSIC);
pubsub.notify(TYPE_MOVIE);
```

✅ **优点**：
- 发布者与订阅者完全解耦，易于扩展维护
- 支持事件广播机制，多个订阅者响应同一事件

❌ **缺点**：
- 消息中心失效可能影响整个系统
- 难以追踪消息流向，调试复杂度高

---

## 通俗理解：为啥要用发布订阅模式？

> 类比“微信群广播”：
- 群主（发布者）发言并不关心谁会回复
- 群成员（订阅者）决定自己要不要监听消息
- 群聊系统（事件中心）负责中转通知

---

## ✅ 适用场景总结

- 模块解耦通信（如组件间通信）
- 事件驱动架构（Event-driven Architecture）
- 日志系统、消息总线、聊天系统

---

## 🔄 与观察者模式对比（回顾加深）

| 特性             | 观察者模式          | 发布订阅模式        |
|------------------|----------------------|----------------------|
| 通知方式         | 被观察对象直接通知   | 中介者统一调度       |
| 是否依赖引用对象 | 是                   | 否                   |
| 是否支持广播     | 一对多                | 多对多                |
| 解耦程度         | 中等                 | 高                   |

---

# 六、代理模式（Proxy Pattern）

## 什么是代理模式？

代理模式是一种结构型设计模式，**通过引入代理对象，在不改变原始对象的情况下，控制对其访问或增强其功能**。

> 本质：为目标对象提供一个替代者，用于控制访问、缓存结果、延迟加载等。

---

## 代理模式的分类

| 类型       | 描述                           | 示例场景               |
|------------|----------------------------------|------------------------|
| 虚拟代理   | 延迟处理资源消耗较大的操作         | 图片懒加载             |
| 缓存代理   | 缓存计算结果，提高效率             | 乘法缓存               |
| 保护代理   | 控制访问权限                       | 权限控制系统           |
| 智能引用代理 | 在访问对象时增加额外操作（如统计、日志） | Vue 的响应式依赖收集等 |

---

## JavaScript 中的代理实现

### 1. 虚拟代理：延迟加载图片

```js
let myImage = (function() {
  const imgNode = document.createElement('img');
  document.body.appendChild(imgNode);
  return {
    setSrc: function(src) {
      imgNode.src = src;
    }
  }
})();

let proxyImage = (function() {
  const img = new Image();
  img.onload = function() {
    myImage.setSrc(this.src);
  };
  return {
    setSrc: function(src) {
      myImage.setSrc('https://img.zcool.cn/community/01deed576019060000018c1bd2352d.gif');
      img.src = src;
    }
  };
})();

proxyImage.setSrc('https://example.com/image.jpg');
```

✅ **说明**：
- 用户调用的是 `proxyImage`，但真实图片加载在 `img.onload` 之后才设置。
- `loading` 动图先占位，优化了体验。

---

### 2. 缓存代理：避免重复计算

```js
const mult = function(...args) {
  return args.reduce((acc, cur) => acc * cur, 1);
};

const proxyMult = (function() {
  const cache = {};
  return function(...args) {
    const key = args.join(',');
    if (cache[key]) {
      return cache[key];
    }
    return cache[key] = mult(...args);
  }
})();

console.log(proxyMult(1,2,3,4)); // 24
console.log(proxyMult(1,2,3,4)); // 24（读取缓存）
```

✅ **说明**：
- 通过闭包保存计算结果，避免重复执行逻辑。
- 常用于计算型函数、数据转换、接口缓存等。

---

## 通俗理解：为啥要用代理模式？

> 类比生活中的“助理”：
- 你找老板（目标对象）办事之前，先要通过助理（代理对象）
- 助理可以帮你过滤请求、安排时间，甚至直接回答一部分问题（缓存）

所以：
- **虚拟代理**像“前台接待”：老板忙就先给你介绍下情况
- **缓存代理**像“记性很好的助理”：问过一次的事不会重复问老板

---

## ✅ 适用场景总结

- 图片懒加载、视频延迟播放
- Ajax 数据缓存、数据转换缓存
- 权限校验、资源访问控制
- 数据绑定与监听（如 Proxy、Object.defineProperty）
