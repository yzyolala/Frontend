1.解释 JS 的 event loop / 事件循环；call stack 和 task queue 有什么区别？
Keywords: event loop, call stack, task queue, microtask vs macrotask

| 概念                  | 作用           | 内容                         | 优先级      |
| ------------------- | ------------ | -------------------------- | -------- |
| **Call Stack**      | 执行同步代码       | 正在运行的函数                    | 最高（直接执行） |
| **Microtask Queue** | Promise 等微任务 | `then` 回调、`queueMicrotask` | 高        |
| **Macrotask Queue** | 定时器等宏任务      | `setTimeout`、I/O           | 低        |

5️⃣ 经典示例题（高级前端必问）
console.log(1);

setTimeout(() => console.log(2));

Promise.resolve().then(() => console.log(3));

console.log(4);


执行顺序：

1
4
3   ← Microtask
2   ← Macrotask


解释：

1、4 同步任务 → 立即执行

Promise.then → 微任务

setTimeout → 宏任务

微任务优先 → 输出 3

再执行一个宏任务 → 输出 2


2.什么是闭包（closure），典型使用场景和常见坑？
Keywords: closure, lexical scope, memory leak

最直白的一句话：

闭包让一个函数可以记住它创建时的外部变量，即使外部函数已经执行完。

再直一点：

闭包就是让变量继续活着，不被销毁。

看这段代码：
function outer() {
  let a = 10;

  function inner() {
    console.log(a);
  }

  return inner;
}

const fn = outer();
fn(); // 10


执行流程真实情况是：

调用 outer()，创建变量 a = 10

定义 inner()，它引用了 a

outer() 执行完了，本来 outer() 里的变量应该销毁

但 JS 发现：inner() 还在使用 a

所以 JS 不会把 a 清掉，必须保留
→ 这就是闭包形成的原因

重点：

inner() 记住了它出生时能访问的变量

outer() 结束，但变量不会被销毁

因为 inner() 还在引用它

这就叫 Closure。



3.this 在不同场景下怎么绑定？（普通函数 / 箭头函数 / call/apply/bind / class method）
Keywords: this binding, arrow function, implicit/explicit binding

核心心法：你是“出生决定论”还是“际遇决定论”？
在判断 this 之前，先看函数类型：

箭头函数：是出生决定论（Lexical Scoping，词法作用域）。它一旦定义，this 就定死了，由它定义时外层的环境决定。

普通函数：是际遇决定论（Dynamic Scoping，动态作用域）。它的 this 是变的，完全取决于调用那一刻是谁在调用它（Call Site）。

优先级金字塔 (从高到低)
如果在同一个调用位置出现多种规则，按此顺序判定：

1. 特权阶级：箭头函数 (Arrow Function)
规则： 根本不看调用方式，无视 new、bind、call。它没有自己的 this，直接“继承”外层作用域的 this。

面试金句： "Arrow functions don't have their own this; they treat this as a variable captured from the enclosing lexical scope."

2. 皇权绑定：new 关键字
规则： new Constructor()

结果： this 指向新创建的那个实例对象。

注意： 这是普通函数能获得的最高优先级。

3. 显式绑定：call / apply / bind
规则： fn.call(obj), fn.apply(obj), fn.bind(obj)

结果： this 强行指向你传入的 obj。

特例： 如果传入 null 或 undefined，在非严格模式下会指向全局对象（window/global），严格模式下就是 null/undefined。

4. 隐式绑定：对象调用 (Method Invocation)
规则： obj.method()

结果： this 指向“点”前面的那个对象 (obj)。

陷阱： 隐式丢失 (Implicit Loss) —— 如果把 obj.method 赋值给一个变量 let fn = obj.method，再执行 fn()，这就变成了默认绑定。

5. 默认绑定：独立调用 (Function Invocation)
规则： fn() —— 前面没有任何修饰。

结果：

非严格模式：指向全局对象 (Window / Global)。

