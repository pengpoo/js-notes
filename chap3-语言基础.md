[toc]

### 3.3 变量

#### 3.3.1 var

var定义变量, `var name;`. 这个名为`name`的变量可以保存任何类型的值,不初始化的情况下,会保存一个特殊值`undefined`

也可以同时定义变量并设置它的值,`var name = "poo";`这样也并不会把`name`变量标记为字符串类型,随后不仅可以改变保存的值,也可以改变值得类型.

**1. var声明作用域**

使用`var`操作符定义的变量会成为包含他的函数的局部变量.

```javascript
function foo () {
    var message = "hello"; //局部变量
}
foo();
console.log(message); //出错

function foo () {
    message = "hello"; //全局变量
}
foo();
console.log(message); // "hello"
```

**2. var声明提升**

使用`var`声明的变量会自动提升到函数作用域的顶部, 且JavaScript引擎会自动将多余的声明在作用域顶部合并为一个声明

```javascript
function foo () {
    console.log(name);
    var name = "poo";
}
foo() //undefined
```

等价于:

```javascript
function foo () {
    var name;
    console.log(name);
    name = "poo"
}
foo() //undefined
```

```javascript
function () {
    var age = 19;
    var age = 23;
    console.log(age);
}
foo() //23  反复多次使用var声明同一个变量也没有问题
```

#### 3.3.2 let声明

`let`作用跟`var`差不多, 他们之间最大的区别是,`let`声明的范围是块作用域,`var`是函数作用域

```javascript
if (true) {
    var name = "poo";
    console.log(name); //"poo"
}
console.log(name); //"poo"

if (true) {
    let name = "poo";
    console.log(name); //"poo"
}
console.log(name); // ReferenceError
```

`let`不允许同一个块作用域中出现冗余声明

```javascript
var name;
var name; //没事儿
let name;
let name; // SyntaxError: Identifier 'name' has already been declared
```

嵌套使用相同的标识符不会报错,因为同一个块中没有重复声明

```javascript
var name = "poo";
console.log(name);
if (true) {
    var name = "peng";
    console.log(name); //peng
}

let age = 20;
console.log(age);
if (true) {
    let age = 23;
    console.log(age); // 23
}
```

**1. 暂时性死区**

`let`声明的变量不会在作用域中被提升, 在`let`声明之前的执行瞬间被称为"暂时性死区", 再次阶段引用任何后面才声明的变量都会抛出`ReferenceError`

```javascript
console.log(name);
let name = "poo" //ReferenceError: Cannot access 'name' before initialization
```

**2. 全局声明**

与`var`不同,使用`let`在全局作用域中声明的变量不会成为window对象的属性

```javascript
var name = "poo";
console.log(window.name); // "poo"

let age = 23;
console.log(window.age); // undefined
```

**3. 条件声明**

不能使用`let`进行条件声明

```html
<script>
    let name = "poo";
	let age = 23;
</script>
<script>
    if (typeof name === 'undefined') { // 至于为什么是 === 'undefined' 是因为 typeof(typeof name)的结果为'string'
        //typeof操作符返回的是字符串
        let name;
    }
    name = "poo"; //name被限制在if{}块的作用域中,所以这里赋值形同搞了个全局变量
    
    try {
        console.log(age); //如果age没有声明过,则会报错
    }
    catch (error) {
        let age;
    }
    age = 23; //age被限制在catch{}块的作用域中,所以这里赋值形同搞了个全局变量
</script>
```

**4. for循环中的let声明**

`let`出现之前,`for`循环定义的迭代变量会渗透到循环体外部

```javascript
for (let i = 0; i < 5; ++i) {
	//循环逻辑
}
console.log(i) //ReferenceError
```

在使用`let`声明迭代变量时,JavaScript引擎在后台会为每个迭代循环声明一个新的迭代变量

一周目,懵逼.jpg :confused:

```javascript
for (var i = 0; i < 5; ++i) {
	setTimeout(() => console.log(i), 0)
} //结果是5,5,5,5,5
for (let i = 0; i < 5; ++i) {
    setTimeout(() => console.log(i), 0)
} //结果是0, 1, 2, 3, 4
```

> ***for***循环里的***let***可以把 ***i*** 绑定到循环的每次迭代块上——形成独立的闭包环境。
>
> 要解决这个问题就是为每个回调构建单独的闭包作用域，通常用**IIFE**：

```javascript
for (var i = 0; i < 10; i++) {
    setTimeout( (function(copyI) { 
        return function(){console.log(copyI);} 
    })(i) , 100 * i);
}
作者：Andrew杨杨杨老师
链接：https://www.zhihu.com/question/64551212/answer/221768468
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 3.3.3 const声明

`const`与`let`基本相同,唯一的一个重要区别是用它声明变量时必须同时初始化变量,且尝试修改`const`声明的变量时会报错

`const`声明的限制只适用于它指向的变量的引用,如果`const`变量引用的是一个对象,那么修改这个对象内部的属性并不违反`const`的限制,但是如果想要修改这个变量引用的对象就会违反`const`的限制

```javascript
const person = {};
person.name = "poo"; //OK
person = []; //TypeError: Assignment to constant variable
```

不能用`const`来声明迭代变量,因为迭代变量会自增

```javascript
for (const i = 0; i < 5; ++i) {
    //
} // TypeError
```

但可以用`const`来声明一个不会被修改的`for`循环变量,对`for-of`和`for-in`循环特别有意义

```javascript
let i = 0;
for (const j = 7; i < 5; ++i) {
    console.log(j);
}

