# 1.实例对象与 new 命令

JavaScript 语言具有很强的面向对象编程能力，本章介绍 JavaScript 面向对象编程的基础知识。

## 对象是什么

面向对象编程（Object Oriented Programming，缩写为 OOP）是目前主流的编程范式。它将真实世界各种复杂的关系，抽象为一个个对象，然后由对象之间的分工与合作，完成对真实世界的模拟。

每一个对象都是功能中心，具有明确分工，可以完成接受信息、处理数据、发出信息等任务。对象可以复用，通过继承机制还可以定制。因此，面向对象编程具有灵活、代码可复用、高度模块化等特点，容易维护和开发，比起由一系列函数或指令组成的传统的过程式编程（procedural programming），更适合多人合作的大型软件项目。

那么，“对象”（object）到底是什么？我们从两个层次来理解。

**（1）对象是单个实物的抽象。**

一本书、一辆汽车、一个人都可以是对象，一个数据库、一张网页、一个远程服务器连接也可以是对象。当实物被抽象成对象，实物之间的关系就变成了对象之间的关系，从而就可以模拟现实情况，针对对象进行编程。

**（2）对象是一个容器，封装了属性（property）和方法（method）。**

属性是对象的状态，方法是对象的行为（完成某种任务）。比如，我们可以把动物抽象为`animal`对象，使用“属性”记录具体是哪一种动物，使用“方法”表示动物的某种行为（奔跑、捕猎、休息等等）。

## 构造函数

面向对象编程的第一步，就是要生成对象。前面说过，对象是单个实物的抽象。通常需要一个模板，表示某一类实物的共同特征，然后对象根据这个模板生成。

典型的面向对象编程语言（比如 C++ 和 Java），都有“类”（class）这个概念。所谓“类”就是对象的模板，对象就是“类”的实例。但是，JavaScript 语言的对象体系，不是基于“类”的，而是基于构造函数（constructor）和原型链（prototype）。

JavaScript 语言使用构造函数（constructor）作为对象的模板。所谓”构造函数”，就是专门用来生成实例对象的函数。它就是对象的模板，描述实例对象的基本结构。一个构造函数，可以生成多个实例对象，这些实例对象都有相同的结构。

构造函数就是一个普通的函数，但具有自己的特征和用法。

```javascript
var Vehicle = function () {
  this.price = 1000;
};
```

上面代码中，`Vehicle`就是构造函数。为了与普通函数区别，构造函数名字的第一个字母通常大写。

构造函数的特点有两个。

- 函数体内部使用了`this`关键字，代表了所要生成的对象实例。
- 生成对象的时候，必须使用`new`命令。

下面先介绍`new`命令。

## new 命令

### 基本用法

`new`命令的作用，就是执行构造函数，返回一个实例对象。

```javascript
var Vehicle = function () {
  this.price = 1000;
};

var v = new Vehicle();
v.price // 1000
```

上面代码通过`new`命令，让构造函数`Vehicle`生成一个实例对象，保存在变量`v`中。这个新生成的实例对象，从构造函数`Vehicle`得到了`price`属性。`new`命令执行时，构造函数内部的`this`，就代表了新生成的实例对象，`this.price`表示实例对象有一个`price`属性，值是1000。

使用`new`命令时，根据需要，构造函数也可以接受参数。

```javascript
var Vehicle = function (p) {
  this.price = p;
};

var v = new Vehicle(500);
```

`new`命令本身就可以执行构造函数，所以后面的构造函数可以带括号，也可以不带括号。下面两行代码是等价的，但是为了表示这里是函数调用，推荐使用括号。

```javascript
// 推荐的写法
var v = new Vehicle();
// 不推荐的写法
var v = new Vehicle;
```

一个很自然的问题是，如果忘了使用`new`命令，直接调用构造函数会发生什么事？

这种情况下，构造函数就变成了普通函数，并不会生成实例对象。而且由于后面会说到的原因，`this`这时代表全局对象，将造成一些意想不到的结果。

```javascript
var Vehicle = function (){
  this.price = 1000;
};

var v = Vehicle();
v // undefined
price // 1000
```

上面代码中，调用`Vehicle`构造函数时，忘了加上`new`命令。结果，变量`v`变成了`undefined`，而`price`属性变成了全局变量。因此，应该非常小心，避免不使用`new`命令、直接调用构造函数。

为了保证构造函数必须与`new`命令一起使用，一个解决办法是，构造函数内部使用严格模式，即第一行加上`use strict`。这样的话，一旦忘了使用`new`命令，直接调用构造函数就会报错。

```javascript
function Fubar(foo, bar){
  'use strict';
  this._foo = foo;
  this._bar = bar;
}

Fubar()
// TypeError: Cannot set property '_foo' of undefined
```

上面代码的`Fubar`为构造函数，`use strict`命令保证了该函数在严格模式下运行。由于严格模式中，函数内部的`this`不能指向全局对象，默认等于`undefined`，导致不加`new`调用会报错（JavaScript 不允许对`undefined`添加属性）。

另一个解决办法，构造函数内部判断是否使用`new`命令，如果发现没有使用，则直接返回一个实例对象。

```javascript
function Fubar(foo, bar) {
  if (!(this instanceof Fubar)) {
    return new Fubar(foo, bar);
  }

  this._foo = foo;
  this._bar = bar;
}

Fubar(1, 2)._foo // 1
(new Fubar(1, 2))._foo // 1
```

上面代码中的构造函数，不管加不加`new`命令，都会得到同样的结果。

### new 命令的原理

使用`new`命令时，它后面的函数依次执行下面的步骤。

1. 创建一个空对象，作为将要返回的对象实例。
1. 将这个空对象的原型，指向构造函数的`prototype`属性。
1. 将这个空对象赋值给函数内部的`this`关键字。
1. 开始执行构造函数内部的代码。

也就是说，构造函数内部，`this`指的是一个新生成的空对象，所有针对`this`的操作，都会发生在这个空对象上。构造函数之所以叫“构造函数”，就是说这个函数的目的，就是操作一个空对象（即`this`对象），将其“构造”为需要的样子。

如果构造函数内部有`return`语句，而且`return`后面跟着一个对象，`new`命令会返回`return`语句指定的对象；否则，就会不管`return`语句，返回`this`对象。

```javascript
var Vehicle = function () {
  this.price = 1000;
  return 1000;
};

(new Vehicle()) === 1000
// false
```

上面代码中，构造函数`Vehicle`的`return`语句返回一个数值。这时，`new`命令就会忽略这个`return`语句，返回“构造”后的`this`对象。

但是，如果`return`语句返回的是一个跟`this`无关的新对象，`new`命令会返回这个新对象，而不是`this`对象。这一点需要特别引起注意。

```javascript
var Vehicle = function (){
  this.price = 1000;
  return { price: 2000 };
};

(new Vehicle()).price
// 2000
```

上面代码中，构造函数`Vehicle`的`return`语句，返回的是一个新对象。`new`命令会返回这个对象，而不是`this`对象。

另一方面，如果对普通函数（内部没有`this`关键字的函数）使用`new`命令，则会返回一个空对象。

```javascript
function getMessage() {
  return 'this is a message';
}

var msg = new getMessage();

msg // {}
typeof msg // "object"
```

上面代码中，`getMessage`是一个普通函数，返回一个字符串。对它使用`new`命令，会得到一个空对象。这是因为`new`命令总是返回一个对象，要么是实例对象，要么是`return`语句指定的对象。本例中，`return`语句返回的是字符串，所以`new`命令就忽略了该语句。

`new`命令简化的内部流程，可以用下面的代码表示。

```javascript
function _new(/* 构造函数 */ constructor, /* 构造函数参数 */ params) {
  // 将 arguments 对象转为数组
  var args = [].slice.call(arguments);
  // 取出构造函数
  var constructor = args.shift();
  // 创建一个空对象，继承构造函数的 prototype 属性
  var context = Object.create(constructor.prototype);
  // 执行构造函数
  var result = constructor.apply(context, args);
  // 如果返回结果是对象，就直接返回，否则返回 context 对象
  return (typeof result === 'object' && result != null) ? result : context;
}

// 实例
var actor = _new(Person, '张三', 28);
```

### new.target

函数内部可以使用`new.target`属性。如果当前函数是`new`命令调用，`new.target`指向当前函数，否则为`undefined`。

```javascript
function f() {
  console.log(new.target === f);
}

f() // false
new f() // true
```

使用这个属性，可以判断函数调用的时候，是否使用`new`命令。

```javascript
function f() {
  if (!new.target) {
    throw new Error('请使用 new 命令调用！');
  }
  // ...
}

f() // Uncaught Error: 请使用 new 命令调用！
```

上面代码中，构造函数`f`调用时，没有使用`new`命令，就抛出一个错误。

## Object.create() 创建实例对象

构造函数作为模板，可以生成实例对象。但是，有时拿不到构造函数，只能拿到一个现有的对象。我们希望以这个现有的对象作为模板，生成新的实例对象，这时就可以使用`Object.create()`方法。

```javascript
var person1 = {
  name: '张三',
  age: 38,
  greeting: function() {
    console.log('Hi! I\'m ' + this.name + '.');
  }
};

var person2 = Object.create(person1);

person2.name // 张三
person2.greeting() // Hi! I'm 张三.
```

上面代码中，对象`person1`是`person2`的模板，后者继承了前者的属性和方法。

# 2.this 关键字

## 涵义

`this`关键字是一个非常重要的语法点。毫不夸张地说，不理解它的含义，大部分开发任务都无法完成。

前一章已经提到，`this`可以用在构造函数之中，表示实例对象。除此之外，`this`还可以用在别的场合。但不管是什么场合，`this`都有一个共同点：它总是返回一个对象。

简单说，`this`就是属性或方法“当前”所在的对象。

```javascript
this.property
```

上面代码中，`this`就代表`property`属性当前所在的对象。

下面是一个实际的例子。

```javascript
var person = {
  name: '张三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

person.describe()
// "姓名：张三"
```

上面代码中，`this.name`表示`name`属性所在的那个对象。由于`this.name`是在`describe`方法中调用，而`describe`方法所在的当前对象是`person`，因此`this`指向`person`，`this.name`就是`person.name`。

