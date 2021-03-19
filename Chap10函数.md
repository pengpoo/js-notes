[TOC]

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
// 好像并不是特别准确
```

### 10.7 函数作为值

函数名在 ECMAScript 中就是一个变量，所以函数可以用在任何可以使用变量的地方

**把函数作为参数传入：**

```javascript
function callSomeFunction (someFunction, someArguments) {
    return someFunction(someArguments);
}
// callSomeFunction 创建了一个作用在全局的函数

function add10 (num) {
    return num + 10;
}
let result1 = callSomeFunction (add10, 10);
console.log(result1);

function greeting (name) {
    return `hello ${ name }`;
}
let result2 = callSomeFunction(greeting, 'poo');
console.log(result2);

```

访问函数不是调用函数，不带括号

```javascript
function callSomeFunction (someFunction, ...args) {
    return someFunction(...args);
}

function addAll () {
    let sum = 0;
    for (let i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}

let result = callSomeFunction(addAll, 1, 2, 3, 4, 5, 6, 7, 8, 9);
console.log(result); // 45
```

**从一个函数中返回另一个函数**

假设有有一个包含对象的数组，现在需要按照任意指定的对象的属性将它们排列

数组排序，按照一定规则，可以使用 `sort()` 方法加上传入的比较函数来实现。比较函数接收两个参数，是要比较的两个 `item` ，在这里就是数组里的两个对象。如果比较函数直接是两个对象，两个对象比较大小，会调用 `valueOf()` 和 `toString()` 方法，比较结果很可能没有实际意义。那要求的是能够按照任意属性比较大小，那就需要创建另一个函数，另一个能够接受属性的函数

```javascript
function createCompareFunction (property) {
    return function comepareFunction (obj1, obj2) {
        if (obj1[property] < obj2[property]) {
            return -1;
        } else if (obj1[property] > obj2[property]) {
            return 1;
        } else {
            return 0;
        }
    }
}

let objArr = [
    {
        name: 'pengpoo',
        age: 24
    },
    {
        name: 'shen',
        age:20
    }
]

console.log(objArr.sort(createCompareFunction('age')));
console.log(objArr.sort(createCompareFunction('name')));
```

### 10.9 函数内部

ES6 之前，函数内部存在两个特殊的对象 `arguments` 和 `this`，ES6 新增 `new.target` 属性

#### 10.9.1 arguments

类数组对象，箭头函数定义的没有。`arguments` 对象还有一个属性 `callee` ，是一个指向 `arguments` 对象所在函数的指针。

```javascript
// 阶乘
function factorial (num) {
    if (num <= 1) {
        return 1;
    } else {
        return num = num * factorial(num-1);
    }
}
```

只要给函数一个名称，而且这个名称不会变，这样的定义就没有问题。但函数要执行就必须保证函数名 `factorial`不变，导致了紧耦合。使用 `arguments.callee` 就可以让函数逻辑与函数名解耦：

```javascript
function factorial (num) {
    if (num <=1 ) {
        return 1;
    } else {
        return num * arguments.callee(num-1);
    }
}

let trueFactorial = factorial;

factorial = function () {
    return 0;
};

console.log(trueFactorial(4)); // 12
console.log(factorial(4)); // 0
```

#### 10.9.2 this

这个对象在标准函数和箭头函数中有不同行为

----

在标准函数中，`this` 引用的是把当前函数当成方法调用的上下文对象，这时通常称其为 `this` 值（在网页的全局上下文中调用函数时，`this` 指向的是 `window`）

```javascript
window.color = 'red';
let o = {
    color: 'blue'
}

function logColor () {
    console.log(this.color);
}

o.logColor = logColor;

logColor(); // 'red'
o.logColor(); // 'bule'
```

`this` 到底引用哪个对象必须得到函数调用的时候才能确定。执行到 `logColor()` 时，由于函数在全局上下文中被调用，`this` 指向 `window`，所以 `this.color` 就是 `window.color` 。把 `logColor` 指针的值赋值给 o 的 `logColor` ，现在`o.logColor` 也指向打印颜色的函数对象，再调用 `o.logColor()` ，`this` 会指向 `o`，所以 `this.color` 就是 `o.color` 。

----

在箭头函数中，`this` 引用的是定义箭头函数的上下文

```javascript
window.color = 'red';
let o = {
    color: 'blue'
};

let logColor = () => { console.log(this.color) };

o.logColor = logColor;

logColor(); // 'red'
o.logColor(); // 'red'
```

这里的箭头函数是在全局上下文中定义的，所以 `this` 指向 `window`

在事件回调或定时回调中调用某个函数时，`this` 值指向的并不是想要的对象。此时将回调函数写成箭头函数就可以解决问题

```javascript
function King () {
    this.royaltyName = 'Henry';
    // this 引用 King 实例
    setTimeout(() => { console.log(`King ${ this.royaltyName }`)}, 1000);
}

function Queen () {
    this.royaltyName = 'Elizabeth';
    // this 引用 window 对象
    setTimeout(function () { console.log(`Queen ${ this.royaltyName }`) }, 1000);
}

new King();
new Queen();
```

#### 10.9.3 caller

ECMASCript5 也会给函数加上一个 `caller` 属性，这个属性引用的是调用当前函数的函数，如果是在全局作用域中被调用则为 `null` 

```javascript
function outer () {
    inner();
}

function inner () {
    console.log(inner.caller);
}

outer();
/*
ƒ outer () {
    inner();
}
*/
inner(); // null
```

降低耦合度

```javascript
function outer () {
    inner();
}

function inner () {
    console.log(arguments.callee.caller);
}

outer();
/*
ƒ outer () {
    inner();
}
*/
```

#### 10.9.4 new.target

ECMAScript 中的函数始终可以作为一个构造函数实例化一个新的对象，也可作为普通函数被调用。ES6 中新增 `new.target` 属性，检测函数是否是使用 `new` 关键字调用的。如果函数是正常调用的，`new.target` 的值是 `undefined` ，如果是使用 `new` 关键字调用，则 `new.target` 将引用被调用的构造函数

```javascript
function King () {
    if (!new.target) {
        throw 'King must be instantiated using "new"';
    }
    console.log('King instantiated using "new"');
}

new King();
King();
```

### 10.10 函数属性与方法

ECMAScript 中函数是对象，有属性和方法。每个函数都有两个属性：`length` 和 `prototype`

`length` 属性保存函数定义的**命名参数**的个数

```javascript
function logName (name) {
    console.log(name);
}
function sum (num1, num2) {
    console.log(num1 + num2);
}
function logHi () {
    console.log('Hi');
}
console.log(logName.length); // 1
console.log(sum.length); // 2
console.log(logHi.length); // 0
```

`prototype` 是保存引用类型所有的实例方法的地方，`toString()`、`valueOf()` 这些方法都保存在 `prototype` 上，进而由实例共享。在ECMAScript5 中，`prototype`属性是不可枚举的，使用 `for-in` 循环不会返回这个属性。

----

函数还有两个方法 `apply()` 和 `call()`

两个方法都会以指定的 `this` 值来调用函数，即会设置调用函数时函数体内 `this` 对象的值。

`apply()` 方法接收两个参数：函数内 `this` 值和一个参数数组

```javascript
function sum (num1, num2) {
    return num1 + num2;
}

function callSum1() {
    return sum.apply(this, arguments);
}

function callSum2 (a, b) {
    return sum.apply(this, [a, b])
}

callSum1(10, 20);
callSum2(10, 20);

console.log(callSum1(10, 20)); // 30
console.log(callSum2(10, 20)); // 30
```

`callSum1()` 函数会调用 `sum()` 函数，将 `this`（`callSum1()` 内的 `this` 值） 作为函数体（ `sum()` 函数体）内的 `this` 值传入，同时传入了 `arguments` 对象。

```javascript
function sum () {
    return this.num1 + this.num2;
}

let obj = {
    num1: 1,
    num2: 2
}

function callSum () {
    return sum.apply(obj)
}

console.log(callSum());
// 这写的没啥意义啊
```

`call()` 方法和 `apply()` 方法的作用一样，只是传参的形式不同。第一个参数也是 `this` 值，剩下的要传给被调用函数的参数是逐个传递的

```javascript
function sum (num1, num2) {
    return num1 + num2;
}

function callSum (a, b) {
    return sum.call(this, a, b); // 这儿逐个传递
}

console.log(callSum(10, 20)); // 30
```

到底是使用 `apply()` 还是 `call()`，完全取决于怎么给要调用的函数传参更方便。想直接传 `arguments` 对象或者一个数组就用 `apply()`。一个个传就用 `call()`。没有参数要传就都一样。

`apply()` 和 `call()` 真正强大的地方并不是给函数传递参数，而是控制函数调用上下文（即函数体内 `this` 的值）的能力

```javascript
window.color = 'red';

let o = {
    color: 'blue'
}

function logColor () {
    console.log(this.color);
}

logColor(); 'red'
logColor.call(this); // 'red'
logColor.call(window); // 'red'
logColor.call(o); // 'blue'
```

使用 `call()` 或 `apply()` 的好处是可以将任意对象设置为任意函数的作用域，这样对象可以不用关心方法

在之前切换上下文需要先把 `logColor()` 直接赋值为对象 `o` 的属性，然后再调用。

ES5 出于同样的目的定义了一个新方法：`bind()`。`bind()` 方法会创建一个新的函数实例，其 `this` 值会被绑定到传给 `bind()` 的对象

```javascript
window.color = 'red';

let o = {
    color: 'blue'
}

function logColor () {
    console.log(this.color);
}

let objLogColor = logColor.bind(o);
objLogColor(); // 'blue'
```

在 `logColor()` 上调用 `bind()` 并传入对象 `o` 创建一个新的函数实例，一个 `this` 值被绑定到 `o` 的函数实例 `objLogColor()` 。

对函数而言，继承的方法 `toLocalString()` 和 `toString()` 始终返回函数的代码，具体格式因浏览器而异。继承的方法 `valueOf()` 返回函数本身。

### 10.11 函数表达式

定义函数有两种方式：函数声明和函数表达式

函数声明的关键特点是函数声明提升，函数声明会在代码执行前获得定义

```javascript
logHi();
function logHi () {
    console.log('Hi');
}
// 不会报错
```

```javascript
let someFunction = function () {
    // 函数体
}

```

函数表达式看起来就像一个普通的变量定义和赋值，这样创建的函数叫作匿名函数，因为 `function` 关键字后面没有标识符。匿名函数有时也称为兰姆达函数。未赋值给其他变量的匿名函数的 `name` 属性是空字符串

函数表达式需要先赋值再使用。

```javascript
// 不能这样写
if (condition) {
    function logHi () {
        console.log('hi');
    }
} else {
    function logHi () {
        console.log('hello');
    }
}
```

这种写法在 ECMAScript 中不是有效语法，函数声明提升是重点，定义了两个 `logHi` ，改成

```javascript
let logHi;
if (condition) {
    logHi = function () {
        console.log('hi');
    }
} else {
    logHi = function () {
        console.log('hello')
    }
}
```

先声明一个变量，根据 `condition` 的真假来选择给 `logHi`指定指向的函数

创建函数并赋值给变量的能力也可以用于在一个函数中把另一个函数当作值返回

```javascript
function createCompareFunction (property) {
    return function (obj1, obj2) {
        let val1 = obj1[property];
        let val2 = obj2[property];
        if (val1 < val2) {
            return -1;
        } else if (val1 > val2) {
            return 1;
        } else {
            return 0;
        }
    }
}
```

`createCompareFunction()` 返回一个匿名函数，这个匿名函数要么被赋值给一个变量，要么可以直接调用。

任何时候只要函数被当作值来使用，他就是一个函数表达式

### 10.12 递归

**递归函数**通常的形式是一个函数通过名称调用自己

```javascript
function factorial (num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * factorial(num - 1);
    }
}
// 经典递归阶乘函数

