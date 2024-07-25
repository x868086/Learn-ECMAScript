
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