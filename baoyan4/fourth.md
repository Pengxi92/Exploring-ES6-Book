
### 第四章链接：

http://exploringjs.com/es6/ch_core-features.ruby#ch_core-features

# 4. 核心ES6特性

本章描述了ES6的核心特性。这些特性是日常采用的特性;剩下的特性主要是学术作者们（这里的原文是 library authors，感觉翻译的不太合理）感兴趣的。我将通过相应的ES5代码解释每一个特性。

## 4.1  从 var 到 let/const

在ES5中，通过var声明变量。这些变量是函数作用域的，它们的作用域是最内层封闭的函数。var的行为有时令人困惑。举个ES5的例子:

``` ruby
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

``` ruby
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

```ruby
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

### 学习汾分割线（这不是翻译）

---

顺便来聊聊js中定义变量时有无var的区别吧，虽然es6往后是不会再出现了，但是个知识点呀。
就上面的那段代码，这样写试试，会输出什么呢：

```ruby

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

    ``` ruby
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

2. var和function声明的变量都声明提前，赋值留在原地。 声明提前！声明提前！声明提前！不是值提前哈，来再举个栗子：

* 使用var

``` ruby
    function hh(){
        console.log(a);
        var a = 'hello world';
    }
    hh()  // 'undefined'
```

* 不用var

``` ruby

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

```ruby
(function () {  // IIFE 开始
    var tmp = ···;
    ···
}());  // IIFE 结束

console.log(tmp); // ReferenceError
```

ECMAScript 6 中可以简单地使用块和 let 申明(或 const 申明)：

```ruby
{  // 块起始
    let tmp = ···;
    ···
}  // 块结束
console.log(tmp); // ReferenceError
```

## 4.3 从字符串拼接，到模板字面量

ES6 中，JavaScript 终于有了字符串插值和多行文本。

### 4.3.1 String 插值

ES5 中你想在字符串中插入一些值，你需要将那些值和字符串片断连接起来：

```ruby
function printCoord(x, y) {
    console.log('('+x+', '+y+')');
}
```

ES6 中你可以在模板字面量中使用字符串插值：

```ruby
function printCoord(x, y) {
    console.log(`(${x}, ${y})`);
}
```

### 4.3.2 多行文本

模板文字也有助于表示多行字符串。

例如，在 ES5 中你要这么做：

```ruby
var HTML5_SKELETON =
    '<!doctype html>\n' +
    '<html>\n' +
    '<head>\n' +
    '    <meta charset="UTF-8">\n' +
    '    <title></title>\n' +
    '</head>\n' +
    '<body>\n' +
    '</body>\n' +
    '</html>\n';
```

如果你通过反斜杠来转义换行符，看起来会好一些(但是仍然需要显式添加换行符)：

```ruby
var HTML5_SKELETON = '\
 <!doctype html>\n\
 <html>\n\
 <head>\n\
 <meta charset="UTF-8">\n\
 <title></title>\n\
 </head>\n\
 <body>\n\
 </body>\n\
 </html>';
```

ES6 的模板字面量允许多行文本：

```ruby
const HTML5_SKELETON = `
 <!doctype html>
 <html>
 <head>
 <meta charset="UTF-8">
 <title></title>
 </head>
 <body>
 </body>
 </html>`;
```

(示例中包含了许多的的空白字符，不过对这个示例没有影响。)

### 4.4 从函数表达式到箭头函数

当前 ES5 代码中，在使用函数表达式的时候，你必须小心 this。在下面的示例中我会在创建一个 _this(A 行) 作为辅助变量，这样在 B 行才可能访问到指向 UiComponent 对象的 this。

```ruby
function UiComponent() {
    var _this = this; // (A)
    var button = document.getElementById('myButton');
    button.addEventListener('click', function () {
        console.log('CLICK');
        _this.handleClick(); // (B)
    });
}
UiComponent.prototype.handleClick = function () {
    ···
};
```

在ES6中，你可以使用箭头函数，不会影响 this （A行）：

```ruby
function UiComponent() {
    var button = document.getElementById('myButton');
    button.addEventListener('click', () => {
        console.log('CLICK');
        this.handleClick(); // (A)
    });
}
```

(ES6 中你也可以选择使用 class 代替构建函数，这会在稍后详述。)

对于一些只需要返回某个表达式值的简短的回调，用箭头函数特别方便。

ES5 中这类回调相对繁琐：

```ruby
var arr = [1, 2, 3];
var squares = arr.map(function (x) { return x * x });
```

ES6 中使用箭头函数就简洁得多：

```ruby
const arr = [1, 2, 3];
const squares = arr.map(x => x * x);
```

在定义参数的时候，如果只有一个参数，你可以省略掉括号。像 (x) => x * x 和 x => x * x 都可以。

