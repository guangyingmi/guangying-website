# Node

# 一、基础概念与架构理解

## Node.js 与 JavaScript 的关系

Node.js 是一个培生于 Chrome V8 JavaScript 引擎上的运行环境，目标是在网站服务器端实现无需模拟器的 JavaScript 执行。

### 基本区分
| 组件 | JavaScript (浏览器) | Node.js (服务器) |
|--------|----------------------|------------------|
| 执行环境 | Chrome V8 | V8 + C++ 带的扩展 API |
| 入出功能 | 通过 BOM/DOM | 通过 fs/net/process 等内置模块 |
| 用途 | UI 开发 | CLI/后端服务器端、脚本、工具 |

Node.js 将 JavaScript 应用到了非浏览器环境中，用于构建异步网站、CLI工具、数据处理等。

---

## Node.js 的 I/O 模型（非阻塞、事件驱动）

传统系统如 Java/二进制端口模型通常采用 **阻塞 I/O** ，即等待操作完成后继续执行下一步。Node.js 则采用了 **非阻塞 I/O** ，培生出事件驱动模型，根据 I/O 结果回调执行后续操作。

### 核心思想

- **Event Loop**：属于 libuv 库，负责分类处理事件绑定和循环执行
- **Callback 回调**：通过 callback 函数实现 I/O 操作的异步解耦
- **非阻塞**：输入/输出操作不占用主线程 CPU 资源

```js
const fs = require('fs');
fs.readFile('data.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
console.log('readFile 调用完成');
```

> 这种模型符合高应用性服务器场景，如同时处理成千上万的 HTTP 请求。

---

## Node.js 的单线程机制与多线程支持

### 为什么采用单线程？

- 避免线程间的纠结和并发系统的处理复杂度
- 培生事件驱动与异步编程模型

### 如何支持多线程？

1. **child_process**：创建子进程分工执行
2. **cluster 模块**：多进程分应 CPU 核心
3. **worker_threads**：Node >= 10.5 开始支持较为优雅的软线程模型

### worker_threads 示例

```js
const { Worker, isMainThread, parentPort } = require('worker_threads');

if (isMainThread) {
  new Worker(__filename);
  console.log('Main thread');
} else {
  console.log('Worker thread');
}
```

> 通过 worker_threads 可以解决 CPU 性计算总占用主线程问题，如大型软解码操作。

> 综上，Node.js 的特点在于强质的单线程、异步 I/O 和事件驱动模型的组合，展现在高应用性、低延迟网站服务器开发上极具优势。

---

# 二、文件系统操作（fs 模块）

Node.js 中的 `fs` (文件系统) 模块是对 POSIX API 的打包，提供了完善的文件操作能力，包括读取、写入、增量、复制和文件夹操作等。下面将对其中的核心功能进行精细解析。

## 文件读取：`readFile` 与 `readFileSync`

### `readFile`：异步非阻塞

`readFile` 采用异步非阻塞模型，适合于需要高应用性的服务器场景。它通过回调函数来处理读取结果，不会阻塞主线程。

```js
const fs = require('fs');
fs.readFile('example.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

注意：输入符集符号（如 'utf8'）如未指定，则返回 Buffer 对象。

### `readFileSync`：同步阻塞

适合 CLI 脚本或运行时必须立即获取文件内容的场景，但不适合在服务器端异步场景中使用。

```js
const fs = require('fs');
const data = fs.readFileSync('example.txt', 'utf8');
console.log(data);
```

同步操作会阻塞主线程，应该避免在 Web 服务器进程中使用。

---

## 文件写入：`writeFile` 与 `writeFileSync`

### `writeFile`

非阻塞写入指定文件，如文件存在则被覆盖，不存在则创建。

```js
fs.writeFile('output.txt', 'Hello Node.js', err => {
  if (err) throw err;
  console.log('Write complete');
});
```

### `writeFileSync`

直接执行写入，有相同的行为，但不合适在服务器中多次调用。

```js
fs.writeFileSync('output.txt', 'Sync Hello');
```

同样会覆盖文件内容，如需附加请使用 append 操作。

---

## 文件附加与复制：`appendFile`、`copyFile`

### `appendFile`

向文件尾部增加内容，保留文件现有数据，适用于日志等需求。

```js
fs.appendFile('log.txt', '\nNew log entry', err => {
  if (err) throw err;
});
```

### `copyFile`

复制文件，默认会覆盖目标文件，适合文件备份、应用协同等场景。

```js
fs.copyFile('source.txt', 'backup.txt', err => {
  if (err) throw err;
  console.log('Copy done');
});
```

也支持 sync 版本：`copyFileSync`

---

## 文件夹操作：`mkdir`、`mkdirSync`

Node.js 允许使用 `mkdir` 和 `mkdirSync` 创建文件夹，包括嵌套目录创建（需要指定 `recursive`）。

```js
// 异步创建嵌套目录
fs.mkdir('dir/subdir', { recursive: true }, err => {
  if (err) throw err;
  console.log('Directory created');
});