由于对象的属性可以赋给另一个对象，所以属性所在的当前对象是可变的，即`this`的指向是可变的。

```javascript
var A = {
  name: '张三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

var B = {
  name: '李四'
};

B.describe = A.describe;
B.describe()
// "姓名：李四"
```

上面代码中，`A.describe`属性被赋给`B`，于是`B.describe`就表示`describe`方法所在的当前对象是`B`，所以`this.name`就指向`B.name`。

稍稍重构这个例子，`this`的动态指向就能看得更清楚。

```javascript
function f() {
  return '姓名：'+ this.name;
}

var A = {
  name: '张三',
  describe: f
};

var B = {
  name: '李四',
  describe: f
};

A.describe() // "姓名：张三"
B.describe() // "姓名：李四"
```

上面代码中，函数`f`内部使用了`this`关键字，随着`f`所在的对象不同，`this`的指向也不同。

只要函数被赋给另一个变量，`this`的指向就会变。

```javascript
var A = {
  name: '张三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

var name = '李四';
var f = A.describe;
f() // "姓名：李四"
```

上面代码中，`A.describe`被赋值给变量`f`，内部的`this`就会指向`f`运行时所在的对象（本例是顶层对象）。

再看一个网页编程的例子。

```html
<input type="text" name="age" size=3 onChange="validate(this, 18, 99);">

<script>
function validate(obj, lowval, hival){
  if ((obj.value < lowval) || (obj.value > hival))
    console.log('Invalid Value!');
}
</script>
```

上面代码是一个文本输入框，每当用户输入一个值，就会调用`onChange`回调函数，验证这个值是否在指定范围。浏览器会向回调函数传入当前对象，因此`this`就代表传入当前对象（即文本框），然后就可以从`this.value`上面读到用户的输入值。

总结一下，JavaScript 语言之中，一切皆对象，运行环境也是对象，所以函数都是在某个对象之中运行，`this`就是函数运行时所在的对象（环境）。这本来并不会让用户糊涂，但是 JavaScript 支持运行环境动态切换，也就是说，`this`的指向是动态的，没有办法事先确定到底指向哪个对象，这才是最让初学者感到困惑的地方。

## 实质

JavaScript 语言之所以有 this 的设计，跟内存里面的数据结构有关系。

```javascript
var obj = { foo:  5 };
```

上面的代码将一个对象赋值给变量`obj`。JavaScript 引擎会先在内存里面，生成一个对象`{ foo: 5 }`，然后把这个对象的内存地址赋值给变量`obj`。也就是说，变量`obj`是一个地址（reference）。后面如果要读取`obj.foo`，引擎先从`obj`拿到内存地址，然后再从该地址读出原始的对象，返回它的`foo`属性。

原始的对象以字典结构保存，每一个属性名都对应一个属性描述对象。举例来说，上面例子的`foo`属性，实际上是以下面的形式保存的。

```javascript
{
  foo: {
    [[value]]: 5
    [[writable]]: true
    [[enumerable]]: true
    [[configurable]]: true
  }
}
```

注意，`foo`属性的值保存在属性描述对象的`value`属性里面。

这样的结构是很清晰的，问题在于属性的值可能是一个函数。

```javascript
var obj = { foo: function () {} };
```

这时，引擎会将函数单独保存在内存中，然后再将函数的地址赋值给`foo`属性的`value`属性。

```javascript
{
  foo: {
    [[value]]: 函数的地址
    ...
  }
}
```

由于函数是一个单独的值，所以它可以在不同的环境（上下文）执行。

```javascript
var f = function () {};
var obj = { f: f };

// 单独执行
f()

// obj 环境执行
obj.f()
```

JavaScript 允许在函数体内部，引用当前环境的其他变量。

```javascript
var f = function () {
  console.log(x);
};
```

上面代码中，函数体里面使用了变量`x`。该变量由运行环境提供。

现在问题就来了，由于函数可以在不同的运行环境执行，所以需要有一种机制，能够在函数体内部获得当前的运行环境（context）。所以，`this`就出现了，它的设计目的就是在函数体内部，指代函数当前的运行环境。

```javascript
var f = function () {
  console.log(this.x);
}
```

上面代码中，函数体里面的`this.x`就是指当前运行环境的`x`。

```javascript
var f = function () {
  console.log(this.x);
}

var x = 1;
var obj = {
  f: f,
  x: 2,
};

// 单独执行
f() // 1

// obj 环境执行
obj.f() // 2
```

上面代码中，函数`f`在全局环境执行，`this.x`指向全局环境的`x`；在`obj`环境执行，`this.x`指向`obj.x`。

## 使用场合

`this`主要有以下几个使用场合。

**（1）全局环境**

全局环境使用`this`，它指的就是顶层对象`window`。

```javascript
this === window // true

function f() {
  console.log(this === window);
}
f() // true
```

上面代码说明，不管是不是在函数内部，只要是在全局环境下运行，`this`就是指顶层对象`window`。

**（2）构造函数**

构造函数中的`this`，指的是实例对象。

```javascript
var Obj = function (p) {
  this.p = p;
};
```

上面代码定义了一个构造函数`Obj`。由于`this`指向实例对象，所以在构造函数内部定义`this.p`，就相当于定义实例对象有一个`p`属性。

```javascript
var o = new Obj('Hello World!');
o.p // "Hello World!"
```

**（3）对象的方法**

如果对象的方法里面包含`this`，`this`的指向就是方法运行时所在的对象。该方法赋值给另一个对象，就会改变`this`的指向。

但是，这条规则很不容易把握。请看下面的代码。

```javascript
var obj ={
  foo: function () {
    console.log(this);
  }
};

obj.foo() // obj
```

上面代码中，`obj.foo`方法执行时，它内部的`this`指向`obj`。

但是，下面这几种用法，都会改变`this`的指向。

```javascript
// 情况一
(obj.foo = obj.foo)() // window
// 情况二
(false || obj.foo)() // window
// 情况三
(1, obj.foo)() // window
```

上面代码中，`obj.foo`就是一个值。这个值真正调用的时候，运行环境已经不是`obj`了，而是全局环境，所以`this`不再指向`obj`。

可以这样理解，JavaScript 引擎内部，`obj`和`obj.foo`储存在两个内存地址，称为地址一和地址二。`obj.foo()`这样调用时，是从地址一调用地址二，因此地址二的运行环境是地址一，`this`指向`obj`。但是，上面三种情况，都是直接取出地址二进行调用，这样的话，运行环境就是全局环境，因此`this`指向全局环境。上面三种情况等同于下面的代码。

```javascript
// 情况一
(obj.foo = function () {
  console.log(this);
})()
// 等同于
(function () {
  console.log(this);
})()

// 情况二
(false || function () {
  console.log(this);
})()

// 情况三
(1, function () {
  console.log(this);
})()
```

如果`this`所在的方法不在对象的第一层，这时`this`只是指向当前一层的对象，而不会继承更上面的层。

```javascript
var a = {
  p: 'Hello',
  b: {
    m: function() {
      console.log(this.p);
    }
  }
};

a.b.m() // undefined
```

上面代码中，`a.b.m`方法在`a`对象的第二层，该方法内部的`this`不是指向`a`，而是指向`a.b`，因为实际执行的是下面的代码。

```javascript
var b = {
  m: function() {
   console.log(this.p);
  }
};

var a = {
  p: 'Hello',
  b: b
};

(a.b).m() // 等同于 b.m()
```

如果要达到预期效果，只有写成下面这样。

```javascript
var a = {
  b: {
    m: function() {
      console.log(this.p);
    },
    p: 'Hello'
  }
};
```

如果这时将嵌套对象内部的方法赋值给一个变量，`this`依然会指向全局对象。

```javascript
var a = {
  b: {
    m: function() {
      console.log(this.p);
    },
    p: 'Hello'
  }
};

var hello = a.b.m;
hello() // undefined
```

上面代码中，`m`是多层对象内部的一个方法。为求简便，将其赋值给`hello`变量，结果调用时，`this`指向了顶层对象。为了避免这个问题，可以只将`m`所在的对象赋值给`hello`，这样调用时，`this`的指向就不会变。

```javascript
var hello = a.b;
hello.m() // Hello
```

## 使用注意点

### 避免多层 this

由于`this`的指向是不确定的，所以切勿在函数中包含多层的`this`。

```javascript
var o = {
  f1: function () {
    console.log(this);
    var f2 = function () {
      console.log(this);
    }();
  }
}

o.f1()
// Object
// Window
```

上面代码包含两层`this`，结果运行后，第一层指向对象`o`，第二层指向全局对象，因为实际执行的是下面的代码。

```javascript
var temp = function () {
  console.log(this);
};

var o = {
  f1: function () {
    console.log(this);
    var f2 = temp();
  }
}
```

一个解决方法是在第二层改用一个指向外层`this`的变量。

```javascript
var o = {
  f1: function() {
    console.log(this);
    var that = this;
    var f2 = function() {
      console.log(that);
    }();
  }
}

o.f1()
// Object
// Object
```

上面代码定义了变量`that`，固定指向外层的`this`，然后在内层使用`that`，就不会发生`this`指向的改变。

事实上，使用一个变量固定`this`的值，然后内层函数调用这个变量，是非常常见的做法，请务必掌握。

JavaScript 提供了严格模式，也可以硬性避免这种问题。严格模式下，如果函数内部的`this`指向顶层对象，就会报错。

```javascript
var counter = {
  count: 0
};
counter.inc = function () {
  'use strict';
  this.count++
};
var f = counter.inc;
f()
// TypeError: Cannot read property 'count' of undefined
```

上面代码中，`inc`方法通过`'use strict'`声明采用严格模式，这时内部的`this`一旦指向顶层对象，就会报错。

### 避免数组处理方法中的 this

数组的`map`和`foreach`方法，允许提供一个函数作为参数。这个函数内部不应该使用`this`。

```javascript
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + ' ' + item);
    });
  }
}

o.f()
// undefined a1
// undefined a2
```

