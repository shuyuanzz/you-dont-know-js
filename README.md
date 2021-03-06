# You-Dont-Know-js

you dont know js 读书笔记

### <a href="#作用域">一. 作用域是什么</a>

### <a href="#词法作用域">二. 词法作用域</a>

### <a href="#函数作用域和块作用域">三. 函数作用域和块作用域</a>

### <a href="#提升">四.提升 </a>

### <a href="#作用域闭包">五.作用域闭包 </a>

### <a href="#关于this">六. 关于 this</a>

### <a href="#对象">七. 对象</a>

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

词法作用域是一套关于引擎如何寻找变量以及会在何处找到变量的规则。

词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写 代码时将变量和块作用域写在哪里来决定的，因此当词法分析器处理代码时会保持作用域 不变(大部分情况下是这样的)。无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被声明时所处 的位置决定。

#### js 性能

JavaScript 引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的 词法进行静态分析，并预先确定所有变量和函数的定义位置，才能在执行过程中快速找到 标识符。

#### 如何欺骗词法作用域

avaScript 中有两个机制可以“欺骗”词法作用域:eval(..) 和 with。前者可以对一段包 含一个或多个声明的“代码”字符串进行演算，并借此来修改已经存在的词法作用域(在 运行时)。后者本质上是通过将一个对象的引用当作作用域来处理，将对象的属性当作作 用域中的标识符来处理，从而创建了一个新的词法作用域(同样是在运行时)。
这两个机制的副作用是引擎无法在编译时对作用域查找进行优化，因为引擎只能谨慎地认 为这样的优化是无效的。使用这其中任何一个机制都将导致代码运行变慢。不要使用它们。

#### 词法作用域 vs 动态作用域

词法作用域，在书写的时候确定。

```
function foo() {
    console.log( a ); // 2
}
function bar() {
    var a = 3;
    foo();
}
var a = 2;
bar();
```

动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们从何处调 用。换句话说，作用域链是基于调用栈的，而不是代码中的作用域嵌套。