// 同步版
fs.mkdirSync('dir/subdir', { recursive: true });
```

如果未指定 recursive 而目录存在，则会抛出错误；如指定则不会重复建立。

---

# 三、Buffer 与编码处理

Node.js 里的 Buffer 是对原生内存块的操作层，它不依赖 V8 JavaScript 对象模型，但可以在 JavaScript 中操作。Buffer 通常用于对二进制数据进行高效处理，特别是网络传输和文件 I/O 场景。

---

## 1. Buffer 概念及内存结构

### 为什么 Node.js 要实现 Buffer？

JavaScript 本身不支持直接操作原生内存，而 Node.js 需要高效处理二进制数据，例如 TCP 协议的 socket 传输，必须直接处理 binary 流。

此外，为了提高性能，Buffer 的内存分配不通过 V8 GC，因此不会受到 V8 固有的小库约束。

### 内存结构

Buffer 本质是一段静态的内存地址值系列，内部通过 TypedArray (如 Uint8Array) 实现，使用 C++ 分配内存，自动跟随 JavaScript 对象生命周期释放。

---

## 2. 创建与操作 Buffer

### `Buffer.from()`
将字符串/数组/其他 Buffer 创建为新 Buffer

```js
const buf1 = Buffer.from('hello');         // 从字符串
const buf2 = Buffer.from([104, 101, 108]); // 从数组
const buf3 = Buffer.from(buf1);            // clone
```

### `Buffer.alloc()`
分配指定大小的空闲 Buffer，并充值

```js
const buf = Buffer.alloc(10);          // 10 个光空字节
const filled = Buffer.alloc(5, 1);     // 充值为 1
```

### `toString()`
将 Buffer 转换为字符串，可指定编码和范围

```js
const buf = Buffer.from('Node.js');
console.log(buf.toString('utf8'));       // Node.js
console.log(buf.toString('utf8', 0, 4));  // Node
```

> 如果指定编码错误，可能得到是碎片字符，引起转码错误（如中文）。

---

## 3. Buffer 与编码转换

Buffer 和编码转换是网络传输和文件读写的基础，常见的编码格式有:

- `utf8` (默认)
- `ascii` (只支持 0x00 - 0x7F)
- `base64`
- `hex`

### 示例：编码处理

```js
const buf = Buffer.from('hello');
console.log(buf.toString('ascii'));   // hello
console.log(buf.toString('base64'));  // aGVsbG8=

const b64 = Buffer.from('aGVsbG8=', 'base64');
console.log(b64.toString('utf8'));    // hello
```

> 处理非 utf8 内容时，如编码指定不对，可能出现“识别错误”。

如果进行中文处理，需确保编码精确：

```js
const buf = Buffer.from('你好', 'utf8');
console.log(buf);              // <Buffer e4 bd a0 e5 a5 bd>
console.log(buf.toString());   // 你好
```

---

# 四、Stream (流处理)

Stream 是 Node.js 中处理大数据时的一种基本模型，它允许对数据分块进行流式处理，遮盖了大部分的 I/O 场景，如文件读写、网络数据流和注册数据处理等。

Stream 特别适合处理大文件或流水类数据，无需一次性完全读入内存，实现低耗高效的数据处理模型。

---

## 1. 读取流与写入流 (readStream / writeStream)

Node.js 提供的 fs 模块支持通过 createReadStream / createWriteStream 构造文件流，无需手动分块操作，则可以分段读取和写入文件。

```js
const fs = require('fs');
const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');