上面代码中，`foreach`方法的回调函数中的`this`，其实是指向`window`对象，因此取不到`o.v`的值。原因跟上一段的多层`this`是一样的，就是内层的`this`不指向外部，而指向顶层对象。

解决这个问题的一种方法，就是前面提到的，使用中间变量固定`this`。

```javascript
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    var that = this;
    this.p.forEach(function (item) {
      console.log(that.v+' '+item);
    });
  }
}

o.f()
// hello a1
// hello a2
```

另一种方法是将`this`当作`foreach`方法的第二个参数，固定它的运行环境。

```javascript
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + ' ' + item);
    }, this);
  }
}

o.f()
// hello a1
// hello a2
```

### 避免回调函数中的 this

回调函数中的`this`往往会改变指向，最好避免使用。

```javascript
var o = new Object();
o.f = function () {
  console.log(this === o);
}

// jQuery 的写法
$('#button').on('click', o.f);
```

上面代码中，点击按钮以后，控制台会显示`false`。原因是此时`this`不再指向`o`对象，而是指向按钮的 DOM 对象，因为`f`方法是在按钮对象的环境中被调用的。这种细微的差别，很容易在编程中忽视，导致难以察觉的错误。

为了解决这个问题，可以采用下面的一些方法对`this`进行绑定，也就是使得`this`固定指向某个对象，减少不确定性。

## 绑定 this 的方法

`this`的动态切换，固然为 JavaScript 创造了巨大的灵活性，但也使得编程变得困难和模糊。有时，需要把`this`固定下来，避免出现意想不到的情况。JavaScript 提供了`call`、`apply`、`bind`这三个方法，来切换/固定`this`的指向。

### Function.prototype.call()

函数实例的`call`方法，可以指定函数内部`this`的指向（即函数执行时所在的作用域），然后在所指定的作用域中，调用该函数。

```javascript
var obj = {};

var f = function () {
  return this;
};

f() === window // true
f.call(obj) === obj // true
```

上面代码中，全局环境运行函数`f`时，`this`指向全局环境（浏览器为`window`对象）；`call`方法可以改变`this`的指向，指定`this`指向对象`obj`，然后在对象`obj`的作用域中运行函数`f`。

`call`方法的参数，应该是一个对象。如果参数为空、`null`和`undefined`，则默认传入全局对象。

```javascript
var n = 123;
var obj = { n: 456 };

function a() {
  console.log(this.n);
}

a.call() // 123
a.call(null) // 123
a.call(undefined) // 123
a.call(window) // 123
a.call(obj) // 456
```

上面代码中，`a`函数中的`this`关键字，如果指向全局对象，返回结果为`123`。如果使用`call`方法将`this`关键字指向`obj`对象，返回结果为`456`。可以看到，如果`call`方法没有参数，或者参数为`null`或`undefined`，则等同于指向全局对象。

如果`call`方法的参数是一个原始值，那么这个原始值会自动转成对应的包装对象，然后传入`call`方法。

```javascript
var f = function () {
  return this;
};

f.call(5)
// Number {[[PrimitiveValue]]: 5}
```

上面代码中，`call`的参数为`5`，不是对象，会被自动转成包装对象（`Number`的实例），绑定`f`内部的`this`。

`call`方法还可以接受多个参数。

```javascript
func.call(thisValue, arg1, arg2, ...)
```

`call`的第一个参数就是`this`所要指向的那个对象，后面的参数则是函数调用时所需的参数。

```javascript
function add(a, b) {
  return a + b;
}

add.call(this, 1, 2) // 3
```

上面代码中，`call`方法指定函数`add`内部的`this`绑定当前环境（对象），并且参数为`1`和`2`，因此函数`add`运行后得到`3`。

`call`方法的一个应用是调用对象的原生方法。

```javascript
var obj = {};
obj.hasOwnProperty('toString') // false

// 覆盖掉继承的 hasOwnProperty 方法
obj.hasOwnProperty = function () {
  return true;
};
obj.hasOwnProperty('toString') // true

Object.prototype.hasOwnProperty.call(obj, 'toString') // false
```

上面代码中，`hasOwnProperty`是`obj`对象继承的方法，如果这个方法一旦被覆盖，就不会得到正确结果。`call`方法可以解决这个问题，它将`hasOwnProperty`方法的原始定义放到`obj`对象上执行，这样无论`obj`上有没有同名方法，都不会影响结果。

### Function.prototype.apply()

`apply`方法的作用与`call`方法类似，也是改变`this`指向，然后再调用该函数。唯一的区别就是，它接收一个数组作为函数执行时的参数，使用格式如下。

```javascript
func.apply(thisValue, [arg1, arg2, ...])
```

`apply`方法的第一个参数也是`this`所要指向的那个对象，如果设为`null`或`undefined`，则等同于指定全局对象。第二个参数则是一个数组，该数组的所有成员依次作为参数，传入原函数。原函数的参数，在`call`方法中必须一个个添加，但是在`apply`方法中，必须以数组形式添加。

```javascript
function f(x, y){
  console.log(x + y);
}

f.call(null, 1, 1) // 2
f.apply(null, [1, 1]) // 2
```

上面代码中，`f`函数本来接受两个参数，使用`apply`方法以后，就变成可以接受一个数组作为参数。

利用这一点，可以做一些有趣的应用。

**（1）找出数组最大元素**

JavaScript 不提供找出数组最大元素的函数。结合使用`apply`方法和`Math.max`方法，就可以返回数组的最大元素。

```javascript
var a = [10, 2, 4, 15, 9];
Math.max.apply(null, a) // 15
```

**（2）将数组的空元素变为`undefined`**

通过`apply`方法，利用`Array`构造函数将数组的空元素变成`undefined`。

```javascript
Array.apply(null, ['a', ,'b'])
// [ 'a', undefined, 'b' ]
```

空元素与`undefined`的差别在于，数组的`forEach`方法会跳过空元素，但是不会跳过`undefined`。因此，遍历内部元素的时候，会得到不同的结果。

```javascript
var a = ['a', , 'b'];

function print(i) {
  console.log(i);
}

a.forEach(print)
// a
// b

Array.apply(null, a).forEach(print)
// a
// undefined
// b
```

**（3）转换类似数组的对象**

另外，利用数组对象的`slice`方法，可以将一个类似数组的对象（比如`arguments`对象）转为真正的数组。

```javascript
Array.prototype.slice.apply({0: 1, length: 1}) // [1]
Array.prototype.slice.apply({0: 1}) // []
Array.prototype.slice.apply({0: 1, length: 2}) // [1, undefined]
Array.prototype.slice.apply({length: 1}) // [undefined]
```

上面代码的`apply`方法的参数都是对象，但是返回结果都是数组，这就起到了将对象转成数组的目的。从上面代码可以看到，这个方法起作用的前提是，被处理的对象必须有`length`属性，以及相对应的数字键。

**（4）绑定回调函数的对象**

前面的按钮点击事件的例子，可以改写如下。

```javascript
var o = new Object();

o.f = function () {
  console.log(this === o);
}

var f = function (){
  o.f.apply(o);
  // 或者 o.f.call(o);
};

// jQuery 的写法
$('#button').on('click', f);
```

上面代码中，点击按钮以后，控制台将会显示`true`。由于`apply()`方法（或者`call()`方法）不仅绑定函数执行时所在的对象，还会立即执行函数，因此不得不把绑定语句写在一个函数体内。更简洁的写法是采用下面介绍的`bind()`方法。

### Function.prototype.bind()

`bind()`方法用于将函数体内的`this`绑定到某个对象，然后返回一个新函数。

```javascript
var d = new Date();
d.getTime() // 1481869925657

var print = d.getTime;
print() // Uncaught TypeError: this is not a Date object.
```

上面代码中，我们将`d.getTime()`方法赋给变量`print`，然后调用`print()`就报错了。这是因为`getTime()`方法内部的`this`，绑定`Date`对象的实例，赋给变量`print`以后，内部的`this`已经不指向`Date`对象的实例了。

`bind()`方法可以解决这个问题。

```javascript
var print = d.getTime.bind(d);
print() // 1481869925657
```

上面代码中，`bind()`方法将`getTime()`方法内部的`this`绑定到`d`对象，这时就可以安全地将这个方法赋值给其他变量了。

`bind`方法的参数就是所要绑定`this`的对象，下面是一个更清晰的例子。

```javascript
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

var func = counter.inc.bind(counter);
func();
counter.count // 1
```

上面代码中，`counter.inc()`方法被赋值给变量`func`。这时必须用`bind()`方法将`inc()`内部的`this`，绑定到`counter`，否则就会出错。

`this`绑定到其他对象也是可以的。

```javascript
var counter = {
  count: 0,
  inc: function () {
    this.count++;
  }
};

var obj = {
  count: 100
};
var func = counter.inc.bind(obj);
func();
obj.count // 101
```

上面代码中，`bind()`方法将`inc()`方法内部的`this`，绑定到`obj`对象。结果调用`func`函数以后，递增的就是`obj`内部的`count`属性。

`bind()`还可以接受更多的参数，将这些参数绑定原函数的参数。

```javascript
var add = function (x, y) {
  return x * this.m + y * this.n;
}

var obj = {
  m: 2,
  n: 2
};

var newAdd = add.bind(obj, 5);
newAdd(5) // 20
```

上面代码中，`bind()`方法除了绑定`this`对象，还将`add()`函数的第一个参数`x`绑定成`5`，然后返回一个新函数`newAdd()`，这个函数只要再接受一个参数`y`就能运行了。

如果`bind()`方法的第一个参数是`null`或`undefined`，等于将`this`绑定到全局对象，函数运行时`this`指向顶层对象（浏览器为`window`）。

```javascript
function add(x, y) {
  return x + y;
}

var plus5 = add.bind(null, 5);
plus5(10) // 15
```

上面代码中，函数`add()`内部并没有`this`，使用`bind()`方法的主要目的是绑定参数`x`，以后每次运行新函数`plus5()`，就只需要提供另一个参数`y`就够了。而且因为`add()`内部没有`this`，所以`bind()`的第一个参数是`null`，不过这里如果是其他对象，也没有影响。