let anotherFac = factorial;
factorial = null;
console.log(anotherFac(4)); // 报错
```

使用 `arguments.callee` ，这是一个指向当前正在执行的函数的指针，可以在函数内部递归调用

```javascript
function factorial (num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * arguments.callee(num - 1)
    }
}
```

在严格模式下，运行的代码是不能访问 `arguments.callee` 的，这时可以使用命名函数表达式

```javascript
const factorial = (function f (num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * f(num - 1);
    }
});

f = null;
```

如果你想在函数体内部引用当前函数，则需要创建一个命名函数表达式。**然后函数名称将会（且只会）作为函数体（作用域内）的本地变量**。

函数表达式和命名函数表达式的区别就在于有没有标识符，上面那段代码里的 `f` 就是标识符

### 10.13 尾调用优化

尾调用就是外部函数的返回值是一个内部函数的返回值

```javascript
function outerFunction () {
    return innerFunction();
}
```

ES6 之前，执行这个例子会在内存中发生的操作是： P307

如果函数的逻辑允许给予尾调用将其销毁，则引擎就会这么做

#### 10.13.1 尾调用优化的条件

- [ ] 代码在严格模式下执行
- [ ] 外部函数的返回值是对尾调用函数的调用
- [ ] 尾调用函数返回后不需要执行额外的逻辑
- [ ] 尾调用函数不是引用外部函数作用域中自由变量的闭包

#### 10.13.2  尾调用优化的代码

```javascript
// 计算斐波拉契数列
"use strict"
// 基础框架
function fib (n) {
    return fibImpl(0, 1, n);
}

