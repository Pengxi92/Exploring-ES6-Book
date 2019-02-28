
# 第四章链接： http://exploringjs.com/es6/ch_core-features.html#ch_core-features

# 4. 核心ES6特性

本章描述了ES6的核心特性。这些特性是日常采用的特性;剩下的特性主要是学术作者们（这里的原文是 library authors，感觉翻译的不太合理）感兴趣的。我将通过相应的ES5代码解释每一个特性。

## 4.1  从 var 到 let/const

在ES5中，通过var声明变量。这些变量是函数作用域的，它们的作用域是最内层封闭的函数。var的行为有时令人困惑。举个ES5的例子:

``` html
var x = 3;
function func(randomize) {
    if (randomize) {
        var x = Math.random(); // (A) scope: whole function
        return x;
    }
    return x; // accesses the x from line A
}
func(false); // undefined

```

func() 返回 undefined ，这可能会比较奇怪。如果重写一下这段代码，让其更清楚地展现出来实际上发生了什么，就明白了：

``` html
var x = 3;
function func(randomize) {
    var x;
    if (randomize) {
        x = Math.random();
        return x;
    }
    return x;
}
func(false); // undefined
```

在 ES6 中还可以使用 let 和 const 来申明变量。这类变量是块级作用域的，它们的作用域是包含它们最近的块。let 可以理解为块级作用域中的 var，const 与 let 类似，只是用 const 申明的变量其值是不可修改的。

let 和 const 更为严格，会抛出更多异常(比如，在变量作用域内访问还没有申明的变量)。块级作用域有助于保持代码片段的作用更局限(参考下一节)。相比函数级作用域来说，块级作用域更为主流，它使 JavaScript 更接近于其它编程语言。

如果把最开始那个示例中的 var 替换为 let，你会发现结果发生了变化：

```html
let x = 3;
function func(randomize) {
    if (randomize) {
        let x = Math.random();
        return x;
    }
    return x;
}
func(false); // 3
```

也就是说不能盲目地将即存代码中的 var 替换为 let 或 const。在重构的时候必须非常小心。

我的建议是：

首选 const。所有不会改变值的变量都可以使用它。
其它的使用 let，用于值会被改变的变量。
避免使用 var。

### 自己加的内容（这不是翻译）

学习分割线开始

---

顺便来聊聊js中定义变量时有无var的区别吧，虽然es6往后是不会再出现了，但是个知识点呀。
就上面的那段代码，这样写试试，会输出什么呢：

```html

var x = 3;
function func(randomize) {
  if (randomize) {
    var x = Math.random();
    return x;
  }
  return x;
}
func(false);  // undefined
console.log(x); // 3

2、
var x = 3;
function func(randomize) {
  if (randomize) {
    x = Math.random();
    return x;
  }
  return x;
}
func(false); // 3
console.log(x); // 3

```

看到结果输出了3，没错；为什么呢？

1. 在函数作用域内 加var定义的变量是局部变量，不加var定义的就成了全局变量。

    换成let会怎么样呢:

    ``` html
    
    let x = 3;
    function func(randomize) {
      if (randomize) {
      let x = Math.random(); // 这里用不用let结果都是一样的
        return x;
      }
      return x;
    }
    console.log(func(false)); // 3
    console.log(x); // 3
    
    ```

2. 因为使用var 定义变量还会提升变量声明，来再举个栗子：

* 使用var

``` html
    function hh(){
        console.log(a);
        var a = 'hello world';
    }
    hh()  // 'undefined'
```

* 不用var

``` html

function hh(){
    console.log(a);
    a = 'hello world';
   }
   hh()  // 'a is not defined'

```

3. 还有就是在ES5的'use strict'模式下，如果变量没有使用var定义，是会报错的。

学习分割线结束

---

## 4.2 从 IIFE 到块

ES5 中如果你想限制变量 tmp 的作用范围仅在某一块代码中，你必须使用一个叫 IIFE(Immediately-Invoked Function Expression，立即执行函数表达式) 的模式：

```html
(function () {  // IIFE 开始
    var tmp = ···;
    ···
}());  // IIFE 结束

console.log(tmp); // ReferenceError
```

ECMAScript 6 中可以简单地使用块和 let 申明(或 const 申明)：

```html
{  // 块起始
    let tmp = ···;
    ···
}  // 块结束

console.log(tmp); // ReferenceError
```