`bind()`方法有一些使用注意点。

**（1）每一次返回一个新函数**

`bind()`方法每运行一次，就返回一个新函数，这会产生一些问题。比如，监听事件的时候，不能写成下面这样。

```javascript
element.addEventListener('click', o.m.bind(o));
```

上面代码中，`click`事件绑定`bind()`方法生成的一个匿名函数。这样会导致无法取消绑定，所以下面的代码是无效的。

```javascript
element.removeEventListener('click', o.m.bind(o));
```

正确的方法是写成下面这样：

```javascript
var listener = o.m.bind(o);
element.addEventListener('click', listener);
//  ...
element.removeEventListener('click', listener);
```

**（2）结合回调函数使用**

回调函数是 JavaScript 最常用的模式之一，但是一个常见的错误是，将包含`this`的方法直接当作回调函数。解决方法就是使用`bind()`方法，将`counter.inc()`绑定`counter`。

```javascript
var counter = {
  count: 0,
  inc: function () {
    'use strict';
    this.count++;
  }
};

function callIt(callback) {
  callback();
}

callIt(counter.inc.bind(counter));
counter.count // 1
```

上面代码中，`callIt()`方法会调用回调函数。这时如果直接把`counter.inc`传入，调用时`counter.inc()`内部的`this`就会指向全局对象。使用`bind()`方法将`counter.inc`绑定`counter`以后，就不会有这个问题，`this`总是指向`counter`。

还有一种情况比较隐蔽，就是某些数组方法可以接受一个函数当作参数。这些函数内部的`this`指向，很可能也会出错。

```javascript
var obj = {
  name: '张三',
  times: [1, 2, 3],
  print: function () {
    this.times.forEach(function (n) {
      console.log(this.name);
    });
  }
};

obj.print()
// 没有任何输出
```

上面代码中，`obj.print`内部`this.times`的`this`是指向`obj`的，这个没有问题。但是，`forEach()`方法的回调函数内部的`this.name`却是指向全局对象，导致没有办法取到值。稍微改动一下，就可以看得更清楚。

```javascript
obj.print = function () {
  this.times.forEach(function (n) {
    console.log(this === window);
  });
};

obj.print()
// true
// true
// true
```

解决这个问题，也是通过`bind()`方法绑定`this`。

```javascript
obj.print = function () {
  this.times.forEach(function (n) {
    console.log(this.name);
  }.bind(this));
};

obj.print()
// 张三
// 张三
// 张三
```

**（3）结合`call()`方法使用**

利用`bind()`方法，可以改写一些 JavaScript 原生方法的使用形式，以数组的`slice()`方法为例。

```javascript
[1, 2, 3].slice(0, 1) // [1]
// 等同于
Array.prototype.slice.call([1, 2, 3], 0, 1) // [1]
```

上面的代码中，数组的`slice`方法从`[1, 2, 3]`里面，按照指定的开始位置和结束位置，切分出另一个数组。这样做的本质是在`[1, 2, 3]`上面调用`Array.prototype.slice()`方法，因此可以用`call`方法表达这个过程，得到同样的结果。

`call()`方法实质上是调用`Function.prototype.call()`方法，因此上面的表达式可以用`bind()`方法改写。

```javascript
var slice = Function.prototype.call.bind(Array.prototype.slice);
slice([1, 2, 3], 0, 1) // [1]
```

上面代码的含义就是，将`Array.prototype.slice`变成`Function.prototype.call`方法所在的对象，调用时就变成了`Array.prototype.slice.call`。类似的写法还可以用于其他数组方法。

```javascript
var push = Function.prototype.call.bind(Array.prototype.push);
var pop = Function.prototype.call.bind(Array.prototype.pop);

var a = [1 ,2 ,3];
push(a, 4)
a // [1, 2, 3, 4]

pop(a)
a // [1, 2, 3]
```

如果再进一步，将`Function.prototype.call`方法绑定到`Function.prototype.bind`对象，就意味着`bind`的调用形式也可以被改写。

```javascript
function f() {
  console.log(this.v);
}

var o = { v: 123 };
var bind = Function.prototype.call.bind(Function.prototype.bind);
bind(f, o)() // 123
```

上面代码的含义就是，将`Function.prototype.bind`方法绑定在`Function.prototype.call`上面，所以`bind`方法就可以直接使用，不需要在函数实例上使用。
# 3.对象的继承

面向对象编程很重要的一个方面，就是对象的继承。A 对象通过继承 B 对象，就能直接拥有 B 对象的所有属性和方法。这对于代码的复用是非常有用的。

大部分面向对象的编程语言，都是通过“类”（class）实现对象的继承。传统上，JavaScript 语言的继承不通过 class，而是通过“原型对象”（prototype）实现，本章介绍 JavaScript 的原型链继承。

ES6 引入了 class 语法，基于 class 的继承不在这个教程介绍，请参阅《ES6 标准入门》一书的相关章节。

## 原型对象概述

### 构造函数的缺点

JavaScript 通过构造函数生成新对象，因此构造函数可以视为对象的模板。实例对象的属性和方法，可以定义在构造函数内部。

```javascript
function Cat (name, color) {
  this.name = name;
  this.color = color;
}

var cat1 = new Cat('大毛', '白色');

cat1.name // '大毛'
cat1.color // '白色'
```

上面代码中，`Cat`函数是一个构造函数，函数内部定义了`name`属性和`color`属性，所有实例对象（上例是`cat1`）都会生成这两个属性，即这两个属性会定义在实例对象上面。

通过构造函数为实例对象定义属性，虽然很方便，但是有一个缺点。同一个构造函数的多个实例之间，无法共享属性，从而造成对系统资源的浪费。

```javascript
function Cat(name, color) {
  this.name = name;
  this.color = color;
  this.meow = function () {
    console.log('喵喵');
  };
}

var cat1 = new Cat('大毛', '白色');
var cat2 = new Cat('二毛', '黑色');

cat1.meow === cat2.meow
// false
```

上面代码中，`cat1`和`cat2`是同一个构造函数的两个实例，它们都具有`meow`方法。由于`meow`方法是生成在每个实例对象上面，所以两个实例就生成了两次。也就是说，每新建一个实例，就会新建一个`meow`方法。这既没有必要，又浪费系统资源，因为所有`meow`方法都是同样的行为，完全应该共享。

这个问题的解决方法，就是 JavaScript 的原型对象（prototype）。

### prototype 属性的作用

JavaScript 继承机制的设计思想就是，原型对象的所有属性和方法，都能被实例对象共享。也就是说，如果属性和方法定义在原型上，那么所有实例对象就能共享，不仅节省了内存，还体现了实例对象之间的联系。

下面，先看怎么为对象指定原型。JavaScript 规定，每个函数都有一个`prototype`属性，指向一个对象。

```javascript
function f() {}
typeof f.prototype // "object"
```

上面代码中，函数`f`默认具有`prototype`属性，指向一个对象。

对于普通函数来说，该属性基本无用。但是，对于构造函数来说，生成实例的时候，该属性会自动成为实例对象的原型。

```javascript
function Animal(name) {
  this.name = name;
}
Animal.prototype.color = 'white';

var cat1 = new Animal('大毛');
var cat2 = new Animal('二毛');

cat1.color // 'white'
cat2.color // 'white'
```

上面代码中，构造函数`Animal`的`prototype`属性，就是实例对象`cat1`和`cat2`的原型对象。原型对象上添加一个`color`属性，结果，实例对象都共享了该属性。

原型对象的属性不是实例对象自身的属性。只要修改原型对象，变动就立刻会体现在**所有**实例对象上。

```javascript
Animal.prototype.color = 'yellow';

cat1.color // "yellow"
cat2.color // "yellow"
```

上面代码中，原型对象的`color`属性的值变为`yellow`，两个实例对象的`color`属性立刻跟着变了。这是因为实例对象其实没有`color`属性，都是读取原型对象的`color`属性。也就是说，当实例对象本身没有某个属性或方法的时候，它会到原型对象去寻找该属性或方法。这就是原型对象的特殊之处。

如果实例对象自身就有某个属性或方法，它就不会再去原型对象寻找这个属性或方法。

```javascript
cat1.color = 'black';

cat1.color // 'black'
cat2.color // 'yellow'
Animal.prototype.color // 'yellow';
```

上面代码中，实例对象`cat1`的`color`属性改为`black`，就使得它不再去原型对象读取`color`属性，后者的值依然为`yellow`。

总结一下，原型对象的作用，就是定义所有实例对象共享的属性和方法。这也是它被称为原型对象的原因，而实例对象可以视作从原型对象衍生出来的子对象。

```javascript
Animal.prototype.walk = function () {
  console.log(this.name + ' is walking');
};
```

上面代码中，`Animal.prototype`对象上面定义了一个`walk`方法，这个方法将可以在所有`Animal`实例对象上面调用。

### 原型链

JavaScript 规定，所有对象都有自己的原型对象（prototype）。一方面，任何一个对象，都可以充当其他对象的原型；另一方面，由于原型对象也是对象，所以它也有自己的原型。因此，就会形成一个“原型链”（prototype chain）：对象到原型，再到原型的原型……

如果一层层地上溯，所有对象的原型最终都可以上溯到`Object.prototype`，即`Object`构造函数的`prototype`属性。也就是说，所有对象都继承了`Object.prototype`的属性。这就是所有对象都有`valueOf`和`toString`方法的原因，因为这是从`Object.prototype`继承的。

那么，`Object.prototype`对象有没有它的原型呢？回答是`Object.prototype`的原型是`null`。`null`没有任何属性和方法，也没有自己的原型。因此，原型链的尽头就是`null`。

```javascript
Object.getPrototypeOf(Object.prototype)
// null
```

上面代码表示，`Object.prototype`对象的原型是`null`，由于`null`没有任何属性，所以原型链到此为止。`Object.getPrototypeOf`方法返回参数对象的原型，具体介绍请看后文。

读取对象的某个属性时，JavaScript 引擎先寻找对象本身的属性，如果找不到，就到它的原型去找，如果还是找不到，就到原型的原型去找。如果直到最顶层的`Object.prototype`还是找不到，则返回`undefined`。如果对象自身和它的原型，都定义了一个同名属性，那么优先读取对象自身的属性，这叫做“覆盖”（overriding）。