function fibImpl (a, b, n) {
    if (n === 0) {
        return a;
    } else {
        return fibImpl(b, a + b, n - 1);
    }
}
```

### 10.14 闭包

**闭包**指的是那些引用了另一个函数作用域中变量的函数，通常是在嵌套函数中实现的。

```javascript
let passed = 3;
function addTo () {
    let inner = 2;
    console.log(inner + passed);
}
addTo(); // 5
console.dir(addTo); 
```

![屏幕截图 2021-03-19 132209](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/19/20210319132242.png)

> closures are nothing but functions with preserved data.

```javascript
function compare (value1, value2) {
    if (value1 < value2) {
        return -1;
    } else if (value > value2) {
        return 1;
    } else {
        return 0;
    }
}
```



```javascript
function createCompareFunction (propertyName) {
    return function (obj1, obj2) {
        let value1 = obj1[propertyName];
        let value2 = obj2[propertyName];
        
        if (value1 < value2) {
            return -1;
        } else if (value1 > value2) {
            return 1;
        } else {
            return 0;
        }
    };
}

let compare = createCompareFunction('name');
let result = compare({name: 'poo'}, {name: 'kataomoi'});
```



#### 10.14.1 this 对象

```javascript
window.identity = 'The Window';
let object = {
    identity: 'My Window',
    getIdentityFunc () {
        return function () {
            return this.identity;
        };
    }
};