readStream.on('data', chunk => {
  console.log('Reading:', chunk.length);
  writeStream.write(chunk);
});

readStream.on('end', () => {
  writeStream.end();
  console.log('Transfer complete');
});
```

> 通过流分段处理，可避免大文件一次全读导致内存爆溢。

---

## 2. 管道机制 (pipe)

为了简化输入输出流之间的组合，Node.js 提供了 stream.pipe(dest) 方法。它会自动解决各种事件绑定，及时关闭流。

```js
const fs = require('fs');
const rs = fs.createReadStream('bigfile.txt');
const ws = fs.createWriteStream('copy.txt');

rs.pipe(ws);
```

> pipe 是一种很重要的 stream API，简化了网络和文件处理中的多个流绑定操作。

---

## 3. 自定义流：Duplex 和 Transform

### Duplex 流
同时具备输入和输出功能

```js
const { Duplex } = require('stream');
const duplex = new Duplex({
  write(chunk, encoding, callback) {
    console.log('write:', chunk.toString());
    callback();
  },
  read(size) {
    this.push(null); // 没有输出
  }
});

duplex.write('hello duplex');
```

### Transform 流
培生输入 → 转换 → 输出 流结构，适合数据编码/解码、清洗、缩解缩等

```js
const { Transform } = require('stream');
const upperCase = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});

process.stdin.pipe(upperCase).pipe(process.stdout);
```

> 自定义流是构建数据处理 pipeline 的基础环节

---

## 4. 实战：静态文件读取 & 复制优化

### 错误的简单版：一次性读入

```js
const fs = require('fs');
const data = fs.readFileSync('large.txt');
fs.writeFileSync('copy.txt', data);
```

> 这种方式将所有数据全部读入内存，对于大文件非常不符合进程符合性

### 推荐方案：使用 pipe

```js
const fs = require('fs');
fs.createReadStream('large.txt').pipe(fs.createWriteStream('copy.txt'));
```

> 这种方式不会缓存所有数据，更加符合性能和内存控制要求

---

# 五、事件机制（EventEmitter）

在 Node.js 中，大量内核模块都基于事件驱动模型，例如 HTTP 服务器、fs 模块、stream 模型等都通过 EventEmitter 实现事件管理。

EventEmitter 是一种结构清晰、解耦良好的设计模式：通过 **on 注册监听、emit 触发事件**，实现发布订阅模式。

---

## 1. 基本使用：on、emit、removeListener、once

```js
const EventEmitter = require('events');
const myEmitter = new EventEmitter();

myEmitter.on('event', () => {
  console.log('An event occurred!');
});

myEmitter.emit('event');
```

### `.on(event, callback)`
用于绑定事件监听器。每次触发该事件，所有绑定的回调都会被依次执行。

### `.emit(event, [...args])`
触发某个事件，可以携带参数。

### `.once(event, callback)`
只监听一次事件，执行后自动移除监听器。

### `.removeListener(event, callback)` / `.off(event, callback)`
移除某个事件上的某个回调函数监听器。两者功能等价，`.off()` 是 `.removeListener()` 的别名（Node 10+）。

> 注意：只有绑定的具名函数可以成功移除，匿名函数无法通过 `.off()` 移除。

---

## 2. 自定义事件类封装

在实际业务中，我们通常会封装一个事件中心统一管理模块之间通信。

```js
const EventEmitter = require('events');

class Bus extends EventEmitter {
  constructor() {
    super();
  }
}

const bus = new Bus();

bus.on('login', user => {
  console.log('User login:', user);
});