注意，一级级向上，在整个原型链上寻找某个属性，对性能是有影响的。所寻找的属性在越上层的原型对象，对性能的影响越大。如果寻找某个不存在的属性，将会遍历整个原型链。

举例来说，如果让构造函数的`prototype`属性指向一个数组，就意味着实例对象可以调用数组方法。

```javascript
var MyArray = function () {};

MyArray.prototype = new Array();
MyArray.prototype.constructor = MyArray;

var mine = new MyArray();
mine.push(1, 2, 3);
mine.length // 3
mine instanceof Array // true
```

上面代码中，`mine`是构造函数`MyArray`的实例对象，由于`MyArray.prototype`指向一个数组实例，使得`mine`可以调用数组方法（这些方法定义在数组实例的`prototype`对象上面）。最后那行`instanceof`表达式，用来比较一个对象是否为某个构造函数的实例，结果就是证明`mine`为`Array`的实例，`instanceof`运算符的详细解释详见后文。

上面代码还出现了原型对象的`constructor`属性，这个属性的含义下一节就来解释。

### constructor 属性

`prototype`对象有一个`constructor`属性，默认指向`prototype`对象所在的构造函数。

```javascript
function P() {}
P.prototype.constructor === P // true
```

由于`constructor`属性定义在`prototype`对象上面，意味着可以被所有实例对象继承。

```javascript
function P() {}
var p = new P();

p.constructor === P // true
p.constructor === P.prototype.constructor // true
p.hasOwnProperty('constructor') // false
```

上面代码中，`p`是构造函数`P`的实例对象，但是`p`自身没有`constructor`属性，该属性其实是读取原型链上面的`P.prototype.constructor`属性。

`constructor`属性的作用是，可以得知某个实例对象，到底是哪一个构造函数产生的。

```javascript
function F() {};
var f = new F();

f.constructor === F // true
f.constructor === RegExp // false
```

上面代码中，`constructor`属性确定了实例对象`f`的构造函数是`F`，而不是`RegExp`。

另一方面，有了`constructor`属性，就可以从一个实例对象新建另一个实例。

```javascript
function Constr() {}
var x = new Constr();

var y = new x.constructor();
y instanceof Constr // true
```

上面代码中，`x`是构造函数`Constr`的实例，可以从`x.constructor`间接调用构造函数。这使得在实例方法中，调用自身的构造函数成为可能。

```javascript
Constr.prototype.createCopy = function () {
  return new this.constructor();
};
```

上面代码中，`createCopy`方法调用构造函数，新建另一个实例。

`constructor`属性表示原型对象与构造函数之间的关联关系，如果修改了原型对象，一般会同时修改`constructor`属性，防止引用的时候出错。

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.constructor === Person // true

Person.prototype = {
  method: function () {}
};

Person.prototype.constructor === Person // false
Person.prototype.constructor === Object // true
```

上面代码中，构造函数`Person`的原型对象改掉了，但是没有修改`constructor`属性，导致这个属性不再指向`Person`。由于`Person`的新原型是一个普通对象，而普通对象的`constructor`属性指向`Object`构造函数，导致`Person.prototype.constructor`变成了`Object`。

所以，修改原型对象时，一般要同时修改`constructor`属性的指向。

```javascript
// 坏的写法
C.prototype = {
  method1: function (...) { ... },
  // ...
};

// 好的写法
C.prototype = {
  constructor: C,
  method1: function (...) { ... },
  // ...
};

// 更好的写法
C.prototype.method1 = function (...) { ... };
```

上面代码中，要么将`constructor`属性重新指向原来的构造函数，要么只在原型对象上添加方法，这样可以保证`instanceof`运算符不会失真。

如果不能确定`constructor`属性是什么函数，还有一个办法：通过`name`属性，从实例得到构造函数的名称。

```javascript
function Foo() {}
var f = new Foo();
f.constructor.name // "Foo"
```

## instanceof 运算符

`instanceof`运算符返回一个布尔值，表示对象是否为某个构造函数的实例。

```javascript
var v = new Vehicle();
v instanceof Vehicle // true
```

上面代码中，对象`v`是构造函数`Vehicle`的实例，所以返回`true`。

`instanceof`运算符的左边是实例对象，右边是构造函数。它会检查右边构造函数的原型对象（prototype），是否在左边对象的原型链上。因此，下面两种写法是等价的。

```javascript
v instanceof Vehicle
// 等同于
Vehicle.prototype.isPrototypeOf(v)
```

上面代码中，`Vehicle`是对象`v`的构造函数，它的原型对象是`Vehicle.prototype`，`isPrototypeOf()`方法是 JavaScript 提供的原生方法，用于检查某个对象是否为另一个对象的原型，详细解释见后文。

由于`instanceof`检查整个原型链，因此同一个实例对象，可能会对多个构造函数都返回`true`。

```javascript
var d = new Date();
d instanceof Date // true
d instanceof Object // true
```

上面代码中，`d`同时是`Date`和`Object`的实例，因此对这两个构造函数都返回`true`。

由于任意对象（除了`null`）都是`Object`的实例，所以`instanceof`运算符可以判断一个值是否为非`null`的对象。

```javascript
var obj = { foo: 123 };
obj instanceof Object // true

null instanceof Object // false
```

上面代码中，除了`null`，其他对象的`instanceOf Object`的运算结果都是`true`。

`instanceof`的原理是检查右边构造函数的`prototype`属性，是否在左边对象的原型链上。有一种特殊情况，就是左边对象的原型链上，只有`null`对象。这时，`instanceof`判断会失真。

```javascript
var obj = Object.create(null);
typeof obj // "object"
obj instanceof Object // false
```

上面代码中，`Object.create(null)`返回一个新对象`obj`，它的原型是`null`（`Object.create()`的详细介绍见后文）。右边的构造函数`Object`的`prototype`属性，不在左边的原型链上，因此`instanceof`就认为`obj`不是`Object`的实例。这是唯一的`instanceof`运算符判断会失真的情况（一个对象的原型是`null`）。

`instanceof`运算符的一个用处，是判断值的类型。

```javascript
var x = [1, 2, 3];
var y = {};
x instanceof Array // true
y instanceof Object // true
```

上面代码中，`instanceof`运算符判断，变量`x`是数组，变量`y`是对象。

注意，`instanceof`运算符只能用于对象，不适用原始类型的值。

```javascript
var s = 'hello';
s instanceof String // false
```

上面代码中，字符串不是`String`对象的实例（因为字符串不是对象），所以返回`false`。

此外，对于`undefined`和`null`，`instanceof`运算符总是返回`false`。

```javascript
undefined instanceof Object // false
null instanceof Object // false
```

利用`instanceof`运算符，还可以巧妙地解决，调用构造函数时，忘了加`new`命令的问题。

```javascript
function Fubar (foo, bar) {
  if (this instanceof Fubar) {
    this._foo = foo;
    this._bar = bar;
  } else {
    return new Fubar(foo, bar);
  }
}
```

上面代码使用`instanceof`运算符，在函数体内部判断`this`关键字是否为构造函数`Fubar`的实例。如果不是，就表明忘了加`new`命令。

## 构造函数的继承

让一个构造函数继承另一个构造函数，是非常常见的需求。这可以分成两步实现。第一步是在子类的构造函数中，调用父类的构造函数。

```javascript
function Sub(value) {
  Super.call(this);
  this.prop = value;
}
```

上面代码中，`Sub`是子类的构造函数，`this`是子类的实例。在实例上调用父类的构造函数`Super`，就会让子类实例具有父类实例的属性。

第二步，是让子类的原型指向父类的原型，这样子类就可以继承父类原型。

```javascript
Sub.prototype = Object.create(Super.prototype);
Sub.prototype.constructor = Sub;
Sub.prototype.method = '...';
```

上面代码中，`Sub.prototype`是子类的原型，要将它赋值为`Object.create(Super.prototype)`，而不是直接等于`Super.prototype`。否则后面两行对`Sub.prototype`的操作，会连父类的原型`Super.prototype`一起修改掉。

另外一种写法是`Sub.prototype`等于一个父类实例。

```javascript
Sub.prototype = new Super();
```

上面这种写法也有继承的效果，但是子类会具有父类实例的方法。有时，这可能不是我们需要的，所以不推荐使用这种写法。

举例来说，下面是一个`Shape`构造函数。

```javascript
function Shape() {
  this.x = 0;
  this.y = 0;
}

Shape.prototype.move = function (x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};
```

我们需要让`Rectangle`构造函数继承`Shape`。

```javascript
// 第一步，子类继承父类的实例
function Rectangle() {
  Shape.call(this); // 调用父类构造函数
}
// 另一种写法
function Rectangle() {
  this.base = Shape;
  this.base();
}

// 第二步，子类继承父类的原型
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;
```

采用这样的写法以后，`instanceof`运算符会对子类和父类的构造函数，都返回`true`。

```javascript
var rect = new Rectangle();

rect instanceof Rectangle  // true
rect instanceof Shape  // true
```

上面代码中，子类是整体继承父类。有时只需要单个方法的继承，这时可以采用下面的写法。

```javascript
ClassB.prototype.print = function() {
  ClassA.prototype.print.call(this);
  // some code
}
```

上面代码中，子类`B`的`print`方法先调用父类`A`的`print`方法，再部署自己的代码。这就等于继承了父类`A`的`print`方法。

## 多重继承

JavaScript 不提供多重继承功能，即不允许一个对象同时继承多个对象。但是，可以通过变通方法，实现这个功能。

```javascript
function M1() {
  this.hello = 'hello';
}

function M2() {
  this.world = 'world';
}

function S() {
  M1.call(this);
  M2.call(this);
}

// 继承 M1
S.prototype = Object.create(M1.prototype);
// 继承链上加入 M2
Object.assign(S.prototype, M2.prototype);

// 指定构造函数
S.prototype.constructor = S;