### 4.5 处理多个返回值

有一些函数或者方法通过数组或对象返回多个值。在 ES5 中，你需要创建一个临时变量来访问那些值。但在 ES6 中你可以使用解构来避免使用临时变量。

#### 4.5.1 通过数组返回多个值

exec() 以伪数组对象的形式返回匹配到的各组。ES5 中需要一个临时变量(下面示例中的matchOjb)，即使你只关心配到的组：

```ruby
var matchObj =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');
var year = matchObj[1];
var month = matchObj[2];
var day = matchObj[3];
```

ES6 的解构让代码变得简单：

```ruby
const [, year, month, day] =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');
```

数组模式开始处的一个空位置，是用来跳过索引0处的数组元素。

##### 学习分割线开始（这不是翻译）

---

* JavaScript exec() 方法： http://www.w3school.com.cn/js/jsref_exec_regexp.asp

* 而且，你可以忽略你不感兴趣的返回值,例如上述例子中忽略的第一个值一样：

    ```ruby
    function f() {
      return [1, 2, 3];
    }

    let [a, , b] = f();
    console.log(a); // 1
    console.log(b); // 3
    ```

* 当解构一个数组时，可以使用剩余模式（拓展语句），将数组剩余部分赋值给一个变量。

    ```ruby
    let [a, ...b] = [1, 2, 3];
    console.log(a); // 1
    console.log(b); // [2, 3]
    ```

学习分割线结束

---

#### 4.5.2 通过对象返回多个值

Object.getOwnPropertyDescriptor() 方法返回一个属性描述对象，这个对象在它的属性中包含了多个值。

即使你只关心对象的属性，在 ES5 中你也必须使用临时变量(下例中的 propDesc)：

```ruby
var obj = { foo: 123 };

var propDesc = Object.getOwnPropertyDescriptor(obj, 'foo');
var writable = propDesc.writable;
var configurable = propDesc.configurable;

console.log(writable, configurable); // true true
```

在 ES6 中就可以使用解构

```ruby
const obj = { foo: 123 };

const {writable, configurable} =
    Object.getOwnPropertyDescriptor(obj, 'foo');

console.log(writable, configurable); // true true
```

{writable, configurable} 是一个缩写。完整的是：
{ writable: writable, configurable: configurable }

### 4.6 从 for 到 forEach() 再到 for-of

在 ES5 之前，你需要遍历数组，如下面这样：

```ruby
var arr = ['a', 'b', 'c'];
for (var i=0; i<arr.length; i++) {
    var elem = arr[i];
    console.log(elem);
}
```

在ES5中你还有一个选择： forEach():

```ruby
arr.forEach(function (elem) {
    console.log(elem);
});
```

for 循环的优势在于可以中止，forEach() 则更简洁。

ES6 中的 for-of 循环综合了两者的优点：

```ruby
const arr = ['a', 'b', 'c'];
for (const elem of arr) {
    console.log(elem);
}
```

如果你既需要元素索引又需要数组元素值，for-of 可以通过一个新的数组方法 entries()，配合使用解构来办到：

```ruby
for (const [index, elem] of arr.entries()) {
    console.log(index+'. '+elem);
}
```

#### 学习分割线又开始（这不是翻译）

---

* javascript entries() 方法 ：

entries() 方法返回一个 Array Iterator 对象，该对象包含数组中每一个索引的键值对。

* 举个栗子：

  ```ruby
  var arr = ["a", "b", "c"];
  var eArr = arr.entries();
  
  console.log(eArr.next().value); // [0, "a"]
  console.log(eArr.next().value); // [1, "b"]
  console.log(eArr.next().value); // [2, "c"]
  
  ```

学习分割线结束

---

### 4.7 默认参数值

在ES5中指定一个参数的默认值要这样：

```ruby
function foo(x, y) {
    x = x || 0;
    y = y || 0;
    ···
}
```

ES6有更好的语法：

```ruby
function foo(x=0, y=0) {
    ···
}
```

 ES6的另一个人好处是，一个参数的默认值只有在undefined的情况下会触发，而在ES5中所有判断是false的值都会被替换成默认值。

### 4.8 命名参数

 在javaScript中命名参数常用的方法是对象字像字面量（也叫选择对象模式）

```ruby
selectEntries({ start: 0, end: -1 });
```

这种方法带来了两个好处：代码可以自解释，而且很轻易的可以忽略某些参数

在ES5中，您可以实现selectEntries()如下:

```ruby
function selectEntries(options) {
    var start = options.start || 0;
    var end = options.end || -1;
    var step = options.step || 1;
    ···
}
```

在ES6中，你可以在参数定义中使用解构，看起来代码简洁了很多：

```ruby
function selectEntries({ start=0, end=-1, step=1 }) {
    ···
}
```

