## 第 10 章  函数

在 JS 中函数实际上也是对象。每个函数都是 `Function` 类型的实例，而 `Function` 也有属性和方法。

函数是对象，那函数名就是指向函数对象的指针，而且不一定与函数本身紧密绑定

函数定义的方式：

> 函数声明（没有分号）：
>
> ```javascript
> function sum (num1, num2) {
>     return num1 + num2;
> }
> ```
>
> 函数表达式（有分号）：
>
> ```javascript
> let sum = function (num1, num2) {
>     return num1 + num2;
> }; // 是因为相当于一个赋值操作所以要加分号吗？
> ```
>
> 箭头函数：
>
> ```javascript
> let sum = (num1, num2) => { return num1 + num2; };
> ```
>
> 构造函数（不推荐）：
>
> ```javascript
> let num = new Function ('num1', 'num2', 'num1 + num2')
> ```
>
> 使用构造函数定义，这段代码会被解释两次，第一次当做常规 ECMAScript 代码，第二次是解释传给构造函数的字符串。会影响性能。

### 10.1 箭头函数

ES6新增，很大程度上，箭头函数实例化的对象与正式的函数表达式创建的函数对象行为是相同的。任何可以使用函数表达式的地方都可以使用箭头函数。

```javascript
let arrowSum = (a, b) => {
    return a * b;
};

let functionExpressionSum  = function (a, b) {
    return a * b;
};

console.log(arrowSum(1, 2));
console.log(functionExpressionSum(1, 2));
```

箭头函数非常适合嵌入函数的场景：

```javascript
let ints = [1, 2, 3];
console.log(ints.map((i) => { return i + 1 })); // [2, 3, 4]
```

如果只有一个参数可以不要括号，没有参数或有多个参数时一定要加括号

使用大括号说明包含“函数体“，可以在一个函数里包含多条语句。如果不使用大括号，那箭头后面只能有一行代码，比如一个赋值操作或一个表达式

```javascript
// 都有效
let double = (x) => { return 2 *x };
let triple = (x) => 3 * x;

console.log(double(1));
console.log(triple(1));

// 赋值
let obj = {};
let setName = (arg) => arg.name = 'poo';
setName(obj)
console.log(obj.name); // poo

// 无效的写法
let multiply = (a, b) => return a * b;
console.log(multiply(1, 2)); 
// Uncaught SyntaxError: Unexpected token 'return'
// 是return一定要在函数体里吗？
```

箭头函数虽然语法简洁，但箭头函数不能使用 `arguments`, `super` 和`new.target`，也不能用作构造函数，此外，箭头函数也没有 `prototype` 属性。

### 10.2 函数名

函数名就是指向函数的指针，和其他包含对象指针的变量具有相同的行为。这也意味着一个函数可以有多个名称

```javascript
function sum (num1, num2) {
    return num1 * num2;
}
let anotherSum = sum; // 访问的是函数指针 不会执行函数

console.log(sum(1, 2)); // 2
console.log(anotherSum(1, 2)); // 2

sum = null;
console.log(anotherSum(1, 2)); // 2
console.log(sum(1, 2)); // 报错 Uncaught TypeError: sum is not a function
```

ES6 中的所有函数对象都会暴露一个只读的 `name` 属性，其中包含关于函数的信息。多数情况下就是一个函数标识符（一个字符串化的变量名），即使函数没有名称，也会如实显示成空字符串。

如果是使用 `Function` 构造函数创建的，则会标识成 `'anonymous'` ：

```javascript
function foo () {}
let bar = () => {};
let baz = function () {};
let das = new Function (); // 会报错 不是代码的问题
// 

console.log(foo.name); // 'foo'
console.log(bar.name); // 'bar'
console.log(baz.name); // 'baz'
console.log(das.name); // 'anonymous'
```