var s = new S();
s.hello // 'hello'
s.world // 'world'
```

上面代码中，子类`S`同时继承了父类`M1`和`M2`。这种模式又称为 Mixin（混入）。

## 模块

随着网站逐渐变成“互联网应用程序”，嵌入网页的 JavaScript 代码越来越庞大，越来越复杂。网页越来越像桌面程序，需要一个团队分工协作、进度管理、单元测试等等……开发者必须使用软件工程的方法，管理网页的业务逻辑。

JavaScript 模块化编程，已经成为一个迫切的需求。理想情况下，开发者只需要实现核心的业务逻辑，其他都可以加载别人已经写好的模块。

但是，JavaScript 不是一种模块化编程语言，ES6 才开始支持“类”和“模块”。下面介绍传统的做法，如何利用对象实现模块的效果。

### 基本的实现方法

模块是实现特定功能的一组属性和方法的封装。

简单的做法是把模块写成一个对象，所有的模块成员都放到这个对象里面。

```javascript
var module1 = new Object({
　_count : 0,
　m1 : function (){
　　//...
　},
　m2 : function (){
  　//...
　}
});
```

上面的函数`m1`和`m2`，都封装在`module1`对象里。使用的时候，就是调用这个对象的属性。

```javascript
module1.m1();
```

但是，这样的写法会暴露所有模块成员，内部状态可以被外部改写。比如，外部代码可以直接改变内部计数器的值。

```javascript
module1._count = 5;
```

### 封装私有变量：构造函数的写法

我们可以利用构造函数，封装私有变量。

```javascript
function StringBuilder() {
  var buffer = [];

  this.add = function (str) {
     buffer.push(str);
  };

  this.toString = function () {
    return buffer.join('');
  };

}
```

上面代码中，`buffer`是模块的私有变量。一旦生成实例对象，外部是无法直接访问`buffer`的。但是，这种方法将私有变量封装在构造函数中，导致构造函数与实例对象是一体的，总是存在于内存之中，无法在使用完成后清除。这意味着，构造函数有双重作用，既用来塑造实例对象，又用来保存实例对象的数据，违背了构造函数与实例对象在数据上相分离的原则（即实例对象的数据，不应该保存在实例对象以外）。同时，非常耗费内存。

```javascript
function StringBuilder() {
  this._buffer = [];
}

StringBuilder.prototype = {
  constructor: StringBuilder,
  add: function (str) {
    this._buffer.push(str);
  },
  toString: function () {
    return this._buffer.join('');
  }
};
```

这种方法将私有变量放入实例对象中，好处是看上去更自然，但是它的私有变量可以从外部读写，不是很安全。

### 封装私有变量：立即执行函数的写法

另一种做法是使用“立即执行函数”（Immediately-Invoked Function Expression，IIFE），将相关的属性和方法封装在一个函数作用域里面，可以达到不暴露私有成员的目的。

```javascript
var module1 = (function () {
　var _count = 0;
　var m1 = function () {
　  //...
　};
　var m2 = function () {
　　//...
　};
　return {
　　m1 : m1,
　　m2 : m2
　};
})();
```

使用上面的写法，外部代码无法读取内部的`_count`变量。

```javascript
console.info(module1._count); //undefined
```

上面的`module1`就是 JavaScript 模块的基本写法。下面，再对这种写法进行加工。

### 模块的放大模式

如果一个模块很大，必须分成几个部分，或者一个模块需要继承另一个模块，这时就有必要采用“放大模式”（augmentation）。

```javascript
var module1 = (function (mod){
　mod.m3 = function () {
　　//...
　};
　return mod;
})(module1);
```

上面的代码为`module1`模块添加了一个新方法`m3()`，然后返回新的`module1`模块。

在浏览器环境中，模块的各个部分通常都是从网上获取的，有时无法知道哪个部分会先加载。如果采用上面的写法，第一个执行的部分有可能加载一个不存在空对象，这时就要采用"宽放大模式"（Loose augmentation）。

```javascript
var module1 = (function (mod) {
　//...
　return mod;
})(window.module1 || {});
```

与"放大模式"相比，“宽放大模式”就是“立即执行函数”的参数可以是空对象。

### 输入全局变量

独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。

为了在模块内部调用全局变量，必须显式地将其他变量输入模块。

```javascript
var module1 = (function ($, YAHOO) {
　//...
})(jQuery, YAHOO);
```

上面的`module1`模块需要使用 jQuery 库和 YUI 库，就把这两个库（其实是两个模块）当作参数输入`module1`。这样做除了保证模块的独立性，还使得模块之间的依赖关系变得明显。

立即执行函数还可以起到命名空间的作用。

```javascript
(function($, window, document) {

  function go(num) {
  }

  function handleEvents() {
  }

  function initialize() {
  }

  function dieCarouselDie() {
  }

  //attach to the global scope
  window.finalCarousel = {
    init : initialize,
    destroy : dieCarouselDie
  }

})( jQuery, window, document );
```

上面代码中，`finalCarousel`对象输出到全局，对外暴露`init`和`destroy`接口，内部方法`go`、`handleEvents`、`initialize`、`dieCarouselDie`都是外部无法调用的。

# 4.Object 对象的相关方法

JavaScript 在`Object`对象上面，提供了很多相关方法，处理面向对象编程的相关操作。本章介绍这些方法。

## Object.getPrototypeOf()

`Object.getPrototypeOf`方法返回参数对象的原型。这是获取原型对象的标准方法。

```javascript
var F = function () {};
var f = new F();
Object.getPrototypeOf(f) === F.prototype // true
```

上面代码中，实例对象`f`的原型是`F.prototype`。

下面是几种特殊对象的原型。

```javascript
// 空对象的原型是 Object.prototype
Object.getPrototypeOf({}) === Object.prototype // true

// Object.prototype 的原型是 null
Object.getPrototypeOf(Object.prototype) === null // true

// 函数的原型是 Function.prototype
function f() {}
Object.getPrototypeOf(f) === Function.prototype // true
```

## Object.setPrototypeOf()

`Object.setPrototypeOf`方法为参数对象设置原型，返回该参数对象。它接受两个参数，第一个是现有对象，第二个是原型对象。

```javascript
var a = {};
var b = {x: 1};
Object.setPrototypeOf(a, b);

Object.getPrototypeOf(a) === b // true
a.x // 1
```

上面代码中，`Object.setPrototypeOf`方法将对象`a`的原型，设置为对象`b`，因此`a`可以共享`b`的属性。

`new`命令可以使用`Object.setPrototypeOf`方法模拟。

```javascript
var F = function () {
  this.foo = 'bar';
};

var f = new F();
// 等同于
var f = Object.setPrototypeOf({}, F.prototype);
F.call(f);
```

上面代码中，`new`命令新建实例对象，其实可以分成两步。第一步，将一个空对象的原型设为构造函数的`prototype`属性（上例是`F.prototype`）；第二步，将构造函数内部的`this`绑定这个空对象，然后执行构造函数，使得定义在`this`上面的方法和属性（上例是`this.foo`），都转移到这个空对象上。

## Object.create()

生成实例对象的常用方法是，使用`new`命令让构造函数返回一个实例。但是很多时候，只能拿到一个实例对象，它可能根本不是由构建函数生成的，那么能不能从一个实例对象，生成另一个实例对象呢？

JavaScript 提供了`Object.create()`方法，用来满足这种需求。该方法接受一个对象作为参数，然后以它为原型，返回一个实例对象。该实例完全继承原型对象的属性。

```javascript
// 原型对象
var A = {
  print: function () {
    console.log('hello');
  }
};

// 实例对象
var B = Object.create(A);

Object.getPrototypeOf(B) === A // true
B.print() // hello
B.print === A.print // true
```

上面代码中，`Object.create()`方法以`A`对象为原型，生成了`B`对象。`B`继承了`A`的所有属性和方法。

实际上，`Object.create()`方法可以用下面的代码代替。

```javascript
if (typeof Object.create !== 'function') {
  Object.create = function (obj) {
    function F() {}
    F.prototype = obj;
    return new F();
  };
}
```

上面代码表明，`Object.create()`方法的实质是新建一个空的构造函数`F`，然后让`F.prototype`属性指向参数对象`obj`，最后返回一个`F`的实例，从而实现让该实例继承`obj`的属性。

下面三种方式生成的新对象是等价的。

```javascript
var obj1 = Object.create({});
var obj2 = Object.create(Object.prototype);
var obj3 = new Object();
```

如果想要生成一个不继承任何属性（比如没有`toString()`和`valueOf()`方法）的对象，可以将`Object.create()`的参数设为`null`。

```javascript
var obj = Object.create(null);

obj.valueOf()
// TypeError: Object [object Object] has no method 'valueOf'
```

上面代码中，对象`obj`的原型是`null`，它就不具备一些定义在`Object.prototype`对象上面的属性，比如`valueOf()`方法。

使用`Object.create()`方法的时候，必须提供对象原型，即参数不能为空，或者不是对象，否则会报错。

```javascript
Object.create()
// TypeError: Object prototype may only be an Object or null
Object.create(123)
// TypeError: Object prototype may only be an Object or null
```

`Object.create()`方法生成的新对象，动态继承了原型。在原型上添加或修改任何方法，会立刻反映在新对象之上。

```javascript
var obj1 = { p: 1 };
var obj2 = Object.create(obj1);

obj1.p = 2;
obj2.p // 2
```

上面代码中，修改对象原型`obj1`会影响到实例对象`obj2`。

除了对象的原型，`Object.create()`方法还可以接受第二个参数。该参数是一个属性描述对象，它所描述的对象属性，会添加到实例对象，作为该对象自身的属性。

```javascript
var obj = Object.create({}, {
  p1: {
    value: 123,
    enumerable: true,
    configurable: true,
    writable: true,
  },
  p2: {
    value: 'abc',
    enumerable: true,
    configurable: true,
    writable: true,
  }
});

// 等同于
var obj = Object.create({});
obj.p1 = 123;
obj.p2 = 'abc';
```

`Object.create()`方法生成的对象，继承了它的原型对象的构造函数。

```javascript
function A() {}
var a = new A();
var b = Object.create(a);