bus.emit('login', { name: 'Alice' });
```

通过继承 EventEmitter，我们可以快速创建一个解耦的事件中心，替代冗长的参数传递或 callback 链条。

---

## 3. prependListener、off 与底层实现机制

### `.prependListener(event, callback)`
将回调添加到监听器数组的头部，使其比后续的回调更早执行。

```js
emitter.on('foo', () => console.log('B'));
emitter.prependListener('foo', () => console.log('A'));
emitter.emit('foo'); // 输出 A B
```

### `.off()`
Node 10+ 引入，语义更清晰，内部仍调用 `removeListener()`。

### EventEmitter 内部原理

事件监听器实际上存储在对象的一个属性 `_events` 上，该属性是一个 Map 或 Object。

```js
{
  eventName1: [callback1, callback2],
  eventName2: callback3,
}
```

当 `.emit()` 被调用时，EventEmitter 会：
1. 从 `_events` 中获取对应事件的监听器数组；
2. 遍历调用，使用 `Reflect.apply(callback, this, args)` 执行函数；
3. 如果是 `.once()` 注册的函数，则标记为已调用，并自动 `off()` 移除。

> EventEmitter 的执行顺序为同步执行，事件触发与监听之间没有异步队列，因此 emit 是同步阻塞的。

---

## 4. 实战：组合使用 once + on + off

```js
const EventEmitter = require('events');
const ee = new EventEmitter();

const sayHello = name => console.log('Hello, ' + name);
const sayBye = name => console.log('Bye, ' + name);

ee.once('wake', sayHello);
ee.on('wake', sayBye);

ee.emit('wake', 'Alice');
ee.emit('wake', 'Bob');