严格模式 ('use strict')：指向 undefined (现代框架如 React 默认都是严格模式)。


4.var / let / const 区别是什么？涉及到 hoisting 吗？
Keywords: hoisting, temporal dead zone, function vs block scope

var / let / const 哪个最好用？
工程最佳实践：

默认用 const

需要修改才用 let

永远不要使用 var

为什么不用 var？

容易变量提升导致 bug

容易 this / 闭包问题

容易声明污染（重复声明）

let 和 const 明确作用域，更安全

一句话记住全部区别：

var 会提升并初始化为 undefined，只认函数作用域；
let/const 会提升但进入 TDZ，认块作用域；
const 不能重新赋值。
总结一句话

var 只能被 “函数作用域 function” 套住
→ {} 套不住
→ if 套不住
→ for 套不住

let / const 只要遇到 “{}” 就能被套住（块级作用域 block）
→ if 能套住
→ for 能套住
→ while 能套住
→ 普通 {} 也能套住
→ function 当然也能套住


5.解释原型链（prototype chain），__proto__ 和 prototype 有什么区别？
Keywords: prototype chain, inheritance, Object.create

| 名字            | 属于谁？   | 代表什么？                  |
| ------------- | ------ | ---------------------- |
| **prototype** | 函数独有   | 作为“模板”，用于创建实例的原型       |
| ****proto**** | 所有对象都有 | 这个对象实际继承自哪个原型（指向它的上一层） |

✔ prototype 示例（函数的模板）
function Person() {}
Person.prototype.sayHi = function () { console.log("hi"); };


这个 prototype 意味着：

“未来所有由 new Person() 创建的对象都会继承这个 sayHi 方法”

✔ proto 示例（对象指向它的原型）
const p = new Person();
p.__proto__ === Person.prototype;  // true


这是整个原型机制的核心。

function Person()  ------ prototype ----->  Person.prototype
        |                                       ↑
        |                                       |
       new                                      |
        ↓                                       |
   p (实例) -------- __proto__ ------------------

Object.create 是什么？（必考）

Object.create(proto) 创建一个“以某个对象为原型”的新对象。

const parent = { a: 1 };
const child = Object.create(parent);

child.a; // 1 → 来自 parent
child.__proto__ === parent; // true

终极总结（面试直接背）

prototype 是函数的模板，决定实例能继承什么。
proto 是对象的隐藏属性，指向它继承自的原型。
原型链是通过不断沿着 proto 查找属性形成的链。
Object.create 能手动指定对象的原型。


6. == 和 === 的区别？什么时候可能会踩坑？
Keywords: coercion, strict equality, abstract equality

| 运算符   | 名称                          | 会不会做类型转换？            | 比较规则     |
| ----- | --------------------------- | -------------------- | -------- |
| `==`  | **宽松相等（abstract equality）** | ✔ 会做类型强制转换（coercion） | 转换后再比较   |
| `===` | **严格相等（strict equality）**   | ❌ 不会做类型转换            | 先比类型，再比值 |

永远不要用 ==，除非你非常确定行为
工作中推荐使用 ===，除非你想同时判断 null 和 undefined

7.Promise 的状态流转；async/await 底层等价于什么？
Keywords: Promise states, thenable, error handling, async/await

Array.prototype.map / filter / reduce / forEach 区别和常用场景？
Keywords: immutability, higher-order functions

节流（throttle）和防抖（debounce）的区别和实现思路？
Keywords: throttle vs debounce, scroll/resize, lodash

浅拷贝 vs 深拷贝，有哪些常见实现方式？
Keywords: spread operator, Object.assign, structuredClone, JSON.parse(JSON.stringify)

Explain the difference between CommonJS and ES Modules.
Keywords: require vs import, static analysis, tree-shaking

浏览器渲染流程大致是什么？重排（reflow）和重绘（repaint）？
Keywords: layout, paint, compositing, performance