b.constructor === A // true
b instanceof A // true
```

上面代码中，`b`对象的原型是`a`对象，因此继承了`a`对象的构造函数`A`。

## Object.prototype.isPrototypeOf()

实例对象的`isPrototypeOf`方法，用来判断该对象是否为参数对象的原型。

```javascript
var o1 = {};
var o2 = Object.create(o1);
var o3 = Object.create(o2);

o2.isPrototypeOf(o3) // true
o1.isPrototypeOf(o3) // true
```

上面代码中，`o1`和`o2`都是`o3`的原型。这表明只要实例对象处在参数对象的原型链上，`isPrototypeOf`方法都返回`true`。

```javascript
Object.prototype.isPrototypeOf({}) // true
Object.prototype.isPrototypeOf([]) // true
Object.prototype.isPrototypeOf(/xyz/) // true
Object.prototype.isPrototypeOf(Object.create(null)) // false
```

上面代码中，由于`Object.prototype`处于原型链的最顶端，所以对各种实例都返回`true`，只有直接继承自`null`的对象除外。

## Object.prototype.\_\_proto\_\_

实例对象的`__proto__`属性（前后各两个下划线），返回该对象的原型。该属性可读写。

```javascript
var obj = {};
var p = {};

obj.__proto__ = p;
Object.getPrototypeOf(obj) === p // true
```

上面代码通过`__proto__`属性，将`p`对象设为`obj`对象的原型。

根据语言标准，`__proto__`属性只有浏览器才需要部署，其他环境可以没有这个属性。它前后的两根下划线，表明它本质是一个内部属性，不应该对使用者暴露。因此，应该尽量少用这个属性，而是用`Object.getPrototypeOf()`和`Object.setPrototypeOf()`，进行原型对象的读写操作。

原型链可以用`__proto__`很直观地表示。

```javascript
var A = {
  name: '张三'
};
var B = {
  name: '李四'
};

var proto = {
  print: function () {
    console.log(this.name);
  }
};

A.__proto__ = proto;
B.__proto__ = proto;

A.print() // 张三
B.print() // 李四

A.print === B.print // true
A.print === proto.print // true
B.print === proto.print // true
```

上面代码中，`A`对象和`B`对象的原型都是`proto`对象，它们都共享`proto`对象的`print`方法。也就是说，`A`和`B`的`print`方法，都是在调用`proto`对象的`print`方法。

## 获取原型对象方法的比较

如前所述，`__proto__`属性指向当前对象的原型对象，即构造函数的`prototype`属性。

```javascript
var obj = new Object();

obj.__proto__ === Object.prototype
// true
obj.__proto__ === obj.constructor.prototype
// true
```

上面代码首先新建了一个对象`obj`，它的`__proto__`属性，指向构造函数（`Object`或`obj.constructor`）的`prototype`属性。

因此，获取实例对象`obj`的原型对象，有三种方法。

- `obj.__proto__`
- `obj.constructor.prototype`
- `Object.getPrototypeOf(obj)`

上面三种方法之中，前两种都不是很可靠。`__proto__`属性只有浏览器才需要部署，其他环境可以不部署。而`obj.constructor.prototype`在手动改变原型对象时，可能会失效。

```javascript
var P = function () {};
var p = new P();

var C = function () {};
C.prototype = p;
var c = new C();

c.constructor.prototype === p // false
```

上面代码中，构造函数`C`的原型对象被改成了`p`，但是实例对象的`c.constructor.prototype`却没有指向`p`。所以，在改变原型对象时，一般要同时设置`constructor`属性。

```javascript
C.prototype = p;
C.prototype.constructor = C;

var c = new C();
c.constructor.prototype === p // true
```

因此，推荐使用第三种`Object.getPrototypeOf`方法，获取原型对象。

## Object.getOwnPropertyNames()

`Object.getOwnPropertyNames`方法返回一个数组，成员是参数对象本身的所有属性的键名，不包含继承的属性键名。

```javascript
Object.getOwnPropertyNames(Date)
// ["parse", "arguments", "UTC", "caller", "name", "prototype", "now", "length"]
```

上面代码中，`Object.getOwnPropertyNames`方法返回`Date`所有自身的属性名。

对象本身的属性之中，有的是可以遍历的（enumerable），有的是不可以遍历的。`Object.getOwnPropertyNames`方法返回所有键名，不管是否可以遍历。只获取那些可以遍历的属性，使用`Object.keys`方法。

```javascript
Object.keys(Date) // []
```

上面代码表明，`Date`对象所有自身的属性，都是不可以遍历的。

## Object.prototype.hasOwnProperty()

对象实例的`hasOwnProperty`方法返回一个布尔值，用于判断某个属性定义在对象自身，还是定义在原型链上。

```javascript
Date.hasOwnProperty('length') // true
Date.hasOwnProperty('toString') // false
```

上面代码表明，`Date.length`（构造函数`Date`可以接受多少个参数）是`Date`自身的属性，`Date.toString`是继承的属性。

另外，`hasOwnProperty`方法是 JavaScript 之中唯一一个处理对象属性时，不会遍历原型链的方法。

## in 运算符和 for...in 循环

`in`运算符返回一个布尔值，表示一个对象是否具有某个属性。它不区分该属性是对象自身的属性，还是继承的属性。

```javascript
'length' in Date // true
'toString' in Date // true
```

`in`运算符常用于检查一个属性是否存在。

获得对象的所有可遍历属性（不管是自身的还是继承的），可以使用`for...in`循环。

```javascript
var o1 = { p1: 123 };

var o2 = Object.create(o1, {
  p2: { value: "abc", enumerable: true }
});

for (p in o2) {
  console.info(p);
}
// p2
// p1
```

上面代码中，对象`o2`的`p2`属性是自身的，`p1`属性是继承的。这两个属性都会被`for...in`循环遍历。

为了在`for...in`循环中获得对象自身的属性，可以采用`hasOwnProperty`方法判断一下。

```javascript
for ( var name in object ) {
  if ( object.hasOwnProperty(name) ) {
    /* loop code */
  }
}
```

获得对象的所有属性（不管是自身的还是继承的，也不管是否可枚举），可以使用下面的函数。

```javascript
function inheritedPropertyNames(obj) {
  var props = {};
  while(obj) {
    Object.getOwnPropertyNames(obj).forEach(function(p) {
      props[p] = true;
    });
    obj = Object.getPrototypeOf(obj);
  }
  return Object.getOwnPropertyNames(props);
}
```

上面代码依次获取`obj`对象的每一级原型对象“自身”的属性，从而获取`obj`对象的“所有”属性，不管是否可遍历。

下面是一个例子，列出`Date`对象的所有属性。

```javascript
inheritedPropertyNames(Date)
// [
//  "caller",
//  "constructor",
//  "toString",
//  "UTC",
//  ...
// ]
```

## 对象的拷贝

如果要拷贝一个对象，需要做到下面两件事情。

- 确保拷贝后的对象，与原对象具有同样的原型。
- 确保拷贝后的对象，与原对象具有同样的实例属性。

下面就是根据上面两点，实现的对象拷贝函数。

```javascript
function copyObject(orig) {
  var copy = Object.create(Object.getPrototypeOf(orig));
  copyOwnPropertiesFrom(copy, orig);
  return copy;
}

function copyOwnPropertiesFrom(target, source) {
  Object
    .getOwnPropertyNames(source)
    .forEach(function (propKey) {
      var desc = Object.getOwnPropertyDescriptor(source, propKey);
      Object.defineProperty(target, propKey, desc);
    });
  return target;
}
```

另一种更简单的写法，是利用 ES2017 才引入标准的`Object.getOwnPropertyDescriptors`方法。

```javascript
function copyObject(orig) {
  return Object.create(
    Object.getPrototypeOf(orig),
    Object.getOwnPropertyDescriptors(orig)
  );
}
```
# 5.严格模式

除了正常的运行模式，JavaScript 还有第二种运行模式：严格模式（strict mode）。顾名思义，这种模式采用更加严格的 JavaScript 语法。

同样的代码，在正常模式和严格模式中，可能会有不一样的运行结果。一些在正常模式下可以运行的语句，在严格模式下将不能运行。

## 设计目的

早期的 JavaScript 语言有很多设计不合理的地方，但是为了兼容以前的代码，又不能改变老的语法，只能不断添加新的语法，引导程序员使用新语法。

严格模式是从 ES5 进入标准的，主要目的有以下几个。

- 明确禁止一些不合理、不严谨的语法，减少 JavaScript 语言的一些怪异行为。
- 增加更多报错的场合，消除代码运行的一些不安全之处，保证代码运行的安全。
- 提高编译器效率，增加运行速度。
- 为未来新版本的 JavaScript 语法做好铺垫。

总之，严格模式体现了 JavaScript 更合理、更安全、更严谨的发展方向。

## 启用方法

进入严格模式的标志，是一行字符串`use strict`。

```javascript
'use strict';
```

老版本的引擎会把它当作一行普通字符串，加以忽略。新版本的引擎就会进入严格模式。

严格模式可以用于整个脚本，也可以只用于单个函数。

**（1） 整个脚本文件**

`use strict`放在脚本文件的第一行，整个脚本都将以严格模式运行。如果这行语句不在第一行就无效，整个脚本会以正常模式运行。(严格地说，只要前面不是产生实际运行结果的语句，`use strict`可以不在第一行，比如直接跟在一个空的分号后面，或者跟在注释后面。)

```html
<script>
  'use strict';
  console.log('这是严格模式');
</script>

<script>
  console.log('这是正常模式');
</script>
```

上面代码中，一个网页文件依次有两段 JavaScript 代码。前一个`<script>`标签是严格模式，后一个不是。

如果`use strict`写成下面这样，则不起作用，严格模式必须从代码一开始就生效。

```html
<script>
  console.log('这是正常模式');
  'use strict';
</script>
```

**（2）单个函数**

`use strict`放在函数体的第一行，则整个函数以严格模式运行。

```javascript
function strict() {
  'use strict';
  return '这是严格模式';
}

function strict2() {
  'use strict';
  function f() {
    return '这也是严格模式';
  }
  return f();
}