ee.off('wake', sayBye);
ee.emit('wake', 'Charlie');
```

通过 once/on/off 的组合，可以实现非常灵活的事件生命周期控制，在异步编程、状态流转、资源监听等方面都具备良好适配性。

---

# 六、模块化机制（CommonJS）

Node.js 在设计之初便采用了 CommonJS 模块规范，目的是为服务端 JavaScript 提供明确的模块边界、作用域隔离以及缓存能力。CommonJS 模块通过 `require` 导入、`exports`/`module.exports` 导出，是 Node.js 最常用的模块组织机制。

---

## 1. require、exports 与 module.exports

### `require(path)`
用于加载一个模块文件，返回其导出对象。

```js
const math = require('./math');
console.log(math.add(1, 2));
```

### `exports` 与 `module.exports`

- `module.exports` 是最终导出的对象，Node.js 会返回它给 `require()` 的调用方。
- `exports` 只是 `module.exports` 的一个引用（即：`exports = module.exports`）。

#### 正确导出
```js
// math.js
exports.add = (a, b) => a + b;
```
或者：
```js
// math.js
module.exports = {
  add(a, b) { return a + b; }
};
```

#### 错误示例
```js
exports = { add() {} }; // ❌ 无效，exports 被覆盖引用，与 module.exports 无关
```

> 如果需要导出的是一个函数或类，而不是对象属性集合，必须使用 `module.exports = ...`。

---

## 2. 模块查找规则

Node.js 查找模块遵循以下流程：

### 路径优先

- `./module` 或 `../module`：按相对路径查找本地文件
- `/path/to/module`：绝对路径
- `module`：查找 node_modules

### 文件扩展名

- 按顺序尝试：`.js` → `.json` → `.node`
- 若为目录：
  1. 查找该目录下的 `package.json` 中 `main` 字段
  2. 否则查找 `index.js`

### node_modules 查找逻辑

- 从当前模块目录开始向上查找 `node_modules`
- 查找层级目录结构如下：
  ```
  /project/src/moduleA.js
  /project/node_modules/
  /node_modules/
  ```
  会尝试：`/project/src/node_modules` → `/project/node_modules` → `/node_modules`

### 模块缓存机制

模块首次加载后会缓存（以绝对路径为 key），后续 `require()` 会复用缓存结果，避免重复加载。

```js
require.cache // 查看缓存模块对象
```

> 若需清除缓存：
```js
delete require.cache[require.resolve('./module')];
```

---

## 3. __filename、__dirname、global 全局变量

Node.js 在每个模块作用域注入了以下特殊变量：

### `__filename`
当前模块的完整文件路径

```js
console.log(__filename); // /Users/user/project/index.js
```

### `__dirname`
当前模块所在目录的绝对路径

```js
console.log(__dirname); // /Users/user/project
```

### `global`
Node.js 的全局作用域对象（类似浏览器中的 `window`）

- `global.console`
- `global.setTimeout`
- `global.process`

这些对象在任何模块中都可直接使用，不需要 require。

```js
global.foo = 123;
console.log(foo); // 123
```

> 注意：不要将项目级变量污染进 global，除非非常必要，否则会破坏模块隔离性。

---

# 七、进程与系统交互

Node.js 本质是一个基于事件驱动的单线程程序，但其提供了强大的系统层交互接口，通过 `process` 模块与底层 OS 紧密结合。无论是命令行参数读取、环境变量识别，还是内存监控与性能优化，这些能力都是构建服务端应用不可或缺的部分。

---

## 1. process 模块：argv、env、cwd 等

`process` 是一个全局对象，直接可用，封装了 Node.js 进程的信息与控制能力。

### `process.argv`

获取命令行参数，返回数组：
- `argv[0]`: node 可执行文件路径
- `argv[1]`: 当前执行脚本路径
- `argv[2+]`: 用户自定义参数

```js
// node script.js hello world
console.log(process.argv.slice(2)); // ['hello', 'world']
```

### `process.env`

获取环境变量：
```js
console.log(process.env.NODE_ENV); // 'development' / 'production'
```

也可设置变量供业务判断，如：
```bash
NODE_ENV=production node app.js
```

### `process.cwd()`

获取当前 Node.js 进程的工作目录：
```js
console.log(process.cwd()); // 当前运行路径
```

与 `__dirname` 不同的是，`cwd()` 返回的是运行时路径，而 `__dirname` 是当前模块的物理路径。

---

## 2. setTimeout、setImmediate、process.nextTick 区别

### `setTimeout(fn, 0)`
在下一轮事件循环“定时器阶段”执行。

### `setImmediate(fn)`
在当前 I/O 循环完成后立即执行（位于“check 阶段”）。

### `process.nextTick(fn)`
**在当前操作完成后、事件循环继续前**立即执行，优先级最高。

```js
process.nextTick(() => console.log('nextTick'));
setTimeout(() => console.log('timeout'), 0);
setImmediate(() => console.log('immediate'));
```

输出结果（通常）：
```txt
nextTick
immediate
timeout
```

> ⚠️ 注意：`nextTick` 优先级高，如果递归使用过多会导致阻塞事件循环（即“饿死其他回调”）。

总结优先级：`nextTick > Promise.then > setImmediate > setTimeout`

---

## 3. 内存与性能监控：process.memoryUsage()

Node.js 提供了 `process.memoryUsage()` 获取当前进程内存使用状况，返回单位为字节（Byte）。

```js
const mem = process.memoryUsage();
console.log((mem.heapUsed / 1024 / 1024).toFixed(2) + ' MB');
```

常见字段：
- `rss`：常驻内存集（包括堆、栈、绑定内存等）
- `heapTotal`：V8 分配的堆总大小
- `heapUsed`：实际使用的堆内存大小
- `external`：C++ 对象绑定的外部内存

### 使用 Easy-Monitor 可视化

`easy-monitor` 是一款开箱即用的 Node.js 性能分析工具。

```js
const easyMonitor = require('easy-monitor');
easyMonitor('MyNodeApp');
```

访问 `http://localhost:12333` 即可查看内存占用、GC 状况、事件循环延迟等可视化数据。

---

# 八、Koa 框架基础

Koa 是由 Express 团队原班人马打造的下一代 Node.js Web 框架，其核心设计理念是极简与中间件机制解耦。Koa 不再内置任何路由、模板或 body 解析功能，而是通过中间件组合实现完整功能，构建一个极致可控的 HTTP 服务端环境。

---

## 1. Koa 中间件机制（洋葱模型）

Koa 的中间件本质上是一个 async 函数组成的有序栈，调用顺序遵循“先执行前置逻辑，再执行后续中间件，最后回溯执行后置逻辑”，形象地被称为“洋葱模型”。

```js
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx, next) => {
  console.log('middleware 1: before');
  await next();
  console.log('middleware 1: after');
});

app.use(async (ctx, next) => {
  console.log('middleware 2');
  ctx.body = 'Hello Koa';
});

app.listen(3000);
```

