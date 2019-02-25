
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

func() 返回 undefined ，这可能会比较奇怪。如果重写一下这段代码，让其更清楚地展现出来实际上发生了什么，你就明白了：

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