function notStrict() {
  return '这是正常模式';
}
```

有时，需要把不同的脚本合并在一个文件里面。如果一个脚本是严格模式，另一个脚本不是，它们的合并就可能出错。严格模式的脚本在前，则合并后的脚本都是严格模式；如果正常模式的脚本在前，则合并后的脚本都是正常模式。这两种情况下，合并后的结果都是不正确的。这时可以考虑把整个脚本文件放在一个立即执行的匿名函数之中。

```javascript
(function () {
  'use strict';
  // some code here
})();
```

## 显式报错

严格模式使得 JavaScript 的语法变得更严格，更多的操作会显式报错。其中有些操作，在正常模式下只会默默地失败，不会报错。

### 只读属性不可写

严格模式下，设置字符串的`length`属性，会报错。

```javascript
'use strict';
'abc'.length = 5;
// TypeError: Cannot assign to read only property 'length' of string 'abc'
```

上面代码报错，因为`length`是只读属性，严格模式下不可写。正常模式下，改变`length`属性是无效的，但不会报错。

严格模式下，对只读属性赋值，或者删除不可配置（non-configurable）属性都会报错。

```javascript
// 对只读属性赋值会报错
'use strict';
Object.defineProperty({}, 'a', {
  value: 37,
  writable: false
});
obj.a = 123;
// TypeError: Cannot assign to read only property 'a' of object #<Object>

// 删除不可配置的属性会报错
'use strict';
var obj = Object.defineProperty({}, 'p', {
  value: 1,
  configurable: false
});
delete obj.p
// TypeError: Cannot delete property 'p' of #<Object>
```

### 只设置了取值器的属性不可写

严格模式下，对一个只有取值器（getter）、没有存值器（setter）的属性赋值，会报错。

```javascript
'use strict';
var obj = {
  get v() { return 1; }
};
obj.v = 2;
// Uncaught TypeError: Cannot set property v of #<Object> which has only a getter
```

上面代码中，`obj.v`只有取值器，没有存值器，对它进行赋值就会报错。

### 禁止扩展的对象不可扩展

严格模式下，对禁止扩展的对象添加新属性，会报错。

```javascript
'use strict';
var obj = {};
Object.preventExtensions(obj);
obj.v = 1;
// Uncaught TypeError: Cannot add property v, object is not extensible
```

上面代码中，`obj`对象禁止扩展，添加属性就会报错。

### eval、arguments 不可用作标识名

严格模式下，使用`eval`或者`arguments`作为标识名，将会报错。下面的语句都会报错。

```javascript
'use strict';
var eval = 17;
var arguments = 17;
var obj = { set p(arguments) { } };
try { } catch (arguments) { }
function x(eval) { }
function arguments() { }
var y = function eval() { };
var f = new Function('arguments', "'use strict'; return 17;");
// SyntaxError: Unexpected eval or arguments in strict mode
```

### 函数不能有重名的参数

正常模式下，如果函数有多个重名的参数，可以用`arguments[i]`读取。严格模式下，这属于语法错误。

```javascript
function f(a, a, b) {
  'use strict';
  return a + b;
}
// Uncaught SyntaxError: Duplicate parameter name not allowed in this context
```

### 禁止八进制的前缀0表示法

正常模式下，整数的第一位如果是`0`，表示这是八进制数，比如`0100`等于十进制的64。严格模式禁止这种表示法，整数第一位为`0`，将报错。

```javascript
'use strict';
var n = 0100;
// Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```

## 增强的安全措施

严格模式增强了安全保护，从语法上防止了一些不小心会出现的错误。

### 全局变量显式声明

正常模式中，如果一个变量没有声明就赋值，默认是全局变量。严格模式禁止这种用法，全局变量必须显式声明。

```javascript
'use strict';

v = 1; // 报错，v未声明

for (i = 0; i < 2; i++) { // 报错，i 未声明
  // ...
}

function f() {
  x = 123;
}
f() // 报错，未声明就创建一个全局变量
```

因此，严格模式下，变量都必须先声明，然后再使用。

### 禁止 this 关键字指向全局对象

正常模式下，函数内部的`this`可能会指向全局对象，严格模式禁止这种用法，避免无意间创造全局变量。

```javascript
// 正常模式
function f() {
  console.log(this === window);
}
f() // true

// 严格模式
function f() {
  'use strict';
  console.log(this === undefined);
}
f() // true
```

上面代码中，严格模式的函数体内部`this`是`undefined`。

这种限制对于构造函数尤其有用。使用构造函数时，有时忘了加`new`，这时`this`不再指向全局对象，而是报错。

```javascript
function f() {
  'use strict';
  this.a = 1;
};

f();// 报错，this 未定义
```

严格模式下，函数直接调用时（不使用`new`调用），函数内部的`this`表示`undefined`（未定义），因此可以用`call`、`apply`和`bind`方法，将任意值绑定在`this`上面。正常模式下，`this`指向全局对象，如果绑定的值是非对象，将被自动转为对象再绑定上去，而`null`和`undefined`这两个无法转成对象的值，将被忽略。

```javascript
// 正常模式
function fun() {
  return this;
}

fun() // window
fun.call(2) // Number {2}
fun.call(true) // Boolean {true}
fun.call(null) // window
fun.call(undefined) // window

// 严格模式
'use strict';
function fun() {
  return this;
}

fun() //undefined
fun.call(2) // 2
fun.call(true) // true
fun.call(null) // null
fun.call(undefined) // undefined
```

上面代码中，可以把任意类型的值，绑定在`this`上面。

### 禁止使用 fn.callee、fn.caller

函数内部不得使用`fn.caller`、`fn.arguments`，否则会报错。这意味着不能在函数内部得到调用栈了。

```javascript
function f1() {
  'use strict';
  f1.caller;    // 报错
  f1.arguments; // 报错
}

f1();
```

### 禁止使用 arguments.callee、arguments.caller

`arguments.callee`和`arguments.caller`是两个历史遗留的变量，从来没有标准化过，现在已经取消了。正常模式下调用它们没有什么作用，但是不会报错。严格模式明确规定，函数内部使用`arguments.callee`、`arguments.caller`将会报错。

```javascript
'use strict';
var f = function () {
  return arguments.callee;
};

f(); // 报错
```

### 禁止删除变量

严格模式下无法删除变量，如果使用`delete`命令删除一个变量，会报错。只有对象的属性，且属性的描述对象的`configurable`属性设置为`true`，才能被`delete`命令删除。

```javascript
'use strict';
var x;
delete x; // 语法错误

var obj = Object.create(null, {
  x: {
    value: 1,
    configurable: true
  }
});
delete obj.x; // 删除成功
```

## 静态绑定

JavaScript 语言的一个特点，就是允许“动态绑定”，即某些属性和方法到底属于哪一个对象，不是在编译时确定的，而是在运行时（runtime）确定的。

严格模式对动态绑定做了一些限制。某些情况下，只允许静态绑定。也就是说，属性和方法到底归属哪个对象，必须在编译阶段就确定。这样做有利于编译效率的提高，也使得代码更容易阅读，更少出现意外。

具体来说，涉及以下几个方面。

### 禁止使用 with 语句

严格模式下，使用`with`语句将报错。因为`with`语句无法在编译时就确定，某个属性到底归属哪个对象，从而影响了编译效果。

```javascript
'use strict';
var v  = 1;
var obj = {};

with (obj) {
  v = 2;
}
// Uncaught SyntaxError: Strict mode code may not include a with statement
```

### 创设 eval 作用域

正常模式下，JavaScript 语言有两种变量作用域（scope）：全局作用域和函数作用域。严格模式创设了第三种作用域：`eval`作用域。

正常模式下，`eval`语句的作用域，取决于它处于全局作用域，还是函数作用域。严格模式下，`eval`语句本身就是一个作用域，不再能够在其所运行的作用域创设新的变量了，也就是说，`eval`所生成的变量只能用于`eval`内部。

```javascript
(function () {
  'use strict';
  var x = 2;
  console.log(eval('var x = 5; x')) // 5
  console.log(x) // 2
})()
```

上面代码中，由于`eval`语句内部是一个独立作用域，所以内部的变量`x`不会泄露到外部。

注意，如果希望`eval`语句也使用严格模式，有两种方式。

```javascript
// 方式一
function f1(str){
  'use strict';
  return eval(str);
}
f1('undeclared_variable = 1'); // 报错

// 方式二
function f2(str){
  return eval(str);
}
f2('"use strict";undeclared_variable = 1')  // 报错
```

上面两种写法，`eval`内部使用的都是严格模式。

### arguments 不再追踪参数的变化

变量`arguments`代表函数的参数。严格模式下，函数内部改变参数与`arguments`的联系被切断了，两者不再存在联动关系。

```javascript
function f(a) {
  a = 2;
  return [a, arguments[0]];
}
f(1); // 正常模式为[2, 2]

function f(a) {
  'use strict';
  a = 2;
  return [a, arguments[0]];
}
f(1); // 严格模式为[2, 1]
```

上面代码中，改变函数的参数，不会反应到`arguments`对象上来。

## 向下一个版本的 JavaScript 过渡

JavaScript 语言的下一个版本是 ECMAScript 6，为了平稳过渡，严格模式引入了一些 ES6 语法。

### 非函数代码块不得声明函数

ES6 会引入块级作用域。为了与新版本接轨，ES5 的严格模式只允许在全局作用域或函数作用域声明函数。也就是说，不允许在非函数的代码块内声明函数。

```javascript
'use strict';
if (true) {
  function f1() { } // 语法错误
}

for (var i = 0; i < 5; i++) {
  function f2() { } // 语法错误
}
```

上面代码在`if`代码块和`for`代码块中声明了函数，ES5 环境会报错。

注意，如果是 ES6 环境，上面的代码不会报错，因为 ES6 允许在代码块之中声明函数。

### 保留字

为了向将来 JavaScript 的新版本过渡，严格模式新增了一些保留字（implements、interface、let、package、private、protected、public、static、yield等）。使用这些词作为变量名将会报错。

```javascript
function package(protected) { // 语法错误
  'use strict';
  var implements; // 语法错误
}
```