console.log(object.getIdentityFunc()()); // 'The Window'
```



```javascript
window.identity = 'The Window';
let object = {
    identity: 'My Window',
    getIdentityFunc () {
        let that = this;
        return function () {
            return that.identity;
        };
    }
};

console.log(object.getIdentityFunc()()); // 'My Window'
```



```javascript
window.identity = 'The Window';
let object = {
    identity: 'My Window',
    getIdentity () {
		return this.identity;
    }
};

console.log(object.getIdentity()); // 'My Window'
console.log((object.getIdentity)()); // 'My Window'
console.log((object.getIdentity = object.getIdentity)()); // 'The Window'
```

第三次执行了一次赋值，紧接着调用。因为赋值表达式的值是函数本身，`this` 的值不再与任何对象绑定。

```javascript
// 如果
object.getIdentity = object.getIdentity;
console.log(object.getIdentity()); // 'My Window'
// 上面的那个相当于
f = object.getIdentity;
console.log(f()); // 'The Window'
```

#### 10.14.2 内存泄漏

IE9 之前的版本中，把 HTML 元素保存在某个闭包的作用域中，就相当于宣布该元素不能被销毁

```javascript
function assignHandler () {
    let element = document.getElementById('someElement');
    element.onclick = () => console.log(element.id);
}
```

上面创建了一个闭包，即 `element` 元素的事件处理程序，而这个处理程序又创建了一个循环引用。匿名函数引用着 `assignHandler()` 的活动对象，阻止了 对 `element` 的引用计数归零。只要匿名函数存在，`element` 的引用计数至少为 1。也就是说内存不会被回收。稍加修改就可以避免这种情况

```javascript
function assignHandler () {
    let element = document.getElementById('someElement');
    let id = element.id;
    element.onclick = () => console.log(id);
    element = null;
}
```

闭包改为引用一个保存着 `element.id` 的变量 `id` ，从而消除了循环引用，但闭包还是会引用到 `element`，。。。。。

### 10.15 立即调用的函数表达式

立即调用的匿名函数又被称为**立即调用的函数表达式**。

使用 IIFE 可以模拟块级作用域

```javascript
(function () {
    for (var i = 0; i < count; i++) {
        console.log(i);
    }
})();
console.log(i); // 抛出错误
```

可以用 IIFE 锁定参数值

```javascript 
let divs = document.querySelectorAll('div');
// 达不到目的
for (var i = 0; i < divs.length; ++i) {
    divs[i].addEventListener('click', function() {
        console.log(i);
    });
}
```

在执行单机处理程序时，迭代变量的值是循环结束时的最终值。

借助 IIFE 来执行一个函数表达式，传入每次循环的当前索引

```javascript
let divs = document.querySelectorAll('div');
for (var i = 0; i < divs.length; ++i) {
    divs[i].addEventListener('click', (function (frozenCounter) {
        return function () {
            console.log(frozenCunter);
        };
    })(i));
}
```

使用块级作用域

```javascript 
let divs = document.querySelectorAll('div');
for (let i = 0; i < divs.length; ++i) {
    divs[i].addEventListener('click', function () {
        console.log(i);
    });
}
```

### 10.16 私有变量

JS 没有私有成员的概念，所有对象属性都是共有的。

但是有私有变量的概念，任何定义在函数或块中的变量都可以认为是私有的，包括函数参数、局部变量、以及函数内部的其他函数

```javascript
function add (num1, num2) {
    let sum = num1 + num2;
    return sum;
} // sum num1 num2 都是私有变量
```

特权方法是能够访问函数私有变量（及私有函数）的公有办法

创建特权方法，在构造函数中实现

```javascript
function MyObj () {
    // 私有变量和函数
    let privateVariable = 10;
    function privateFunction () {
        return false;
    }
    // 特权方法
    this.publicMethod = function () {
        console.log(++privateVariable);
        return privateFunction();
    };
}