#### 4.8.1 使参数可选

在ES5中你要想实现参数可选，你需要在代码里加上A行：

```ruby
function selectEntries(options) {
    options = options || {}; // (A)
    var start = options.start || 0;
    var end = options.end || -1;
    var step = options.step || 1;
    ···
}
```

ES6中可以指定{}作为参数的默认值

```ruby
function selectEntries({ start=0, end=-1, step=1 } = {}) {
    ···
}
```

### 4.9 从argument 到rest 参数

在ES5中如果你想让一个函数（或方法）接受任意数量的参数，你必须要使用这个特殊变量:arguements:

```ruby
function logAllArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

在ES6中，你可以通过...运算符定义一个剩余变量（例如下面栗子中的args）：

```ruby
function logAllArguments(...args) {
    for (const arg of args) {
        console.log(arg);
    }
}
```

如果您只对尾部参数感兴趣，Rest参数甚至更好:

```ruby
function format(pattern, ...args) {
    ···
}
```

用ES5处理同样的事情就有点复杂了：

```ruby
function format(pattern) {
    var args = [].slice.call(arguments, 1);
    ···
}
```

剩余参数让代码变得简单易读：你只要通过看参数定义就能知道它有可变数量的参数。

### 4.10 从apply扩展到运算符（...）

在ES6中通过apply来把数组作为参数使用，ES6通过扩展运算符来解决这个问题：

#### 4.10.1 Math.max()

Math.max() 返回参数中最大的数。它接受数量不定的参数，但不接受数组。

ES5 -apply()

```ruby
> Math.max.apply(Math, [-1, 5, 11, 3])
11
```

ES6扩扩展运算符：

```ruby
const arr1 = ['a', 'b'];
const arr2 = ['c', 'd'];

arr1.push(...arr2);
    // arr1 is now ['a', 'b', 'c', 'd']

```

#### 4.10.2 Array.prototype.push()

ES5 – apply()：

```runby
var arr1 = ['a', 'b'];
var arr2 = ['c', 'd'];

arr1.push.apply(arr1, arr2);
    // arr1 is now ['a', 'b', 'c', 'd']
```

ES6 – 扩展运算符：

```ruby
const arr1 = ['a', 'b'];
const arr2 = ['c', 'd'];

arr1.push(...arr2);
    // arr1 is now ['a', 'b', 'c', 'd']
```

### 4.11 从 concat() 到扩展运算符 (...)

扩展运算符也能(并非解构)将其内容转换为数组元素。也就是说，它可以代替数组方法 concat()。

ES5 – concat()：

```ruby
var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

console.log(arr1.concat(arr2, arr3));
    // [ 'a', 'b', 'c', 'd', 'e' ]
```

ES6 – 扩展运算符：

```ruby
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

console.log([...arr1, ...arr2, ...arr3]);
    // [ 'a', 'b', 'c', 'd', 'e' ]
```

### 4.12 从对象字符量的函数表达式到方法定义

JavaScript 的方法是值为函数的属性。

ES5 对象字面量中，添加方法和添加其它属性一样，其属性值是函数表达式。

```ruby
var obj = {
    foo: function () {
        ···
    },
    bar: function () {
        this.foo();
    }, // trailing comma is legal in ES5
}
```

ES6 引入了 方法定义，专门用于添加方法的语法：

```ruby
const obj = {
    foo() {
        ···
    },
    bar() {
        this.foo();
    },
}
```

### 4.13 从构造器到类

ES6 引入的类语法比原来的构建函数更为方便。

#### 4.13.1 基类

ES5中直接实现一个构造函数：

```ruby
function Person(name) {
    this.name = name;
}
Person.prototype.describe = function () {
    return 'Person called '+this.name;
};
```

es6的类语法提供了比构造函数稍微方便一点的语法：

```ruby
class Person {
    constructor(name) {
        this.name = name;
    }
    describe() {
        return 'Person called '+this.name;
    }
}
```

注意简化的方法定义语法——不再考虑function关键字。也请注意类的各个部分之间没有逗号。

#### 4.13.2 派生类

ES5中织实现子类是件麻烦的事情，尤其是使用父类的构造函数和父类属性的时候，下面使用经典方法创建person的子类构造函数 Employee：

```ruby
function Employee(name, title) {
    Person.call(this, name); // super(name)
    this.title = title;
}
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;
Employee.prototype.describe = function () {
    return Person.prototype.describe.call(this) // super.describe()
           + ' (' + this.title + ')';
};
```

ES6 内置支持子类，只需要使用 extends 子句：

```ruby
class Employee extends Person {
    constructor(name, title) {
        super(name);
        this.title = title;
    }
    describe() {
        return super.describe() + ' (' + this.title + ')';
    }
}
```