> 即使攻击者无法直接注入脚本，他们也会欺骗您的应用将不活动文本转换为可执行的 JavaScript，并代表他们执行它。 `eval()`、`new Function()`、 `setTimeout([string], ...)` 和 `setInterval([string], ...)` 都是矢量，通过它们注入文本最终会导致执行一些意外的恶意行为。CSP 对于此风险的默认响应是完全阻止所有这些矢量。
>
> 这对您构建应用的方式有不小的影响。
>
> - 您必须通过内置 `JSON.parse` 解析 JSON，而不是依靠 `eval` 来解析。 原生 JSON 操作在 [IE8 及以上版本的每个浏览器](http://caniuse.com/#feat=json) 中均可用，并且十分安全。
> - 使用内联函数（而不是字符串）重写您当前正在进行的任何 `setTimeout` 或 `setInterval` 调用。 例如:

如果函数是一个获取函数、设置函数，或者使用 `bind()` 实例化，那么标识符前面会加上一个前缀：

```javascript
function foo () {}
console.log(foo.bind(null).name); // 'bound foo'

let dog = {
    years: 1,
    get age () {
        return this.age;
    },
    set age (newAge) {
        this.years = newAge;
    }
}

let propertyDescriptor = Object.getOwnPropertyDescriptor(dog, 'age');
console.log(propertyDescriptor.get.name); // 'get age'
console.log(propertyDescriptor.set.name); // 'set age'
```

> 获取函数getter：
>
> **`get`**语法将对象属性绑定到查询该属性时将被调用的函数。
>
> 是不是类似于Python里的 `@property` 装饰器，负责把一个方法变成属性调用
>
> 设置函数setter：
>
> 当尝试设置属性时，**`set`**语法将对象属性绑定到要调用的函数。
>
> Python里的 `@xxx.setter`，负责把一个setter方法变成属性赋值
>
> 
>
> `Function.prototype.bind()`:
>
> `bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
>
> ```javascript
> const module = {
>   x: 42,
>   getX: function() {
>     return this.x;
>   }
> };
> 
> const unboundGetX = module.getX; 
> console.log(unboundGetX()); // The function gets invoked at the global scope
> // expected output: undefined
> 
> const boundGetX = unboundGetX.bind(module);
> console.log(boundGetX());
> // expected output: 42
> ```
>
> `unboundGetX()` 没有明确指定 `tihs` 的值
>
> ```javascript
> this.x = 9;    // 在浏览器中，this 指向全局的 "window" 对象
> var module = {
>   x: 81,
>   getX: function() { return this.x; }
> };
> 
> module.getX(); // 81
> 
> var retrieveX = module.getX;
> retrieveX();
> // 返回 9 - 因为函数是在全局作用域中调用的
> 
> // 创建一个新函数，把 'this' 绑定到 module 对象
> // 新手可能会将全局变量 x 与 module 的属性 x 混淆
> var boundGetX = retrieveX.bind(module);
> boundGetX(); // 81
> ```

### 10.3 理解参数

ECMAScript的参数跟大多数语言的不同，ECMAScript函数既不关心传入的参数的个数，也不关心这些参数的数据类型。

定义函数时传入两个参数，并不意味着调用的时候就必须传入两个参数，可以一个不传入，或者传入任意个参数，解释器都不会报错

之所以这样，是因为ECMAScript函数的参数在内部表现为一个数组。函数在调用的时候总是会接受一个数组，但函数并不会关心数组中包含什么。

在使用 `function` 关键字定义（非箭头）函数时，可以在函数内部访问 `arguments` 对象，从中取得传进来的每个参数值。

`arguments` 对象是一个类数组对象（不是 `Array` 实例），但除了length属性和索引元素之外没有任何 `Array` 属性。例如，它没有 pop 方法。但是它可以被转换为一个真正的 `Array`，可以使用中括号访问其中的元素，`arguments.length` 可以知道传进来几个参数。

```javascript 
function sayHi (name, message) {
    console.log('Hi ' + name + ', ' + message);
}
function sayHello () {
    console.log('Hello ' + arguments[0] + ', ' + arguments[1])
}
sayHi('poo', 'poop'); // Hi poo, poop
sayHello('poo', 'poop'); // Hello poo, poop
```

```javascript
// 写成不声明参数也可以
let sum = function () { 
    return arguments[0] + arguments[1];
};
sum(); // NaN
sum(1); // NaN
sum(hello); // helloundefined
```

可以通过检查参数的类型和数量，然后分别执行不同的逻辑来模拟函数重载：

```javascript
function doAdd () {
    if (arguments.length === 1) {
        console.log(arguments[0] + 10);
    } else {
        console.log(arguments[0] + arguments[1]);
    }
}

doAdd(10); // 20
doAdd(10, 20); // 30
```

`arguments` 对象可以跟命名参数一起使用：

```javascript
function doAdd (num1, num2) {
    if (arguments.length === 1) {
        console.log(num1 + 10);
    } else if (arguments.length === 2) {
        console.log(arguments[0] + num2);
    }
}
doAdd(10); // 20
doAdd(10, 20); // 30
```

`num1` 保存着和 `arguments[0]` 一样的值，`num2` 保存着和 `arguments[1]` 一样的值。

`arguments` 对象的值始终会与对用的命名参数同步：

```javascript
function doAdd (num1, num2) {
    arguments[0] = 20;
    console.log(`${ num1 } + ${ num2 } = ${ num1 + num2 }`);
} 
doAdd(10, 10); // 20 + 10 = 30
```

修改 `arguments[0]` 的值也会修改 `num1` 的值，但是这并不意味着它们都访问同一个内存地址，它们在地址中还是分开的，只不过会保持同步而已。还有就是如果只穿了一个参数，然后把 `arguments[1]` 设置为某个值，那这个值并不会反映到第二个命名参数，因为 `arguments` 对象的长度是由传入的参数的个数决定的，而不是定义函数时给出的命名参数的个数。

```javascript
function doMul (num1, num2) {
    arguments[1] = 20;
    console.log(arguments.length); // 1
    console.log(arguments[1]); // 20
    console.log(arguments[0] * arguments[1]); // 200
    console.log(arguments[0] * num2); // NaN
}
doMul(10);
```

**箭头函数中的参数**

如果函数是使用箭头语法定义的，那么传给函数的参数将不能使用 `arguments` 关键字访问，而只能通过定义的命名参数访问。

```javascript 
let foo = () => { console.log(arguments[0]); }
foo(5); // Uncaught ReferenceError: arguments is not defined
```

但可以在包装函数中把他提供给箭头函数：

```javascript
function foo () {
    bar = () => { console.log(arguments[0] + 5) };
    bar();
}
foo(5); // 10
```

>ECMAScript 中所有的参数都是按值传递的，不可能按引用传递参数，如果把对象作为参数传递，那么传递的值就是这个参数的引用
>
>JavaScript 中数据类型可以分为原始数据类型和引用类型
>
>原始数据类型：`undefined`、`null`、`boolean`、`number` 、`string`
>
>引用类型：对象类型，`Object`、`Date`、`Array`、`Function`...
>
>在声明变量的时候，
>
>原始数据类型存放在栈中，按值存放，访问的时候直接访问。将一个变量的值赋值给另一个变量的时候，会将原始值的副本赋值给新变量，这两个变量都存储在栈中，互相独立，占不同的内存空间
>
>对象是存放在堆内存中的，变量实际上是一个存放在栈内存的指针，这个指针指向堆内存中的地址。将一个变量的值赋值给另一个变量时，相当于是将地址的值穿给了另一个变量，这两个变量都存在栈里，占不同的内存，但是都指向一个对象
>
>```javascript
>function changeStuff(a, b, c)
>{
>  a = a * 10;
>  b.item = "changed";
>  c = {item: "changed"};
>}
>
>var num = 10;
>var obj1 = {item: "unchanged"};
>var obj2 = {item: "unchanged"};
>
>changeStuff(num, obj1, obj2);
>
>console.log(num); // 20
>console.log(obj1.item); // 'changed'
>console.log(obj2.item); // 'unchanged'
>```
>
>![623D87DC57BF76883F369C6C27ACFAA2](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/17/20210317181525.png)

### 10.4 没有重载

ECMAScript函数不能像传统编程那样重载，ECMAScript函数没有签名，因为参数是由包含0个值或多个值的数组表示的，没有函数签名，自然也就没有重载。

```javascript
function addSomeNumber (num) {
    return num + 100;
}
function addSomeNumber (num) {
    return num + 200;
}
let result = addSomeNumber(100); // 300
```

```javascript 
let addSomeNumber = function (num) {
    console.log(num + 100);
};
addSomeNumber = function (num) {
    console.log(num + 200);
};
addSomeNumber(100); // 300
```

同一作用域中定义多个同名函数，最后一个将会覆盖前面的各函数。

### 10.5 默认参数值

以前实现函数默认参数的一种常用的方式就是检测某个参数是否等于 `undefined` 

```javascript
function makeKing (name) {
    name = (typeof name !== 'undefined') ? name : 'Henry';
    return `King ${ name } VIII`;
}
console.log(makeKing()); // 'King Henry VIII'
console.log(makeKing('Louis')); // 'King Louis VIII'
```

ES6 之后，支持了显式定义默认参数

```javascript
function makeKing (name = 'Henry') {
    return `King ${ name } VIII`;
}
console.log(makeKing()); // 'King Henry VIII'
console.log(makeKing('Louis')); // 'King Louis VIII'
```

给参数传 `undefined` 相当于没有传值，

```javascript
function makeKing (name = 'Henry', numerals = 'VIII') {
    return `King ${ name } ${ numerals }`;
}
console.log(makeKing()); // King Henry VIII
console.log(makeKing('Louis')); // King Louis VIII
console.log(makeKing(undefined, 'VI')); // King Henry VI
```

在使用默认参数是，`arguments` 对象的值不反应参数的默认值，只记录传给函数的参数。`arguments` 对象始终以调用函数时传入的值为准

```javascript
function makeKing (name = 'Henry') {
    name = 'Louis';
    return `King ${ arguments[0] }`;
}
console.log(makeKing()); // King undefined
console.log(makeKing('Louis')); // King Louis
```

默认参数值并不限于原始值或对象类型，也可以使用调用函数返回的值

```javascript
let numerals = ['I', 'II', 'III', 'IV', 'V'];
let ordinality = 0;
function getNumerals () {
    return numerals[ordinality++];
}

function makeKing (name = 'Henry', nume = getNumerals()) {
    return `King ${ name } ${ nume }`;
}

console.log(makeKing()); // King Henry I
console.log(makeKing('Louis')); // King Louis II
console.log(makeKing('Louis', 'I')); // King Louis I
console.log(makeKing('Peter')); // King Peter III
console.log(makeKing()); // King Henry IV
```

函数的默认参数只有在函数被调用时才会求值，不会在函数定义时求值。而且计算默认值的函数只有在调用函数但未传入相应的参数时才会被调用。

**默认参数作用域与暂时性死区**

函数的参数是在某个作用域中求值的，给多个参数定义默认值实际上就跟使用 `let` 关键字顺序声明变量一样。

```javascript
function makeKing (name = 'henry', nume = 'V') {
    return `King ${ name } ${ nume }`;
}
// 等价于
function makeKing () {
    let name = 'henry';
    let nume = 'V';
    return `King ${ name } ${ nume }`
}
```

因为是按顺序声明的，所以后定义默认值的参数可以引用先定义的默认参数

```javascript
function makeKing (name = 'Henry', nume = 'V') {
    nume = name;
    return `King ${ name } ${ nume }`;
}
console.log(makeKing()); // King Henry Henry
```

参数初始化化顺序遵循 “暂时性死区” 的规则，即前面定义的参数不能引用后面定义的

参数也存在于自己的作用域中，它们不能引用函数体的作用域

```javascript
function makeKing (name = 'Henry', nume = numeral) {
    let numeral = 'VI';
    return `King ${ name } ${ nume }`;
}
console.log(makeKing()); // Uncaught ReferenceError: numeral is not defined
```

### 10.6 参数扩展与收集

ES6 新增了扩展操作符，使用它可以非常简洁地操作和组合集合数据

既可以用于调用函数时传参，也可以用于定义函数参数

#### 10.6.1 扩展参数

再给函数传参时，有时候可能不需要传一个数组，而是分别传入数组的元素

```javascript
let values = [1, 2, 3, 4];
function getSum () {
    let sum = 0;
    for (let i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}
// 不使用扩展操作符
console.log(getSum.apply(null, values)); // 10
// 使用扩展操作符
console.log(getSum(...values)); // 10
console.log(getSum(6, ...values, 2)); // 18
console.log(getSum(-1, ...values)); // 9
```

箭头函数也可以用扩展操作符命名参数，也可以使用默认参数

```javascript
function getProduct (a, b, c = 1) {
    return a * b * c;
}
let getSum = (a, b, c = 1) => { return a + b + c };

console.log(getProduct()); // NaN
console.log(getProduct(...[1, 2])); // 2
console.log(getProduct(...[1, 2, 3])); // 6
console.log(getProduct(...[1, 2, 3, 4])); // 6

console.log(getSum()); // NaN
console.log(getSum(...[1, 2])); // 4
console.log(getSum(...[1, 2, 3])); // 6
console.log(getSum(...[1, 2, 3, 4])); // 6
```

#### 10.6.2 收集参数

可以使用扩展操作符把不同长度的独立参数组合为一个**数组**，会得到一 `Array` 实例

```javascript
function getSum (...values) {
    return values.reduce((x, y) => x + y, 0);
}
console.log(getSum(1, 2, 3)); // 6
```

因为收集参数的结果可变，只能把它作为最后一个参数

箭头函数不支持 `arguments` 对象，但支持收集参数的定义方式，也可以实现与使用 `arguments` 一样的逻辑：

```javascript
let getSum = (...values) => {
    console.log(values.reduce((x, y) => x + y, 0));
    console.log(values[0] + values[1]);
}
console.log(getSum(1, 2, 3)); // 6
// 3
```

### 10.7 函数声明与函数表达式

JavaScript 引擎在任何代码执行前，会先读取函数声明，并在执行上下文中生成函数定义。而函数表达式必须等到代码执行到它那一行，才会在执行上下文中生成函数定义

```javascript
console.log(sum(0, 10));
function sum (a, b) {
    return a + b;
}
// 代码执行没问题
```

函数声明会在任何代码执行之前先被读取并添加到执行上下文，这叫函数声明提升

如果是函数表达式

```javascript 
console.log(sum(0, 10));
let sum = function (a, b) {
    return a + b;
};
// Uncaught ReferenceError: sum is not defined
```

因为这个函数定义包含在一个变量初始化的语句中，而不是函数声明中，代码如果没有执行到变量初始化那一行，执行上下文中就没有函数定义

使用 `var` 关键字也会碰到同样的问题

```javascript
console.log(sum(0, 10));
var sum = function (a, b) {
    return a + b;
};
// Uncaught ReferenceError: sum is not defined
// 因为使用var也只是等价于
var sum;
console.log(sum(0, 10));
sum = function (a, b) {
    return a + b;
};
```

