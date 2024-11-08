
<style>

    .success {
        padding:5px;
        display:inline;
        color:#1B5E20;
        background-color:#C8E6C9;
    }
    .warning {
        padding:5px;
        display:inline;
        color:#E65100;
        background-color:#FFE0B2;
        width:100%;
    }
    .danger {
        padding:5px;
        display:inline;
        color:#B71C1C;
        background-color:#FFCDD2;
    }
    .info {
        padding:5px;
        display:inline;
        color:#006064;
        background-color:#B2EBF2;
    }
    .doubt {
        padding:5px;
        display:inline;
        color:#AAA;
        background-color:#DDDDDD;
    }
    .asso {
        padding:5px;
        display:inline;
        color:#555;
        background-color:#FFCC00;        
    }
    
    .alert {
        display:inline-block;
        width:100%;
        padding:5px;
        line-height:30px;
        margin-top:10px;
    }
</style>


# Learn-ECMAScript

## 常见的编程范式
#### 命令式编程 (Imperative Programming)
通过一系列改变状态的命令来描述程序的流程。例如，使用循环结构（for, while）和条件语句（if, switch）来控制程序的执行流程。
- 适用场景：需要精确控制程序执行流程的场景，适合处理流程明确、步骤固定的任务，如数据处理脚本、简单的算法实现。命令式编程易于理解和实现，适用于初学者或快速原型开发。
```javascript
let sum = 0;
for (let i = 1; i <= 10; i++) {
    sum += i;
}
```


#### 函数式编程 (Functional Programming, FP)
使用纯函数和不可变的数据。函数式编程强调函数的使用，避免改变状态和可变数据。函数被视为一等公民，可以被赋值给变量，作为参数传递给其他函数，甚至作为其他函数的返回值。高阶函数（如 map, reduce, filter）和纯函数是函数式编程的核心概念。
- 适用场景：函数式编程减少了副作用和状态管理，提高了代码的可预测性和可测试性。适用于数据处理、数据密集型计算、不可变状态管理、并发编程等场景。特别适合处理集合数据和需要高可测试性的代码。
```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(num => num * 2);
```

#### 面向对象编程 (Object-Oriented Programming, OOP)
通过类和对象来组织代码，强调封装、继承、多态等概念。在 JavaScript 中，你可以使用构造函数、原型链或 ES6 的 class 关键字来实现类和对象。对象可以包含数据（属性）和行为（方法）。JavaScript 的对象模型基于原型继承，这与基于类的传统 OOP 稍有不同。
- 适用场景：适合解决复杂问题，特别是那些需要模拟现实世界实体和它们之间关系的问题。OOP 通过封装、继承和多态提供了更好的代码复用性和模块化
```javascript
class Person {
    constructor(name) {
    this.name = name;
    }

    greet() {
    console.log(`Hello, my name is ${this.name}`);
    }
}

const alice = new Person('Alice');
alice.greet();
```

#### 事件驱动编程 (Event-Driven Programming)
程序的流程由事件的发生来控制，例如用户输入、点击按钮等。
- 适用场景：适用于需要响应外部事件的应用，如 GUI 应用、Web 应用、网络服务器等。事件驱动编程可以高效处理异步操作和非阻塞 I/O，特别适合处理用户交互和系统事件、实时系统、异步编程等场景。
```javascript
document.getElementById('myButton').addEventListener('click', () => {
    console.log('Button clicked!');
});
```

#### 声明式编程（Declarative Programming）
通过描述结果的方式编写代码，而不是逐步描述如何实现结果。描述你想要什么结果，而不是如何得到这个结果。React 组件就是一个例子，它们描述了 UI 的状态应该是什么样子，而不是如何去更新 DOM。React中的JSX语法就是一个声明式编程的例子。
- 适用场景：适合于定义数据流和变换，如配置文件、数据库查询语言（SQL）、HTML 和 CSS。在前端开发中，React 的组件就是声明式编程的体现，它专注于描述状态和视图，而不是更新过程。适用于UI开发、配置管理、数据查询等场景。特别适合需要简洁表达意图的场合。
```javascript
   const App = () => (
     <div className="App">
       <header className="App-header">
         Hello World
       </header>
     </div>
   );
```

## 知识点

