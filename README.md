# You-Dont-Know-js

you dont know js 读书笔记

### <a href="#作用域">一. 作用域是什么</a>

### <a href="#词法作用域">二. 词法作用域</a>

### <a href="#函数作用域和块作用域">三. 函数作用域和块作用域</a>

### <a href="#提升">四.提升 </a>

### <a name="作用域">一. 作用域是什么</a>

#### 变量的赋值

变量的赋值会进行两个动作，首先编译器会在当前作用域下声明一个变量（如果作用域已经存在该变量则声明无效）。然后 js 引擎会在该作用域查找该变量，如果找到了则给变量赋值。如果没有找到，则在上一级作用域继续查找。如果到全局作用域仍然没有找到，则会抛出异常。

#### 遍历嵌套作用域规则

遍历嵌套作用域链的规则很简单:引擎从当前的执行作用域开始查找变量，如果找不到， 就向上一级继续查找。当抵达最外层的全局作用域时，无论找到还是没找到，查找过程都 会停止。

#### 异常类型

ReferenceError 同作用域判别（在作用域中找到某变量）失败相关，而 TypeError 则代表作用域判别成功了，但是对 结果的操作是非法或不合理的。

#### 作用域

作用域是一套规则，用于确定在何处以及如何查找变量(标识符)。如果查找的目的是对
变量进行赋值，那么就会使用 LHS 查询;如果目的是获取变量的值，就会使用 RHS 查询。

### <a name="词法作用域">二. 词法作用域</a>

#### 词法作用域

词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写 代码时将变量和块作用域写在哪里来决定的，因此当词法分析器处理代码时会保持作用域 不变(大部分情况下是这样的)。无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被声明时所处 的位置决定。

#### js 性能

JavaScript 引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的 词法进行静态分析，并预先确定所有变量和函数的定义位置，才能在执行过程中快速找到 标识符。

#### 如何欺骗词法作用域

avaScript 中有两个机制可以“欺骗”词法作用域:eval(..) 和 with。前者可以对一段包 含一个或多个声明的“代码”字符串进行演算，并借此来修改已经存在的词法作用域(在 运行时)。后者本质上是通过将一个对象的引用当作作用域来处理，将对象的属性当作作 用域中的标识符来处理，从而创建了一个新的词法作用域(同样是在运行时)。
这两个机制的副作用是引擎无法在编译时对作用域查找进行优化，因为引擎只能谨慎地认 为这样的优化是无效的。使用这其中任何一个机制都将导致代码运行变慢。不要使用它们。

### <a name="函数作用域和块作用域">三. 函数作用域和块作用域</a>

#### 函数作用域

函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复 用(事实上在嵌套的作用域中也可以使用)。这种设计方案是非常有用的，能充分利用 JavaScript 变量可以根据需要改变值类型的“动态”特性。
但与此同时，如果不细心处理那些可以在整个作用域范围内被访问的变量，可能会带来意 想不到的问题。

#### 匿名表达式缺点

1. 匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。
2. 如果没有函数名，当函数需要引用自身时只能使用已经过期的 arguments.callee 引用， 比如在递归中。另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑 自身。
3. 匿名函数省略了对于代码可读性/可理解性很重要的函数名。一个描述性的名称可以让 代码不言自明。
   始终给函数表达式命名是一个最佳的实践。

#### 块作用域

块作用域指的是变量和函数不仅可以属于所处的作用域，
也可以属于某个代码块(通常指 { .. } 内部)。
从 ES3 开始，try/catch 结构在 catch 分句中具有块作用域。
在 ES6 中引入了 let 关键字(var 关键字的表亲)，用来在任意代码块中声明变量。if (..) { let a = 2; } 会声明一个劫持了 if 的 { .. } 块的变量，并且将变量添加到这个块 中。
有些人认为块作用域不应该完全作为函数作用域的替代方案。两种功能应该同时存在，开 发者可以并且也应该根据需要选择使用何种作用域，创造可读、可维护的优良代码

### <a name="提升">四.提升 </a>

#### js 执行机制

引擎会 在解释 JavaScript 代码之前首先对其进行编译。编译阶段中的一部分工作就是找到所有的 声明，并用合适的作用域将它们关联起来。第 2 章中展示了这个机制，也正是词法作用域 的核心内容。

#### 变量提升

```
foo();
function foo() {
console.log( a ); // undefined var a = 2;
}
```

#### 函数声明可以被提升，但是函数表达式不能被提升。

```
foo();
function foo() {
console.log( a ); // undefined var a = 2;
}
```

#### 即使是具名的函数表达式，名称标识符在赋值之前也无法在所在作用域中使用

```
 foo(); // TypeError
      bar(); // ReferenceError
var foo = function bar() { // ...
};
```

#### 函数优先

函数声明和变量声明都会被提升。但是一个值得注意的细节(这个细节可以出现在有多个
“重复”声明的代码中)是函数会首先被提升，然后才是变量。

```
foo(); // 1
var foo;
function foo() { console.log( 1 );
}
foo = function() { console.log( 2 );
};
```

var foo 尽管出现在 function foo()... 的声明之前，但它是重复的声明(因此被忽 略了)，因为函数声明会被提升到普通变量之前。
尽管重复的 var 声明会被忽略掉，但出现在后面的函数声明还是可以覆盖前面的。

```
foo(); // 3
function foo() { console.log( 1 );
}
var foo = function() { console.log( 2 );
};
function foo() { console.log( 3 );
}
```

#### 小结

我们习惯将 var a = 2;看作一个声明，而实际上 JavaScript 引擎并不这么认为。它将 var a
和 a = 2 当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。
这意味着无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。 可以将这个过程形象地想象成所有的声明(变量和函数)都会被“移动”到各自作用域的 最顶端，这个过程被称为提升。声明本身会被提升，而包括函数表达式的赋值在内的赋值操作并不会提升。
要注意避免重复声明，特别是当普通的 var 声明和函数声明混合在一起的时候，否则会引
起很多危险的问题!