let myObj = new MyObj();
console.log(myObj.publicMethod()); // 11 false
console.log(myObj.privateVariable); // undefined
```

可以定义私有变量和特权方法，以隐藏不能被直接修改的数据

```javascript
function Person (name) {
    this.getName = function () {
        return name;
    };
    
    this.setName = function (value) {
        name = value;
    };
}

let person = new Person('Nicholas');
console.log(person.getName()); // Nicholas
person.setName('Greg');
console.log(person.getName()); // Greg
```

私有变量 `name` 对每个 `person` 实例而言都是独一无二的，以为每次调用构造函数，都会重新创建一套变量和方法

这样也有一个问题，必须通过构造函数来实现这种隔离，构造函数模式的缺点是每个实例都会重新创建一遍新方法。使用静态私有变量实现特权方法可以避免这个问题

#### 10.16.1 静态私有变量

使用私有作用域定义私有变量和函数实现特权方法

```javascript
(function() {
    // 私有变量和方法
    let privateVariable = 10;
    
    function privateFunction () {
        return false;
    }
    
    // 构造函数
    MyObject = function () {};
    
    // 公有和特权方法
    MyObject.prototype.publicMethod = function () {
        console.log(++privateVariable, privateFunction());
    };
})();

let myObj1 = new MyObject();
myObj1.publicMethod() // 11 false

let myObj2 = new MyObject();
myObj2.publicMethod() // 12 false

let myObj3 = new MyObject();
myObj3.publicMethod() // 13 false
```

匿名函数表达式创建了一个包含构造函数及其方法的私有作用域，首先定义的是私有变量和方法，然后定义了构造函数。公有方法定义在构造函数的原型链上。

这个模式定义构造函数没有使用函数声明，使用的是函数表达式，函数声明会创建内部函数，这里并不需要。声明`MyObject` 没有使用关键字，以为不使用关键字声明的变量会创建在全局作用域中，所以 `MyObject` 变成了全局变量，可以在这个私有域外部被访问。

这个模式与前一个模式主要区别就是，私有变量和私有函数是由实例共享的，以为特权方法定义在原型链上，所以也是由实例共享的。

```javascript
(function () {
    let name = '';
    
    Person = function (value) {
        name = value;
    };
    
    Person.prototype.getName = function () {
        return name;
    };
    
    Person.prototype.setName = function (value) {
        name = value;
    } ;
})();

let person1 = new Person('Nicholas');
console.log(person1.getName()); // Nicholas
person1.setName('Matt');
console.log(person1.getName()); // Matt

let person2 = new Person('poo');

person1.setName('peng'); 
console.log(person2.getName()); // peng
```

使用这种模式，`name` 变成了静态变量，可供所有实例使用，在任何实例上调用 `setName()` 方法，修改这个变量都会影响其他实例。

到底是把私有变量放在实例中，还是作为静态私有变量，根据需求

#### 10.16.2 模块模式

前面的模式通过自定义类型创建私有变量和特权方法

模块模式则在一个单例对象上实现了相同的隔离和封装

单例对象就是只有一个实例的对象

```javascript
let singleton = {
    name: value,
    method () {
        // 方法代码
    }
};
```

模块模式是在单例对象基础上加以扩展，使其通过作用域链来关联私有变量和特权方法

```javascript
let sigleton = function () {
    // 私有变量和函数
    let privateVariable = 10;
    
    function privateFunction () {
        return false;
    }
    
    // 特权/公有方法和属性
    return {
        publicProperty: true,
        
        piblicMethod () {
            privateVariable++;
            return privateFunction();
        }
    };
}();
```

模块模式使用了匿名函数返回一个对象，匿名函数内部首先定义私有变量和函数。之后创建一个要通过匿名函数返回的对象字面量。这个对象字面量中只包含可以公开访问的属性和方法。如果单例对象需要进行某种初始化，并且需要访问私有变量时，就可以采用这个模式。

#### 10.16.3 模块增强模式