for (const key in {a: 1, b:2}) {
    console.log(key);
} // a, b 用let也一样

for (const value of [1, 2, 3, 4, 5]) {
    console.log(value);
} // 1, 2, 3, 4, 5  用let也一样
// 一周目猜测, 跟这个有关 for循环里的let可以把i绑定到循环的每次迭代块上——形成独立的闭包环境。const也可以
```

#### 3.3.4 声明风格及最佳实践

**1. 不使用var**

**2. const优先, let次之**

### 3.4 数据类型

`ECMAScript`有六种简单的数据类型(原始类型): Undefined, Null, Boolean, Number, String, Symbol(ES6新增)

#### 3.4.1 typeof操作符

用来确定任意对象的数据类型,<u>会返回下列字符串之一</u>

- 'undefined'表示值未定义
- 'boolean' 布尔值
- 'string' 字符串
- 'number' 数值型
- 'object' 表示值为对象或null
- 'function' 是函数
- 'symbol' 是符号

`typeof`是一个操作符不是函数,所以不需要传入参数(但可以使用参数)

```javascript
typeof name;
typeof(name); //可以这样写
```

#### 3.4.2 Undefined类型

Undefined类型只有一个值,就是特殊值`undefined`.

使用`var`或`let`声明了变量但没有初始化值时,就相当与给变量赋予了undefined值,<s>(没有声明的变量也是undefined)</s>

包含`undefined`值的变量跟未定义的变量是有区别的

```javascript
let name;
console.log(name); //'undefined'
console.log(age); //报错
```

对未声明的变量只能执行一个有用的操作 `typeof`,会返回`'undefined'`. (对未声明的变量也可以调用`delete`,但在严格模式下也不行)

`undefined`是一个假值（类假值 falsy）

#### 3.4.3 Null类型

Null类型同样只有一个特殊值`null`,逻辑上,`null`值表示一个空对象指针

```javascript
let car = null;
console.log(typeof car); //'object'
```

在定义将来要保存对象值的变量时,建议使用`null`初始化, 这样之后只要检查这个变量的值是不是`null`就可以知道后来有没有赋值

ECMA-262将`undefined`和`null`定义为表面上相等

```javascript
console.log(null == undefined); //true
```

永远不必显式地将变量值设置为undefined

但如果变量要保存对象,而当时有没有那个变量可以保存,就要用`null`填充变量,这样可以保持`null`空对象指针的语义,并进一步将其与`undefined`区分开

`null`是一个假值

#### 3.4.4 Boolean类型

`Boolean`类型有两个字面值: `true`和`false` ,不同于数值, `true`不等于1, `false`不等于0

`Boolean()`转型函数,可将其他类型转换为布尔值, 转换规则如下

| 数据类型  |            转换为true值            | 转换为false值 |
| :-------: | :--------------------------------: | :-----------: |
|  Boolean  |                true                |     false     |
|  String   |             非空字符串             | ""(空字符串)  |
|  Number   |        非零数值(包括无穷值)        |    0, NaN     |
|  Object   |              任意对象              |     null      |
| Undefined | N/A not applicable 的缩写 “不适用” |   undefined   |

NaN和所有值都不相等，包括它自己

上面Undefined里面咋有个N/A :confused:

#### 3.4.5 Number类型

`Number`类型使用IEEE 754格式表示整数和浮点数

十进制整数

```javascript
let intNum = 23;
```

八进制字面量, 零打头, 如果字面量中包含的数字超过了应有的范围,就会忽略前缀零,按十进制算. (八进制字面量严格模式下无效)

```javascript
let octalNum1 = 070; //56
let octalNum2 = 078; //78
```

十六进制,前缀`0x`

```javascript
let hexNum1 = 0xA; //10
let hexNum2 = 0x1f; //31 十六进制中的字母大小写都可 只是0x中 x必须小写
```

**1. 浮点值**

数值中必须包含小数,且小数点后必须至少有一个数字还不能是零

```javascript
let floatNum1 = 1.1; //1.1
let floatNum2 = .1; //0.1 不推荐
let floatNum3 = 0.1; //0.1
let floatNum4 = 1.; //1 整型
let floatNum5 = 1.0; //1 整型
```

科学计数法

```javascript
let floatNum1 = 3.1e7;
let floatNum2 = 3.1e-7;
```

**0.1 + 0.2 = 0.30000000000000004**

**2. 值的范围**

ECMAScript可以保存的最小数值保存在`Number.MIN_VALUE`中, 最大值保存在`Number.MAX_VALUE`中

```javascript
console.log(Number.MIN_VALUE); // 5e-324
console.log(Number.MAX_VALUE); // 1.7976931348623157e+308 这儿都是正数
```

超过范围`Infinity`(正无穷) `-Infinity`(负无穷)

如果计算返回`Infinity`或`-Infinity`,则该值不能再进一步用于任何计算

确定一个值是不是有限大,可使用`isFinite()`函数

```javascript
let result = Number.MAX_VALUE + Number.MAX_VALUE;
console.log(isFinit(result)); //false
```

**3. NaN**

有一个特殊的数值叫`NaN`, not a number, 用于表示本来要返回数值的操作失败了(而不是抛出错误)

在ECMAScript中, 0, +0, -0相除会返回`NaN`

```javascript
console.log(0/0); //NaN
console.log(-0/+0); //NaN
```

```javascript
console.log(5/0); //Infinity
console.log(5/-0); //-Infinity
```

任何涉及`NaN`的操作始终返回`NaN`(如 NaN/10)

`NaN`不等于包括`NaN`在内的任何值

`isNaN()`函数, 把一个值传给`isNaN()`后,该函数会尝试把它转为数值,某些非数值的值可以直接转换为数值("10", 布尔值等) 

任何不能转换为数值的值都会导致这个函数返回true

```javascript
console.log(isNaN(NaN)); //true
console.log(isNaN(10)); //false
console.log(isNaN("10")); //false
console.log(isNaN("blue")); //true
console.log(isNaN(true)); //false
```

`isNan`可用于测试对象,首先会调用对象的`valueOf()`方法，然后再确认返回的值是否可以转换为数值。如果不能，再调用`toString()`方法，并测试其返回值。

**4. 数值转换**

有三个函数可以将非数值转换成数值`Number()`,`parseInt()`和`parseFloat()`

`Number()`使用于任何类型,后两个主要用于将字符串转换为数值

`Number()`函数转换规则:

- 布尔值,true转换为1, false转换为0

- 数值,直接返回

- null,返回0

- underfined, 返回NaN

- 字符串

  > ```javascript
  > Number("1"); //1
  > Number("123"); //123
  > Nubmer("-123"); //-123
  > Number("0123"); //123
  > Number("1.1"); //1.1
  > Number("0xf"); //15
  > Number(""); //0
  > //其它情况都返回NaN
  > Number("poo"); //NaN
  > ```

- 对象, 调用`valueOf()`方法,并按上述规则转换返回值. 如果转换结果是NaN,则调用`toString()`方法,再按照转换字符串的规则转换

后面的一元加操作符,和`Number()`函数遵循相同的转换规则

`parseInt()`函数,字符串最前面的空格会被忽略,从<u>第一个非空字符开始转换</u>,如果不是数值字符,加号或减号,立即返回`NaN`.如果是就继续检测每个字符,直到字符串末尾或碰到非数值字符

```javascript
let num1 = parseInt("123blue"); //123
let num2 = parseInt(""); //NaN
let num3 = parseInt("0xA"); //10
let num4 = parseInt("22.5"); //22
let num5 = parseInt("22"); //22
```

`parseInt()`函数也接受第二个参数用于指定底数(进制数)

```javascript
let num1 = parseInt("0xA", 16); //10
let num2 = parseInt("A", 16); //10
let num3 = parseInt("10", 2); // 2
```

建议始终传入第二个参数

`parseFloat()`和`parseInt()`类似,从<u>第一个非空字符开始转换</u>,如果不是数值字符,加号或减号,立即返回`NaN`。第一次出现的小数点有效,之后出现的就无效了

`parseFloat()`只解析十进制,所以不能指定底数

如果字符串表示整数(没有小数点或小数点之后是零),`parseFloat()`返回整数

```javascript
let num1 = parseFloat("123blue"); //123
let num2 = parseFloat("123.0"); //123
let num3 = parseFloat("22.5"); //22.5
let num4 = parseFloat("22.5.1"); //22.5
let num5 = parseFloat("090.4"); //90.4
let num6 = parseFloat("0xA"); //0
let num7 = parseFloat(".123"); //0.123
let num8 = parseInt(".123"); //NaN
let num9 = parseFloat(''); // NaN
```

#### 3.4.6 String类型

**1. 字符字面量**

用来表示非打印字符或有其他用途的字符,如:

| 字面量 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| \n     | 换行                                                         |
| \t     | tab                                                          |
| \b     | 退格                                                         |
| \r     | 回车                                                         |
| \f     | 换页                                                         |
| `\\`   | 反斜杠                                                       |
| `\'`   | 单引号                                                       |
| `\"`   | 双引号                                                       |
| \xnn   | 以十六进制编码nn表示的字符,如`\x41`等于"A"                   |
| \unnnn | 以十六进制编码nnnn表示的Unicode字符,例如`\u03a3`等于 $\Sigma$ |