### 赋值 assignment
- 对象的计算属性名，将计算结果当作对象的属性名称。将计算结果放到 **[]** 中求值。

```javascript
const a = {}
let i = 2
//对象的属性支持计算
a[0+i]='cde' // a = {'2':cde}

// 对象的字面量属性也支持计算
a={
    [i+2]:i
}
// a= {'4':2}

```


### 异步编程
当然，我可以详细解释一下“当前的async函数会暂停执行，允许其他任务继续运行”这句话的意思，并通过一个具体的例子来说明。概念解释在Node.js中，async函数内部的await表达式会暂停当前函数的执行，直到等待的Promise完成。这意味着在await表达式暂停期间，Node.js的事件循环不会被阻塞，而是可以继续处理其他任务。这种机制使得异步操作可以高效地进行，而不会导致程序卡顿或阻塞。举例说明假设我们有一个简单的Node.js应用程序，其中包括两个异步操作：从网络获取数据和写入文件。我们将使用async/await来实现这两个操作，并观察它们是如何并发执行的。
```js
// 模拟网络请求的异步函数
function fetchData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(`数据来自 ${url}`);
    }, 2000); // 模拟2秒的网络延迟
  });
}

// 模拟写入文件的异步函数
function writeFile(data) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(`文件已写入: ${data}`);
      resolve();
    }, 1000); // 模拟1秒的文件写入时间
  });
}

// 定义一个异步函数来处理这两个操作
async function processTasks() {
  console.log('开始处理任务');

  // 任务1：从网络获取数据
  const data = await fetchData('https://api.example.com/data');
  console.log('数据获取完成:', data);

  // 任务2：写入文件
  await writeFile(data);
  console.log('文件写入完成');
}

// 调用异步函数
processTasks();

// 在主程序中模拟其他任务
console.log('主程序继续运行');
setTimeout(() => {
  console.log('其他任务1完成');
}, 500);

setTimeout(() => {
  console.log('其他任务2完成');
}, 1500);
```
输出结果运行上述代码，你将看到以下输出顺序：开始处理任务
主程序继续运行
其他任务1完成
数据获取完成: 数据来自 https://api.example.com/data
其他任务2完成
文件写入完成

**解释:**
1. 开始处理任务：首先，processTasks函数被调用，输出“开始处理任务”。
2. 主程序继续运行：由于await fetchData('https://api.example.com/data')会暂停processTasks函数的执行，但不会阻塞事件循环，因此主程序中的console.log('主程序继续运行')会立即执行。
3. 其他任务1完成：设置的500毫秒后，输出“其他任务1完成”。
4. 数据获取完成：2秒后，fetchData的Promise完成，processTasks函数继续执行，输出“数据获取完成: 数据来自 https://api.example.com/data”。
5. 其他任务2完成：1.5秒后，设置的1500毫秒超时完成，输出“其他任务2完成”。
6. 文件写入完成：1秒后，writeFile的Promise完成，processTasks函数继续执行，输出“文件写入完成”。

**关键点:**
•暂停执行：在await fetchData('https://api.example.com/data')处，processTasks函数会暂停执行，等待fetchData的Promise完成。在这段时间内，Node.js的事件循环可以继续处理其他任务。
•并发执行：由于await表达式不会阻塞事件循环，其他任务（如定时器回调）可以继续运行，从而实现并发执行。
通过这种方式，async/await使得异步代码更加简洁和高效，同时保持了良好的并发性能。希望这个解释和示例能帮助你更好地理解“当前的async函数会暂停执行，允许其他任务继续运行”的含义。如果有更多问题，欢迎继续提问。内容由AI生成
已记录


--------
[code snippet](./code-snippet.md)
[code utils](./code-unitls/utils.md)


----
<span class="success">
    test asdfds adasf dfas 
</span>

<span class="alert danger">
    test asdfds adasf dfas 
</span>

<span class="alert info">
    test asdfds adasf dfas 
</span>


<span class="alert success">
    test asdfds adasf dfas 
</span>

<div class="alert warning">python不区分单精度和双精度浮点
数，默认双精度，int也不细分short,long整型)
</div>

<div class="alert asso">python不区分单精度和双精度浮点
数，默认双精度，int也不细分short,long整型)
</div>

<div class="alert doubt">python不区分单精度和双精度浮点
数，默认双精度，int也不细分short,long整型)
</div>