输出顺序：
```
middleware 1: before
middleware 2
middleware 1: after
```

> 这个机制允许在前后环节做前置检查（如权限、参数校验）和后置收尾（如日志、响应处理），极其适合“切面式”业务需求。

---

## 2. 自定义 bodyparser 与 static 中间件

### bodyparser 中间件
Koa 默认不解析 POST 请求体，需自定义或引入中间件实现：

```js
const querystring = require('querystring');

function bodyParser() {
  return async (ctx, next) => {
    await new Promise((resolve, reject) => {
      let data = '';
      ctx.req.on('data', chunk => data += chunk);
      ctx.req.on('end', () => {
        const contentType = ctx.get('content-type');
        if (contentType.includes('application/x-www-form-urlencoded')) {
          ctx.request.body = querystring.parse(data);
        } else if (contentType.includes('application/json')) {
          ctx.request.body = JSON.parse(data);
        }
        resolve();
      });
    });
    await next();
  };
}
```

### static 中间件
静态资源服务：

```js
const fs = require('fs');
const path = require('path');
const mime = require('mime');

function staticMiddleware(rootPath) {
  return async (ctx, next) => {
    const realPath = path.join(rootPath, ctx.path);
    if (fs.existsSync(realPath) && fs.statSync(realPath).isFile()) {
      ctx.type = mime.getType(realPath);
      ctx.body = fs.createReadStream(realPath);
    } else {
      await next();
    }
  };
}
```

---

## 3. 实现日志记录中间件

```js
const fs = require('fs');

function logger() {
  return async (ctx, next) => {
    const start = Date.now();
    await next();
    const duration = Date.now() - start;
    const log = `${ctx.method} ${ctx.url} - ${duration}ms`;
    fs.appendFileSync('./access.log', log + '\n');
  };
}
```

> 通过中间件统一记录每一次请求的性能信息，有助于分析慢请求、排查问题。

---

## 4. 文件上传中间件（koa-body、koa-multer）

### 使用 koa-body

```bash
npm install koa-body
```

```js
const koaBody = require('koa-body');
app.use(koaBody({ multipart: true }));

app.use(async ctx => {
  const file = ctx.request.files.file; // 获取上传文件对象
  ctx.body = { filename: file.name };
});
```

### 使用 koa-multer

```bash
npm install koa-multer
```

```js
const multer = require('@koa/multer');
const upload = multer({ dest: 'uploads/' });

router.post('/upload', upload.single('file'), ctx => {
  ctx.body = {
    filename: ctx.file.originalname,
    path: ctx.file.path
  };
});
```

> koa-body 更轻量适合简单上传，koa-multer 适合复杂字段控制和多文件上传。

---

# 九、JWT 权限认证

JWT（JSON Web Token）是一种常用的前后端分离身份认证机制，通过签发加密 Token 来实现用户登录状态的无状态保持。相比传统的 session 机制，JWT 更适合微服务与 API 架构。

---

## 1. Token 的构成：Header、Payload、Signature

一个标准的 JWT 由三部分组成，用点号 `.` 分隔：
```
Header.Payload.Signature
```

### Header（头部）
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
声明签名算法类型（如 HS256）和 token 类型。

### Payload（负载）
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```
负载中存储的是不加密但经过 Base64 编码的用户信息，一般包括：
- `sub`：用户唯一 ID
- `iat`：签发时间
- 可扩展：如 `exp`（过期时间）、`role`（角色）等

### Signature（签名）
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```
用于验证 token 是否被篡改。服务端保存 secret 用于验证。

---

## 2. 使用 jsonwebtoken 生成与验证

安装依赖：
```bash
npm install jsonwebtoken
```

### 签发 token
```js
const jwt = require('jsonwebtoken');

const token = jwt.sign(
  { id: user.id, name: user.name },
  'my_secret_key',
  { expiresIn: '1h' }
);
```

### 验证 token
```js
try {
  const decoded = jwt.verify(token, 'my_secret_key');
  console.log(decoded); // payload
} catch (err) {
  console.log('Invalid token');
}
```