字符串的长度可以通过其length属性获取: `str.length`, 这里的length是字符串的一个属性不是方法。

```javascript
let text = 'This is the letter sigma: \u03a3.';
console.log(text.length); // 28
```

按照目前所能有的知识理解对象可以拥有属性和方法，JS里`string`是字符串不是对象啊，但是可以这样`str.substring()`还有下面的`num.toString()`说明JS中`String`和`Number`也是有方法的呀

[JavaScript中为什么string可以拥有方法？](https://www.cnblogs.com/SheilaSun/p/4765394.html)

如果字符串中包含双字节字符,`length`属性返回的值<u>可能</u>不是准确的字符数

**2. 字符串的特点**

ECMAScript中的字符串是不可变的,要修改某个变量中字符串的值,必须先销毁原始的字符串,然后将包含新值的另一个字符串保存到该变量

**3. 转换为字符串**

几乎所有值都有的`toString()`方法,可用于数值,布尔值,对象和字符串值. null和undefined没有`toString()`方法

多数情况下`toString()`方法不接受参数,但是对数值调用这个方法时,`toString()`可以接受一个参数用于用于指定进制数,如:

```javascript
let num = 10;
console.log(num.toString()); //"100"
console.log(num.toString(2)); //"1010"
console.log(num.toString(8)); //"12"
console.log(num.toString(16)); //"a"
```

如果不知道一个值是不是null或undefined,可以使用`String()`转型函数,函数遵循如下规则

- [ ] 如果值有`toString()`方法,则调用该方法
- [ ] 如果是null,则返回"null"
- [ ] 如果是undefined,则返回"undefined"

用加号操作符给一个值加上一个空字符串"",也可以将其转化成字符串

**4. 模板字面量**

模板字面量保留换行字符,可跨行定义字符串

```javascript
let str1 = "first line\nsecond line";
let str2 = `first line
second line`;
```

**5. 字符串插值**

模板字面量最常用的一个特性是支持字符串插值, 从技术上将木板字面量不是字符串,而是一种特殊的JavaScript句法表达式,只不过求值后的到的是字符串.模板字面量在定义时立即求值并转换为字符串实例

```javascript
let value = 5;
let exponent = "second";
let str = `${value} to the ${exponent} power is ${value * value}`;
```

所有插入的值都会使用`toString()`强制转化为字符串,任何JS 表达式都可用于插值

嵌套的模板字符无需转义:

```javascript
console.log(`hello ${`world`}!`); // hello world!
```

将表达式转换为字符串时会调用`toString()`方法,前面说`null`和`undefined`没有`toString()`方法:confused:,但实测

```javascript
let str;
console.log(`this is ${str}`) //this is undefined
```

那是不是说明这里不是使用`toString()`方法把undefined类型的值转换成字符串`'undefined'`的。

**6. 模板字面量标签函数**

标签函数定义语法: 函数名后面直接带一个模板字面量

标签函数会接收被插值记号分隔后的模板和对每个表达式求值的结果

```javascript
let a = 6;
let b = 9;
function simpleTag (string, aValue, bValue, sumValue) {
    console.log(string);
    console.log(aValue);
    console.log(bValue);
    console.log(sumValue);
    return "foobar";
}

let unTagged = `${ a } + ${ b } = ${ a + b }`;
console.log(unTagged); //"6 + 9 = 15"
let Tagged = simpleTag`${ a } + ${ b } = ${ a + b }`;
// 执行了函数simpleTag 有下面这些输出了
// ["", " + ", " = ", ""]
// 6 应该是字符串
// 9
// 15
console.log(Tagged); // "foobar"
```

由于表达式参数数量可变,应使用剩余操作符将他们收集到一个数组中

```javascript
let a = 6;
let b = 9;
function simpleTag (string, ...expressions) {
    console.log(string);
    for (const expression of expressions) {
        console.log(expression)
    }
    return "foobar";
}

let Tagged = simpleTag`${ a } + ${ b } = ${ a + b }`;
// ["", " + ", " = ", ""] //注意这里的" + " 前后是有空格的 模板字面量里面的空格啊这些字符是会保留的 ${ a }里的空格不会,是因为{}框起来的是javascript表达式
// 6
// 9
// 15
console.log(Tagged); // "foobar"
```

如果想把这些字符串和对表达式的求值结果拼接起来作为默认返回的字符串,可以:

```javascript
let a = 1;
let b = 2;
function Tag (string, ...expressions) {
    return string[0] + expressions.map((e, i) => e + string[i+1]).join("")
}
let c = Tag`${a} + ${b} = ${a + b}`;
```

```javascript
let a = 6;
let b = 9;
function zipTag (string, ...expressions) {
    return string[0] + 
        expressions.map((e, i) => `${e}${string[i + 1]}`).join(''); //array.map和箭头函数
}
let taggedResult = zipTag`${ a } + ${ b } = ${ a + b }`;
console.log(taggedResult); //6 + 9 = 15
```

**7. 原始字符串**

使用模板字面量也可以直接获取原始的模板字面量内容(如换行符或Unicode 字符), 默认的`String.raw`标签函数的使用:

```javascript
console.log(`\u00A9`); //©   length = 1
console.log(String.raw`\u00A9`); // \u00A9   length = 6
```

```javascript
console.log(`first line\nsecond line`);
// first line
// second line

console.log(String.raw`first line\nsecond line`);
// first line\nsecond line

console.log(`first line
second line`);
// first line
// second line

console.log(String.raw`first line
second line`);
// first line
// second line
// 对实际的换行符来说是没用的
```

#### 3.4.7 Symbol类型

#### 3.4.8 object类型



### 3.5 操作符

#### 3.5.1 一元操作符

**1. 递增/递减操作符**

前缀/后缀,递增/递减在语句中的优先级是相等的.后缀版递增和递减发生在语句被求值之后

```javascript
let num1 = 2;
let num2 = 20;
let num3 = --num1 + num2; //21
let num4 = num1 + num2; //21
```

```javascript
let num1 = 2;
let num2 = 20;
let num3 = num1-- + num2; //22
let num4 = num1 + num2; //21
```

这四个操作符可以用于任何值， 不限于整数，字符串、布尔值、浮点值、甚至对象都可以使用。遵循如下规则：

- [ ] 对于字符串，如果是有效的数值形式`str = '123'`，则转换为数值再应用改变。变量类型从字符串变为数值。
- [ ] 对于字符串，如果不是有效的数值形式，则将变量的值设置为`NaN`。变量类型从字符串变为数值。
- [ ] 对于布尔值，`false`转变为0再改变，`true`转变为1变量类型从布尔值变为数值。
- [ ] 对于浮点值，直接运算
- [ ] 如果是对象，则调用其`valueOf()`方法，取得可以操作的值。对得到的值应用上述规则。如果是`NaN`则接着调用`toString()`方法，并再次运用其他规则。变量类型从对象变为数值。

```javascript
let s1 = '2';
let s2 = 'z';
let b = false;
let f = 1.1;
let o = {
    valueOf () {
        return -1;
    }
};
console.log(++s1); // 3
console.log(++s2); // NaN
console.log(++b); // 1
console.log(++f); // 2.1
console.log(++o); // 0
```

**2. 一元加和减**

一元加由一个加号表示，一元减由一个减号表示，放在变量前，对数值没有任何影响。

一元加一元减在运用到不同的数据类型上时：

```javascript
let s1 = '01';
let s2 = '1.1';
let s3 = 'z';
let b = false;
let f = 1.1;
let o = {
    valueOf () {
        return -1;
    }
};
s1 = +s1; //1
s2 = +s2; //1.1
s3 = +s3; //NaN
b = +b; //0
f = +f; //1.1
o = +o; //-1
```

```javascript
let s1 = '01';
let s2 = '1.1';
let s3 = 'z';
let b = false;
let f = 1.1;
let o = {
    valueOf () {
        return -1;
    }
};
s1 = -s1; //-1
s2 = -s2; //-1.1
s3 = -s3; //NaN
b = -b; //0
f = -f; //-1.1
o = -o; //1
```

#### 3.5.2 位操作符

有符号整数使用32位的前31位表示整数值。第32位表示数值的符号，0表示正，1表示负，这一位称为符号位。符号位的值决定了数值其余部分的格式。正值以真正的二进制格式存储，负值以一种称为二补数（补码）的二进制编码存储。一个数值的二补数计算方法如下：

1. 确定绝对值得二进制表示
2. 找到数值的一补数（反码）
3. 给结果加1

例如计算-18的二进制表示

1. 确定18的二进制表示 `0000 0000 0000 0000 0000 0000 0001 0010`
2. 计算一补数(反码) `1111 1111 1111 1111 1111 1111 1110 1101`
3. 加一，得到-18的二进制表示 `1111 1111 1111 1111 1111 1111 1110 1110` (-18的补码)

正数的反码补码都是其本身。

默认情况下，ECMAScript中所有整数都表示有符号数。

特殊值`NaN`和`Infinity`在位操作中都会被当成0处理。

如果将位操作符应用到非数值，首先会自动使用`Number()`函数将该值转换为数值，然后再应用位操作。

**1. 按位非**

`~`

返回数值的反码

```javascript
let num = 25;
num = ~num;
console.log(num); // -26
```

按位非的最终效果是将数值取反再减一 

**2. 按位与**

`&`

将两个数的每一个位对齐，然后基于真值表中的规则，对每一位执行相应的操作。

```javascript
let result = 35 & 3;
console.log(result); //1
```

```
25 = 0000 0000 0000 0000 0000 0000 0001 1001
3  = 0000 0000 0000 0000 0000 0000 0000 0011
   = 0000 0000 0000 0000 0000 0000 0000 0001
```

**3. 按位或**

`|`

```
25 = 0000 0000 0000 0000 0000 0000 0001 1001
3  = 0000 0000 0000 0000 0000 0000 0000 0011
   = 0000 0000 0000 0000 0000 0000 0001 1011
```

**4. 按位异或**

`^`

| 第一个数的位 | 第二个数的位 | 结果 |
| ------------ | ------------ | ---- |
| 1            | 1            | 0    |
| 1            | 0            | 1    |
| 0            | 0            | 0    |
| 0            | 1            | 1    |

先看异不异，再用或的规则

**5. 左移**

`<<`

按照指定的位数将数值的所有位向左移动，符号位不动，保留操作数值的符号，左移后右侧空位补0

```javascript
let oldValue = 2;
let newValue = oldValue << 5; // 64
```

**6. 有符号右移**

`>>`

左移的逆运算

移动后左侧在符号位之后出现空位，且在符号位之后，会用符号位的值来填充空位。

**7. 无符号右移**

`>>>`

会将数值的所有32位都向右移，无符号右移给空位补0

#### 3.5.3 布尔操作符

**1. 逻辑非**

`!`

规则如下：

- [ ] 如果操作数是对象，则返回`false`
- [ ] 如果操作数是空字符串，返回`true`
- [ ] 如果操作数是非空字符串，返回`false`
- [ ] 如果操作数是数值0，返回`true`
- [ ] 如果操作数是非0数值（包括`Infinity`），返回`false`
- [ ] 如果操作数是`null`，返回`true`
- [ ] 如果操作数是`NaN`，返回`true`
- [ ] 如果操作数是`undefined`，返回`true`

同时使用两个叹号`!!`，相当于调用了转型函数`Boolean()`

**2. 逻辑与**

`&&`

规则如下：

- [ ] 如果第一个操作数是对象，则返回第二个操作数
- [ ] 如果第二个操作数是对象则只有第一个操作数求值为`true`时才会返回该对象，不然都返回第一个操作数。
- [ ] 如果两个操作数都是对象，则返回第二个操作数
- [ ] 如果有一个操作数是`null`，则返回`null`
- [ ] 如果有一个操作数是`NaN`，则返回`NaN`
- [ ] 如果有一个操作数是`undefined`，则返回`undefined`

```javascript
let a = null;
let b = '';
console.log(b && a); // 结果返回的是空字符串 不是null 有点看不懂后三条规则了

console.log()
```

MND上的：

`expr1 && expr2` (逻辑与) 若 `expr1` 可转换为 `true`，则返回 `expr2`；否则，返回 `expr1`。

如果一个值可以被转换为 `true`，那么这个值就是所谓的 [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)，如果可以被转换为 `false`，那么这个值就是所谓的 [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)。

逻辑与操作符是一种短路操作符，如果第一个操作数决定了结果，那么永远不会对第二个操作数求值。

```javascript
let found = true;
let result = (found && someUndeclaredVariable); // 会出错
console.log(result); // 不会执行
```

因为`found`是`true`，要返回第二个表达式的值，这里第二个表达式没有声明所以会报错。

```javascript
let found = 'yes';
let result = (found && someUndeclaredVariable); // 同样会出错
```



```javascript
let found = false;
let result = (found && someUndeclaredVariable); // 不会出错
console.log(result); // 会执行
```

```javascript
let found = '';
let result = (found && someUndeclaredVariable); // 一样不会出错
```

要记住那些类型是`truthy`和`falsy`的

**3. 逻辑或**

`||`

`expr1 || expr2` (逻辑或) 如果`expr1`能被转换为`true`，那么返回`expr1`；否则，返回`expr2`。因此，`||`用于布尔值时，当任何一个操作数为`true`则返回`true`；如果操作数都是`false`则返回`false`。

同样有短路的特性

利用这个特性：

```javascript
let myObject = preferredObject || defaultObject
```

#### 3.5.4 乘性操作符

**1. 乘法操作符**

`*`

- [ ] 如果操作数都是数值，正常乘法，ECMASript不能表示的返回`Infinity`或`-Infinity`	
- [ ] 如果有任意操作数为`NaN`，则返回`NaN`
- [ ] `Infinity`乘以0，则返回`NaN`
- [ ] `Infinity`乘以非0的有限值，根据有限值的正负返回`Infinity`或`-Infinity`
- [ ] `Infinity`乘以`Infinity`，返回`Infinity`
- [ ] 如果有不是数值的操作数，自动先用`Number()`函数转换

**2. 除法操作符**

`/`

- [ ] 都是数值，正常除
- [ ] 有任意操作符是`NaN`，返回`NaN`
- [ ] `Infinity`除以`Infinity`，返回`NaN`
- [ ] 0除以0，返回`NaN`
- [ ] 非0有限值除以0，根据情况返回`Infinity`或`-Infinity`
- [ ] `Infinity`除以任何数值，根据第二个数值的正负返回`Infinity,-Infinty`
- [ ] 如果有不是数值的操作数，自动先用`Number()`函数转换

```js
console.log(Infinity / 0); // Infinity
console.log(Infinity / -0); // -Infinity
console.log(0 / Infinity); // 0
console.log(0 / -Infinity); // -0
console.log(2 / Infinity); // 0
console.log(2 / -Infinity); // -0
```

**3. 取模（余）操作符**

`%`

- [ ] 都是数值，正常算
- [ ] 被除数是无限值，除数是有限值，返回`NaN`
- [ ] 被除数是有限值，除数是0，返回`NaN`
- [ ] `Infinity`除以`Infinity`，返回`NaN`
- [ ] 被除数有限值，除数无限值，返回被除数
- [ ] 被除数是0，除数不是0，返回0
- [ ] 如果有不是数值的操作数，自动先用`Number()`函数转换

#### 3.5.5 指数操作符

`**`

#### 3.5.6 加性操作符

**1. 加法操作符**

- [ ] 有任意操作数是`NaN`，返回`NaN`
- [ ] `Infinity`加`Infinity`，返回`Infinity`
- [ ] `-Infinity`加`-Infinity`，返回`-Infinity`
- [ ] `Infinity`加`-Infinity`，返回`NaN`

```javascript
console.log(+0 + +0); // 0
console.log(+0 + -0); // 0
console.log(-0 + +0); // 0
console.log(-0 + -0); // -0
```

如果有一个操作数是字符串：

- [ ] 两个操作数都是字符串，拼接
- [ ] 不全都是字符串，将不是字符串的转换为字符串，再拼接

如果有任一操作数是对象、数值或布尔值，调用他们的`toString()`方法获取字符串。对于`undefined`和`null`，则调用`String()`函数，分别获取`'undefined'`和`'null'`

```javascript
console.log(true + 1); // 2
console.log(true + '1'); // true1
console.log(null + '1'); // null1
```

**2. 减法操作符**

- [ ] 有任意操作数是`NaN`，返回`NaN`
- [ ] `Infinity`减`Infinity`，返回`NaN`
- [ ] `-Infinity`减`-Infinity`，返回`NaN`
- [ ] `Infinity`减`-Infinity`，返回`Infinity`
- [ ] `-Infinity`减`Infinity`，返回`-Infinity`
- [ ] 如果有任一操作数是字符串、布尔值、`null`或`undefined`，会自动先使用`Number()`函数将其转化为数值，再计算。
- [ ] 如果有任一操作数是对象，调用其`valueOf()`方法取得他表示的数值，再计算。如果对象没有`valueOf()`方法，则调用其`toString()`方法，再将得到的字符串转换为数值进行计算。

#### 3.5.7 关系操作符

`>`，`<`，`>=`，`<=`

- [ ] 如果操作数都是数值，就进行数值的比较
- [ ] 都是字符串，逐个比较字符串中对应字符的编码
- [ ] 如果有任一操作数是数值，则将另一个转换为数值进行比较
- [ ] 如果有任一操作数是对象，调用`valueOf()`转换为数值，没有`valueOf()`方法就是调用`toString()`方法转换为字符串，再比较。
- [ ] 如果有任一操作数是布尔值，将其转换为数值再比较。

任何关系操作符，在涉及比较`NaN`时都会返回`false`

#### 3.5.8 相等操作符

**1. 等于和不等于**

`==`和`!=`

- [ ] 如果任一操作数是布尔值或字符串，转换成数值再比较
- [ ] 如果任一操作数是对象，调用`valueOf()`方法取得其原始值
- [ ] `null`和`undefined`相等
- [ ] ==`null`和`undefined`不能转换为其它类型的值再比较==
- [ ] 如果有任一操作数是`NaN`则相等操作符返回`false`，不等操作符返回`true`。`NaN`不等与包括他本身在内的任何
- [ ] 如果两个操作数都是对象，则比较他们是不是同一个对象。如果两个操作数都指向同一个对象则返回`true`

```javascript
let person = { toString () { return 'poo' },
              valueOf () { return 100 } };
console.log(person == 100); // true
console.log(person == 1); //false

let person = { toString () { return 'poo' } };
console.log(person == 1); // false

let person = { toString () { return '1' } };
console.log(person == 1); // true

let persona = person;
console.log(person == persona); // true
```

**2. 全等和不全等**

全等`===`

只有两个操作数再不转换的前提下相等才返回`true`

```javascript
let result1 = ('55' == 55); // true
let result2 = ('55' === 55); // false
console.log(null == undefined); // true
console.log(null === undefined); // false
```

不全等`!==`

```javascript
let result1 = ('55' != 55); // false
let result2 = ('55' !== 55); // true
```

只有两个操作数再不转换的前提下不相等才返回`true`

#### 3.5.9 条件操作符

`variable = boolean_expression ?  true_value : false_value;`

#### 3.5.10 赋值操作符

`=`

复合赋值操作符：`*=`，`/=`，`%=`，`+=`，`-=`，`<<=`，`>>=`，`>>>=`

#### 3.5.11 逗号操作符

可以用来在一条语句中执行多个操作：

`let num1 = 1, num2 = 2, num3 = 3;`

辅助赋值：`let num = (1, 3, 4, 5); //num = 5`

### 3.6 语句

#### 3.6.1 if语句

```
if (condition) statement1 else statement2;
```

```javascript
if (i > 10) {
    console.log(i)
} else if (i < 3) {
    console.log('less than 3')
}
```

#### 3.6.2 do-while语句

```javascript
do {
	statement;
} while (expression);
```

```javas
let i = 1;
do {
	console.log(i);
	i += 2;
} while (i <= 10);
```

#### 3.6.3 while语句

```
while (expression) statement
```

```javascript
while (i <= 10) {
	console.log(i);
	i++;
}
```

#### 3.6.4 for语句

```
for (initialization; expression; post-loop-expression) statement
```

```javascript
let count = 10;
for (let i = 1; i < count; i++) {
    console.log(i);
}
```

无法通过while循环实现的逻辑，同样也无法通过for循环实现。

#### 3.6.5 for-in语句

for-in语句是一种严格的迭代语句，用于<u>枚举对象中的非符号==键属性==</u>

```
for (property in expression) statement
```

```javascript
for (const propName in window) {
    document.write(propName);
}
```

如果for-in循环要迭代的变量是null或undefined，则不执行循环体

```javascript
let str = 'string';
for (i in str) {
    console.log(i);
}
/*0
1
2
3
4
5*/
let str = 'string';
for (i in str) {
    console.log(str[i])
}
/*s
t
r
i
n
g*/
```

#### 3.6.6 for-of语句

for-of语句是一种严格的迭代语句，用于<u>遍历可迭代对象的==元素==</u>

```
for (property of expression) statement
```

```javascript
for (const el of [2, 4, 6, 8]) {
    document.write(el);
}
```

```javascript
for (const el of 'string') {
    console.log(el);
}
/*s
t
r
i
n
g*/
```

#### 3.6.7 标签语句

```
label: statement
```

```javascript
start: for (let i = 0; i < count; i++) {
	console.log(i);
}
```

在这里start就是一个标签，可以在后面通过break或continue语句引用。

标签语句的主要运用场景是嵌套循环。

#### 3.6.8 break和continue语句

break语句用于立即退出循环，强制执行循环后的下一条语句。而continue语句也用于立即退出循环，但是会再次从循环顶部开始执行。

```javascript
let num = 0;
for (let i = 1; i < 10; i++) {
    if (i % 5 == 0) {
        break;
    }
    num++;
}
console.log(num); // 4
```

```javascript
let num = 0;
for (let i = 1; i < 10; i++) {
    if (i % 5 == 0) {
        continue;
    }
    num++;
}
console.log(num); // 8
```

break和continue语句都可以与标签语句一起使用，返回代码中的特定位置

```javascript
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break outermost;
        }
        num++;
    }
}
console.log(num); // 55
```

```javascript
let num = 0;
for (let i = 0; i < 10; i++) {
    for (j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break;
        }
        num++;
    }
}
console.log(num); // 95
```

```javascript
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            continue outermost;
        }
        num++;
    }
}
console.log(num); // 95
```

```javascript
let num = 0;
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            continue;
        }
        num++;
    }
}
console.log(num); // 99
```

#### 3.6.9 with语句

with语句的用途是将代码作用域设置为特定的对象，

```
with (expression) statement
```

主要使用场景是针对一个对象反复操作：

```javascript
let qs = location.search.substring(1);
let hostName = location..hostname;
let url = location.href;
// 用with后
with (location) {
    let qs = search.substring(1);
    let hostName = hostname;
    let url = href;
}
```

#### 3.6.10 switch语句

```javascript
switch (expression) {
    case value1:
        statement
        break;
    case value2:
        statement
        break;
    default:
        statement
}
```

每个case分支相当于：如果表达式等于后面的value，则执行该分支下的语句，break关键字会导致代码跳出switch语句。如果没有break，则代码会继续匹配下一个条件。default关键字用于在任何条件都没有满足时指定默认执行的语句。

ECMAScript给switch语句一些其他的特性。switch语句可以用于所有数据类型，因此可以使用字符串或是对象。条件的值不需要是常量，也可以是变量或表达式

```javascript
switch ('hello world') {//使用了字符串
    case 'hello ' + 'world':
        console.log('found');
        break;
    case 'goodbye':
        console.log('bye');
        break;
    default:
        console.log('default');
}
```

```javascript
let num = 25;
switch (true) { //这里传入true这个参数是因为下面每个表达式的返回值是布尔值，switch语句比较的时候使用的是全等操作符
    case num < 0:
        console.log('<0');
        break;
    case num >=0 && num < 15:
        console.log('>=0, <15');
        break;
    case num >= 15 && num < 30:
        console.log('>=15, <30');
        break;
    default:
        console.log('>=30');
}
```

switch语句在比较每个条件的值的时候会使用全等操作符。

### 3.7 函数

```
function functionName (arg0, arg1, ..., argN) {
	statements
}
```

任何函数在任何时间都可以使用return语句来返回函数的值。只要碰到return语句，函数就会立即停止执行并退出，因此return语句后的代码不会被执行。return语句可以不带返回值，这是函数会立即停止执行并退出，返回undefined。





**练习**：生成九九乘法表

```javascript
for (let i = 1; i < 10; i++) {
  let multi = '';
  for (let j = 1; j <= i; j++) {
    if (i * j < 10) {
      multi += `${ i }x${ j }=${ i * j }  `;
    } else {
      multi += `${ i }x${ j }=${ i * j } `;
    }
  }
  console.log(multi);
}
```