```
function foo() {
    console.log( a ); // 3(不是 2 !)
}
function bar() {
     var a = 3;
        foo();
}
var a = 2; bar();

```

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
console.log( a ); // undefined
var a = 2;
}
```

#### 函数声明可以被提升，但是函数表达式不能被提升。

```
foo();//TypeError
var foo =  function(){
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

### <a name="作用域闭包">作用域闭包</a>

#### 闭包

当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包

#### 模块的创建

```
function CoolModule() {
var something = "cool";

var another = [1, 2, 3];
function doSomething() { console.log( something );
}
function doAnother() {
console.log( another.join( " ! " ) );
}
return {
doSomething: doSomething, doAnother: doAnother
}; }
var foo = CoolModule(); foo.doSomething(); // cool
     foo.doAnother(); // 1 ! 2 ! 3
```

必要条件：

1. 必须有外部的封闭函数，该函数必须至少被调用一次(每次调用都会创建一个新的模块 实例)。
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并 且可以访问或者修改私有的状态。

#### 基于函数模块 vs ES6 模块

基于函数的模块并不是一个能被稳定识别的模式(编译器无法识别)，它们 的 API 语义只有在运行时才会被考虑进来。因此可以在运行时修改一个模块 的 API(参考前面关于公共 API 的讨论)。
相比之下，ES6 模块 API 更加稳定(API 不会在运行时改变)。由于编辑器知 道这一点，因此可以在(的确也这样做了)编译期检查对导入模块的 API 成 员的引用是否真实存在。如果 API 引用并不存在，编译器会在运行时抛出一 个或多个“早期”错误，而不会像往常一样在运行期采用动态的解决方案。

#### ES6 模块 api 详解

import 可以将一个模块中的一个或多个 API 导入到当前作用域中，并分别绑定在一个变量 上(在我们的例子里是 hello)。module 会将整个模块的 API 导入并绑定到一个变量上(在 我们的例子里是 foo 和 bar)。export 会将当前模块的一个标识符(变量、函数)导出为公 共 API。这些操作可以在模块定义中根据需要使用任意多次。
模块文件中的内容会被当作好像包含在作用域闭包中一样来处理，就和前面介绍的函数闭 包模块一样。

### <a name="关于this">六. 关于 this</a>

#### 为什么要使用 this

this 提供了一种更优雅的方式来隐式“传递”一个对象引用，因此可以将 API 设计
得更加简洁并且易于复用

#### 什么是 this

当一个函数被调用时，会创建一个活动记录(有时候也称为执行上下文)。这个记录会包 含函数在哪里被调用(调用栈)、函数的调用方法、传入的参数等信息。this 就是记录的 其中一个属性，会在函数执行的过程中用到。

#### 关键点

this 既不指向函数自身也不指向函数的词法作用域，你也许被 这样的解释误导过，但其实它们都是错误的。
this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

#### this 分析流程

1. 根据调用栈分析调用位置。（调用栈前一个对象）
2. 跟据绑定规则来决定 this 的绑定对象。

#### 绑定规则

1. 默认绑定 windows（如果使用严格模式 默认绑定会绑定到 undefined）
2. 隐式绑定：

```
function foo() {
     console.log( this.a );
}
var obj = {
    a: 2,
    foo: foo
};
obj.foo(); // 2

//隐式丢失
function foo() {
console.log( this.a );
}
var obj = {
    a: 2,
    foo: foo
};
var bar = obj.foo; // 函数别名!
var a = "oops, global"; // a 是全局对象的属性
bar(); // "oops, global"
```

3. 显式绑定：

```
//硬绑定
function foo() {
     console.log( this.a );
}
var obj = {
     a:2
};
var bar = function() {
    foo.call( obj );
};
 bar(); // 2
 setTimeout( bar, 100 ); // 2
// 硬绑定的 bar 不可能再修改它的
this bar.call( window ); // 2

```

4. new 绑定

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建(或者说构造)一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的 this。
4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

#### 箭头函数

箭头函数在涉及 this 绑定时的行为和普通函数的行为完全不一致。它放弃了所 有普通 this 绑定的规则，取而代之的是用当前的词法作用域覆盖了 this 本来的值。箭头函数的 this 无法被修改，new 也不行。具体来说，箭头函数会继承外层函数调用的 this 绑定(无论 this 绑定到什么)。这其实和 ES6 之前代码中的 self = this 机制一样

#### 如何判断 this

现在我们可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的
顺序来进行判断:

1. 函数是否在 new 中调用(new 绑定)?如果是的话 this 绑定的是新创建的对象。
   var bar = new foo()
2. 函数是否通过 call、apply(显式绑定)或者硬绑定调用?如果是的话，this 绑定的是 指定的对象。
   var bar = foo.call(obj2)
3. 函数是否在某个上下文对象中调用(隐式绑定)?如果是的话，this 绑定的是那个上下文对象。（普通函数中调用普通函数 this 绑定 window-全局对象）
   var bar = obj1.foo()
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到 全局对象。
   var bar = foo()
   就是这样。对于正常的函数调用来说，理解了这些知识你就可以明白 this 的绑定原理了。
   不过......凡事总有例外。

#### this 被忽略的情况

1. 如果你把 null 或者 undefined 作为 this 的绑定对象传入 call、apply 或者 bind，这些值
   在调用时会被忽略，实际应用的是默认绑定规则:

2. 间接引用

```

function foo() {
    console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };
o.foo(); // 3
(p.foo = o.foo)(); // 2
//赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo() 或者 o.foo()。根据我//们之前说过的，这里会应用默认绑定。
```

### <a name="对象">七. 对象</a>

#### 对象里的内容

需要强调的一点是，当我们说“内容”时，似乎在暗示这些值实际上被存储在对象内部， 但是这只是它的表现形式。在引擎内部，这些值的存储方式是多种多样的，一般并不会存 在对象容器内部。存储在对象容器内部的是这些属性的名称，它们就像指针(从技术角度 来说就是引用)一样，指向这些值真正的存储位置。

#### 对象的深浅拷贝

可查资料

#### 属性描述符

```
var myObject = {
     a:2
};
//获取
 Object.getOwnPropertyDescriptor( myObject, "a" );
// {
// value: 2,
// writable: true,
// enumerable: true,
// configurable: true
// }
//定义
var myObject = {};
Object.defineProperty( myObject, "a",
{
         value: 2,
         writable: true,
         configurable: true,
          enumerable: true
} );

```

1. Writable
   writable 决定是否可以修改属性的值。
2. Configurable
   只要属性是可配置的，就可以使用 defineProperty(..) 方法来修改属性描述符:
   要注意有一个小小的例外:即便属性是 configurable:false，我们还是可以 把 writable 的状态由 true 改为 false，但是无法由 false 改为 true。
3. Enumerable
   这里我们要介绍的最后一个属性描述符(还有两个，我们会在介绍 getter 和 setter 时提到) 是 enumerable。
   从名字就可以看出，这个描述符控制的是属性是否会出现在对象的属性枚举中，比如说 for..in 循环。如果把 enumerable 设置成 false，这个属性就不会出现在枚举中，虽然仍 然可以正常访问它。相对地，设置成 true 就会让它出现在枚举中。
   用户定义的所有的普通属性默认都是 enumerable，这通常就是你想要的。但是如果你不希 望某些特殊属性出现在枚举中，那就把它设置成 enumerable:false。

#### 如何实现对象不变性

1. 对象常量
结合 writable:false 和 configurable:false 就可以创建一个真正的常量属性(不可修改、 重定义或者删除):

```
var myObject = {};
Object.defineProperty(myObject, "FAVORITE_NUMBER", {
    value: 42,
    writable: false,
    configurable: false
});
```

2. 禁止扩展
如果你想禁止一个对象添加新属性并且保留已有属性，可以使用 Object.prevent Extensions(..):
```
var myObject = {
        a: 2
    };
Object.preventExtensions(myObject);
myObject.b = 3;
myObject.b; // undefined
```

3. 密封
Object.seal(..) 会创建一个“密封”的对象，这个方法实际上会在一个现有对象上调用 Object.preventExtensions(..) 并把所有现有属性标记为 configurable:false。
所以，密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有属性(虽然可以 修改属性的值)。

4. 冻结
Object.freeze(..) 会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 Object.seal(..) 并把所有“数据访问”属性标记为 writable:false，这样就无法修改它们 的值。
这个方法是你可以应用在对象上的级别最高的不可变性，它会禁止对于对象本身及其任意 直接属性的修改(不过就像我们之前说过的，这个对象引用的其他对象是不受影响的)。
你可以“深度冻结”一个对象，具体方法为，首先在这个对象上调用 Object.freeze(..)， 然后遍历它引用的所有对象并在这些对象上调用 Object.freeze(..)。但是一定要小心，因 为这样做有可能会在无意中冻结其他(共享)对象。


#### 如何判断对象是否存在于对象中

```
var myObject = {
    a: 2
};
("a" in myObject); // true
("b" in myObject); // false
myObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("b"); // false
```
in 操作符会检查属性是否在对象及其 [[Prototype]] 原型链中(参见第 5 章)。相比之下， hasOwnProperty(..) 只会检查属性是否在 myObject 对象中