> 注意：Payload 是可读的，仅 Signature 部分是加密的，因此不要在 Payload 中放敏感数据。

---

## 3. 登录生成 token，验证 token 示例

```js
const jwt = require('jsonwebtoken');
const crypto = require('crypto');

const userList = [{ name: 'admin', password: crypto.createHash('md5').update('123456').digest('hex') }];

function login(ctx) {
  const { name, password } = ctx.request.body;
  const hash = crypto.createHash('md5').update(password).digest('hex');
  const user = userList.find(u => u.name === name && u.password === hash);

  if (user) {
    const token = jwt.sign({ name: user.name }, 'secret_key', { expiresIn: '2h' });
    ctx.body = { token };
  } else {
    ctx.status = 401;
    ctx.body = { error: 'Invalid credentials' };
  }
}
```

---

## 4. koa-jwt 的使用与配置

koa-jwt 是对 jsonwebtoken 的封装，自动解析 token 并将验证后的用户信息挂载到 ctx.state.user 上。

### 安装依赖：
```bash
npm install koa-jwt
```

### 应用中间件：
```js
const jwt = require('jsonwebtoken');
const jwtMiddleware = require('koa-jwt');

app.use(jwtMiddleware({ secret: 'secret_key' }).unless({
  path: [/^\/public/, /\/api\/login/] // 排除登录与公共接口
}));

router.get('/api/user', ctx => {
  const user = ctx.state.user;
  ctx.body = { user };
});
```

### 客户端携带 token 示例（推荐使用 Bearer 模式）：
```js
axios.get('/api/user', {
  headers: {
    Authorization: 'Bearer ' + localStorage.getItem('token')
  }
});
```

---

# 十、数据库分页与查询（MySQL 示例）

在 Web 应用中，大数据列表的展示必须采用分页策略，以避免一次性加载所有数据导致性能下降与用户体验问题。Node.js 与 MySQL 的结合中，分页通常依赖 SQL 的 `LIMIT` 与 `OFFSET` 子句实现。

---

## 1. SQL 分页逻辑：LIMIT + OFFSET

### 基本语法：
```sql
SELECT * FROM table_name LIMIT {limit} OFFSET {offset};
```

- `LIMIT`：每页返回的数据条数；
- `OFFSET`：跳过多少条记录开始返回。

例如：获取第 3 页，每页 10 条：
```sql
SELECT * FROM users LIMIT 10 OFFSET 20;
```

或者简写为：
```sql
SELECT * FROM users LIMIT 20, 10;
```

> OFFSET = (page - 1) * pageSize

### 注意事项：
- 对大表分页时必须配合索引使用，避免全表扫描；
- 对于精准分页或滚动分页，可以采用 `WHERE id > ?` 方式优化性能。

---

## 2. 查询总条数：SELECT COUNT(*)

为了前端进行分页 UI 控制（页码数、总记录数），还需查询总记录数：

```sql
SELECT COUNT(*) as total FROM users WHERE status = 'active';
```

该语句应与主查询保持相同的 `WHERE` 条件。

---

## 3. 实现分页接口与逻辑处理

以 Koa + mysql2 示例：

### 安装 mysql2
```bash
npm install mysql2
```

### 创建连接池
```js
const mysql = require('mysql2/promise');
const pool = mysql.createPool({
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'mydb',
});
```

### 分页接口路由
```js
router.get('/users', async ctx => {
  const page = parseInt(ctx.query.page) || 1;
  const pageSize = parseInt(ctx.query.pageSize) || 10;
  const offset = (page - 1) * pageSize;

  const [data] = await pool.execute(
    'SELECT * FROM users LIMIT ? OFFSET ?',
    [pageSize, offset]
  );

  const [[{ total }]] = await pool.execute(
    'SELECT COUNT(*) as total FROM users'
  );

  ctx.body = {
    page,
    pageSize,
    total,
    data,
  };
});
```

### 返回结果结构
```json
{
  "page": 1,
  "pageSize": 10,
  "total": 125,
  "data": [
    { "id": 1, "name": "Alice" },
    { "id": 2, "name": "Bob" }
  ]
}
```






