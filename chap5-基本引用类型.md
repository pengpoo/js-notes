[toc]



## 第五章 基本引用类型

引用值(对象)是某个特定引用类型的实例，在ECMAScript中，引用类型是把数据和功能组织到一起的结构，是不同于类的。

引用类型有时也被称为对象定义，因为他们描述了自己的对象应有的属性和方法。

新对象通过使用`new`操作符加构造函数来创建。

```javascript
let now = Date();
```

上面这行代码创建了引用类型`Date`的一个实例，并将它保存在变量`now`里。`Date()`在这里就是一个构造函数，并且这里并没有给构造函数传入任何参数所以这里只是创建了一个有默认属性和方法的简单对象。

### 5.1 Date

`Date`类型将日期保存为自协调世界时间(UTC)1970年1月1日零时至今所经过的毫秒数。

> 题外话：Windows，Linux双系统时，Linux系统时间比Windows快8小时是因为，Windows把计算机硬件时间当成是本地时间，Linux把计算机硬件时间当成是UTC时间，再根据装系统时选择的时区来计算本地时间。

创建日期对象：

```javascript
let now = new Date();
```

不传入参数，对象保存当前日期和时间。要想基于其他时间创建对象需要传入其毫秒表示。为此JavaScript提供了`Date.parse()`和`Date.UTC()`两个辅助函数。

----

`Date.parse()`接收一个表示日期的==字符串参数==

- [ ] `‘month/day/year'`, eg:`5/23/2019`;
- [ ] `'月名 日, 年'`, eg:`'May 23, 2019'`
- [ ] `'周几 月名 日 年 时:分:秒 时区'`, eg：`'Tue Mar 9 2021 12:28:30 GMT+0800'`

```javascript
let someDate = new Date(Date.parse('3/9/2021'))
```

如果传给`Date.parse()`的参数不表示日期，则会返回`NaN`

如果直接把表示时间的字符串传给构造函数`Date()`，那么`Date`会在后台调用`Date.parse()`，所以也可以直接这么写：

```javascript
let someDate = new Date('3/9/2021')
```

----

`Date.UTC()`传入的参数是 年，零起点月数(0到11)，日（1到31），时（0到23），分，秒，毫秒

```javascript
// GMT时间2000年1月1日零时
let y2k = new Date(Date.UTC(2000, 0, 1));
// GMT时间2005年5月5日下午5时5分5秒
let allFive = new Date(Date.UTC(2005, 4, 5, 17, 5, 5));
```

`Date.UTC()`也可以被`Date()`构造函数隐式调用，不过这种情况下创建的时间不是GMT时间，是本地时间。

```javascript
// 本地时间2000年1月1日零时
let ly2k = new Date(2000, 0, 1);
```

----

`Date.now()`方法，返回方法执行时日期和时间毫秒数

```javascript
console.log(Date.now()); // 1615266167819
```

```javascript
let start = Date.now();
// 调用某个函数
doSomething();
let end = Date.now();
let result = end - start;
```

----

#### 5.1.1 继承的方法

`Date`类型重写了`toString()`,`toLocaleString()`和`valueOf()`方法。

`toString()`方法通常返回带时区信息的日期和时间

`toLocaleString()`方法返回与浏览器运行的本地环境一致的日期和时间。

`valueOf()`方法重写之后返回的是日期的毫秒表示

```javascript
let Date1 = new Date(2021, 0, 1);
let Date2 = new Date(2021, 0, 2);
console.log(Date2 > Date1); // true
```

#### 5.1.2 日期格式化方法

- [ ] `toDateString()`
- [ ] `toTimeString()`
- [ ] `toLocaleDateString()`
- [ ] `toLocaleTimeString()`
- [ ] `toUTCString()`

```javascript
let date = new Date(2021, 4, 11); // 创建的是本地时间
console.log(date.toString()); // Tue May 11 2021 00:00:00 GMT+0800 (中国标准时间)
console.log(date.toDateString()); // Tue May 11 2021
console.log(date.toTimeString()); // 00:00:00 GMT+0800 (中国标准时间)
console.log(date.toLocaleDateString()); // 2021/5/11
console.log(date.toLocaleTimeString()); // 上午12:00:00
console.log(date.toUTCString()); // Mon, 10 May 2021 16:00:00 GMT
```

#### 5.1.3 日期、时间组件方法

### 5.2 RegExp

ECMAScript通过`RegExp`类型支持正则表达式。

```
let expression = /pattern/flags;
```

`pattern`可以是任何简单或复杂的正则表达式，每个正则表达式可以带一个或多个`flag`：

- [ ] `g`全局模式
- [ ] `i`不分大小写
- [ ] `m`多行模式，查找到一行文本末尾时会继续查找
- [ ] `y`粘附模式，只查找从`lastIndex`开始之后的字符串
- [ ] `u` Unicode模式，启用Unicode匹配
- [ ] `s` dotAll模式，表示元字符`.`可以匹配任何字符（包括`\n`或`\r`）

正则表达式`pattern`中所有元字符也必须转义，元字符有:

|元字符|描述|
|:----:|----|
|.|句号匹配任意单个字符除了换行符。|
|[ ]|字符种类。匹配方括号内的任意字符。|
|[^ ]|否定的字符种类。匹配除了方括号里的任意字符|
|*|匹配>=0个重复的在*号之前的字符。|
|+|匹配>=1个重复的+号前的字符|
|?|标记?之前的字符为可选.|
|{n,m}|匹配num个大括号之前的字符或字符集 (n <= num <= m).|
|(xyz)|字符集，匹配与 xyz 完全相等的字符串.|
|&#124;|或运算符，匹配符号前或后的字符.|
|&#92;|转义字符,用于匹配一些保留的字符 <code>[ ] ( ) { } . * + ? ^ $ \ \|</code>|
|^|从开始行开始匹配.|
|$|从末端开始匹配.|

正则表达式也可以使用`RegExp()`构造函数来创建。接收两个参数，pattern字符串和标记字符串。

```javascript
let pattern1 = /[bc]at/i;
let pattern2 = new RegExp('[bc]at', 'i');
/ ------------------------------
let pattern1 = /\[bc\]at/i;
let pattern2 = new RegExp('\\[bc\\]at', 'i');
```

#### 5.2.1 RegExp实例属性

- [ ] `global`
- [ ] `ignoreCase`
- [ ] `unicode`
- [ ] `sticky`
- [ ] `lastIndex` 整数，表示在原字符中下一次搜索的开始位置
- [ ] `multiline`
- [ ] `dotAll`
- [ ] `source` 正则表达式的字面量字符串，（不是传给构造函数的的那个字符串）
- [ ] `flags`

```javascript
let pattern = /\[bc\]at/gi;
console.log(pattern.source); // \[bc\]at
let pattern = new RegExp('\\[bc\\]at', 'gi');
console.log(pattern.source); // \[bc\]at
```

#### 5.2.2 RegExp实例方法

`exec()`方法：

主要用于配合捕获组使用

只接收一个参数，即要应用模式的字符串

如果找到了匹配项，则返回包含第一个匹配信息的数组。如果没找到匹配项，则返回`null`

返回的数组虽然是`Array`的实例，但会包含两个额外的属性:`index`和`input`，`index`是字符串中匹配模式的起始位置。`input`是要查找的字符串。

如果模式中有捕获组，返回的数组里，第一个元素是匹配整个模式的字符串，其他元素是与表达式中捕获组匹配的字符串。

如果模式中没有捕获组，返回的数组里只包含一个元素

```javascript
let text = 'mom and dad and me and mimi';
let pattern = /mom( and dad( and me)?)?/gi
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches.input); // mom and dad and me and mimi
console.log(matches[0]); // mom and dad and me
console.log(matches[1]); //  and dad and me
console.log(matches[2]); //  and me
```

设置全局标记与不设置全局标记的区别

```javascript
let text = 'cat, bat, sat, fat';
let pattern = /.at/;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches.input); // cat, bat, sat, fat
console.log(matches[0]); // cat
matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches.input); // cat, bat, sat, fat
console.log(matches[0]); // cat
```

```javascript
let text = 'cat, bat, sat, fat';
let pattern = /.at/g;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches.input); // cat, bat, sat, fat
console.log(matches[0]); // cat
matches = pattern.exec(text);
console.log(matches.index); // 5
console.log(matches.input); // cat, bat, sat, fat
console.log(matches[0]); // bat
console.log(pattern.lastIndex); // 8 实例的lastIndex属性会更新
```

如果模式设置了粘附标记，每次调用`exec()`就只会在`lastIndex`的位置上寻找匹配项，粘附标记会覆盖全局标记。

```javascript
let text = 'cat, bat, sat, fat';
let pattern = /.at/gy;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches[0]); // cat
console.log(pattern.lastIndex); // 3

// 以索引3对应的字符开头，找不到匹配项，exec()返回null
// exec()找不到匹配项，于是将lastIndex设置为0
matches = pattern.exec(text);
console.log(matches); // null 
console.log(pattern.lastIndex); // 0

// 向前设置lastIndex
pattern.lastIndex = 5;
matches = pattern.exec(text);
console.log(matches.index); // 5
console.log(matches[0]); // bat
console.log(pattern.lastIndex); // 8
```

----

`test()`方法：接收一个字符串参数。如果输入的文本与模式匹配，则返回`true`，否则返回`false`

用于只想知道模式是否匹配而不需要匹配实际内容，常在`if`语句中使用

```javascript
let text = '000-00-0000';
let pattern = /\d{3}-\d{2}-\d{4}/;
if (pattern.test(text)) {
    console.log('The pattern is matched.');
} else {
    console.log('Please check your input.')
}
```

验证用户输入

----

继承的方法`toString()`和`toLocaleString()`都返回正则表达式的字面量，返回的是一个字符串

```javascript
let pattern = /\[bc\]at/gi;
console.log(pattern.toString()); // /\[bc\]at/gi
console.log(pattern.toLocaleString()); // /\[bc\]at/gi
// ---
let pattern = new RegExp('\\[bc\\]at', 'gi');
console.log(pattern.toString()); // /\[bc\]at/gi
console.log(pattern.toLocaleString()); // /\[bc\]at/gi
```

正则表达式对象的`valueOf()`函数返回表达式本身，返回的还是个对象

```javascript
// 上面的那个对象pattern
console.log(pattern.valueOf()); // /\[bc\]at/gi
typeof pattern.valueOf(); // object
Number(pattern); // NaN 
// 先调用valueOf(),返回的不是原始类型，调用toString(),返回字符串是原始类型，转换。
```

#### 5.2.3 RegExp构造函数属性

`RegExp`构造函数本身的属性，有的语言里管这种属性叫静态属性。

- [ ] `input`最后搜索的字符串
- [ ] `lastMatch`最后匹配的文本
- [ ] `lastParen`最后匹配的捕获组
- [ ] `leftContext`最后匹配的文本左边的文本
- [ ] `rightContext`最后匹配的文本右边的文本

通过这些属性可以提取出于`exec()`和`test()`执行的操作相关的信息，只有执行了`exec()`和`test()`方法后，这些属性才会被赋予相应的值。
```javascript
let text = 'this is a short summer';
let pattern = /(.)hort/g;
if (pattern.test(text)) { 
    console.log(RegExp.input); // this is a short summer
    console.log(RegExp.lastMatch); // short
    console.log(RegExp.lastParen); // s
    console.log(RegExp.leftContext); // this is a<space>
    console.log(RegExp.rightContext); // <space>summer
}
```

`RegExp`还有其他几个构造函数属性，可以存储最多九个捕获组的匹配项。这些属性通过`RegExp.$1 ~ RegExp.$9`来访问。

```javascript
let text = 'this is a short summer';
let pattern = /(..)or(.)/g;
if (pattern.test(text)) {
    console.log(RegExp.$1); // sh
    console.log(RegExp.$2); // t
}
```

`RegExp`构造函数的所有属性都没有任何Web标准出处，不要在生产环境中使用。

#### 5.2.4 模式局限

- [ ] `\A`和`\Z`锚
- [ ] 联合及交叉类
- [ ] 原子组
- [ ] x（忽略空格）匹配模式
- [ ] 条件式匹配
- [ ] 正则表达式注释

### 5.3 原始值包装类型

为了方便操作原始值，ECMAScript提供了三种特殊的引用类型`Boolean`, `Number`和`String`

每当用到某个原始值的方法或属性时，后台都会创建一个相应的原始包装类型的对象，从而暴露出操作原始值的各种方法。

```javascript
let s1 = 'all day';
let s2 = s1.substring(2);
```

上面的第二行，按理说`s1`是个字符串，不应该有方法啊，但是这里使用了`substring()`方法也没有报错。

在以读模式访问字符串值的任何时候后台都会执行以下三步：

1. 创建一个`String`类型的实例

2. 调用实例上的特定方法

3. 销毁实例

   ```javascript
   let s1 = new String('all day');
   let s2 = s1.substring(2);
   s1 = null
   ```

引用类型和原始包装类型最主要的区别在对象的生命周期。

```javascript
let s1 = 'do it for fun';
let s1.color = 'violet';
console.log(s1.color); // undefined
```

第二行代码运行时会临时创建一个`String`对象，并给这个对象加了一个`color`属性赋值为`'violet'`，而当第三行代码执行时，又临时创建了一个`String`对象，但是第二行代码创建的对象已经被销毁了，新创建的临时对象没有`color`这个属性。

可以显式的使用`Boolean`、`Number`和`String`构造函数创建原始包装对象，不是临时的那种，但最好不这么做

#### 5.3.1 Boolean

```javascript
let booleanObj = new Boolean(false);
if (!!booleanObj) {
    console.log('hello');
}
// hello

let falseObj = new Boolean(false);
let result = falseObj && true;
console.log(result); // true
```

所有对象在布尔表达式中都会自动转换为`true`

`Boolean`实例会重写`valueOf()`方法，返回一个原始值`true`或`false`。`toString()`方法也会被覆盖，返回`true`或`false`

```javascript
let booleanObj = new Boolean(false);
booleanObj.valueOf(); // 布尔值false
booleanObj.toString(); // 'false'
booleanObj.toLocaleString(); // 'false'
```

原始值和原始包装类型的对象之前的几个区别：

```javascript
let falseObj = new Boolean(false);
let falseValue = false;
consosle.log(typeof falseObj); // object(字符串)
console.log(typeof falseValue); // boolean(字符串)
console.log(falseObj instanceof Boolean); // true(布尔值)
console.log(falseValue instanceof Boolean); // false(布尔值)
```

`instanceof`返回布尔值`true`或`false`。

#### 5.3.2 Number

`Number`类型也重写了`valueOf()`、`toLocaleString()`和`toString()`方法。`valueOf()`返回对象的原始值，后两个返回原始值的字符串形式。`toString()`方法可以接收一个参数，作为基数。

```javascript
let numObj = new Number(10);
num.valueOf(); // 10
numObj.toString(); // '10'
numObj.toString(2); // '1010'
numObj.toLocaleString(); // '10'
numObj.toLocaleString(8); // '10' 参数没用
```

----

`Number`类型还提供了几个用于将数值字符格式化为字符串的方法

`toFixed()`接收一个参数表示小数点位数

```javascript
let num = 10;
console.log(num.toFixed(2)); // '10.00'
```

> 为什么0.1+0.2不等于0.3
>
> [为什么0.1+0.2=0.300000004](https://draveness.me/whys-the-design-floating-point-arithmetic/)

`toExponential()`接收一个参数表示结果中小数的位数

```javascript
let num = 10;
console,log(num.toExponential(1)); // '1.0e+1'
```

`toPrecision()`接收一个参数表示结果中数字的总位数

```javascript
let num = 99;
console.log(num.toPrecision(1)); // '1e+2'
console.log(num.toPrecision(2)); // '99'
console.log(num.toPrecision(3)); // '99.0'
```

本质上`toPrecision()`会根据数值和精度来决定调用`toFixed()`还是`toExponential()`

-----

**`isInteger()`方法和安全整数**

ES6新增`Number.isInteger()`方法，返回布尔值，用于判别一个数值是否保存为整数

```javascript
console.log(Number.isInteger(1)); // true布尔值
console.log(Number.isInteger(1.00)); // true
console.log(Number.isInteger(1.01)); // false
```

`IEEE754`数值格式有一个特殊的数值范围，在这个范围内二进制可以表示一个整数值，这个数值范围为`Number.MIN_SAFE_INTEGER`到`Number.MAX_SAFE_INTEGER`之间，也即$-2^{53}+1 \sim 2^{53}-1$

### 5.3.3 String

创建一个`String`对象

```javascript
let stringObj = new String('hello world');
```

3个继承的方法`valueOf()`，`toLocaleString()`，`toString()`都返回对象的原始字符串

每个`String`对象都有一个`length`属性，表示字符串中字符的数量，即使字符串中包含双字节字符，也仍然会按单字符来计数。

**1. JavaScript字符**

```javascript
let message = 'abcde';
console.log(message.length); // 5
```

`charAt()`方法返回给定索引位置的字符，传入一个整数作为参数

```javascript
console.log(message.charAt(2)); // 'c'
```

`chartCodeAt()`方法，返回指定索引位置字符的编码值

```javascript
console.log(message.charCodeAt(2)); // 99 小写字母c的编码为 U+0063
99 === 0x63; // true
```

`fromCharCode()`方法，可以接收多个数值作为参数，并将所有数值对应的字符拼接起来返回。

```javascript
console.log(String.fromCharCode(0x61, 0x62, 0x63, 0x64, 0x65)); // 'abcde'
console.log(String.fromCharCode(97, 98, 99, 100, 101)); // 'abcde'
```

16位只能表示65536个字符，在Unicode中称为基本多语言平面。为了能表示更多的字符，Unicode采用了一个策略，每个字符都使用另外16位去选择一个增补平面。这种每个字符使用两个16位码元的策略称为代理对

```javascript
// 这个笑脸表情符的编码是 U+1F60A 128522
let message = 'ab😊de';
console.log(message.length); // 6
console.log(message.charAt(2)); // �
console.log(message.charAt(3)); // �
console.log(message.charAt(4)); // 'd'

console.log(message.charCodeAt(1)); // 98
console.log(message.charCodeAt(2)); // 55357
console.log(message.charCodeAt(3)); // 56842
console.log(message.charCodeAt(4)); // 100

console.log(String.fromCodePoint(0x1F60A)); // 😊
console.log(String.fromCharCode(97, 98, 55357, 56842, 100, 101)); // ab😊de
console.log(String.fromCodePoint(97, 98, 128522, 100, 101)); // ab😊de

console.log(message.codePointAt(2)); // 128522
console.log(message.codePointAt(3)); // 56842
```

`charAt()`，`charCodeAt()`，`fromCharCode()`，`codePointAt()`，`fromCodePoint()`

```javascript
console.log([...'ab😊de']); // ["a", "b", "😊", "d", "e"] 迭代字符串可以智能地识别代理对的码点
```

**2. normalize()方法**

某些Unicode字符有多种编码方式

```javascript
let a1 = String.fromCharCode(0x00C5);
let a2 = String.fromCharCode(0x212B);
let a3 = String.fromCharCode(0x0041, 0x030A);
// 尽管这三个是一个字符，但计算机看来它们是不一样的
console.log(a1 === a2); // false
```

Unicode提供了4中规范化形式 NFD，NFC，NFKD，NFKC

```javascript
console.log(a1.normalize('NFD') === a2.normalize('NFD')); // true
```

选择同一种规范化形式可以让全等操作符返回true。

**3. 字符串操作方法**

`concat()`，用于将一个或多个字符串拼接成一个新字符串，可以接收任意多个字符串作为参数

```javascript
let stringValue = 'hello ';
let result = stringValue.concat('world', '!');
console.log(result); // 'hello world!'
console.log(stringValue); // 'hello ' 值不变
```

更常用的拼接字符串的方法是用'+'操作符

ECMAScript提供了三个从字符串中提取字符串的方法 `slice()`，`substr()`，`substring()`

```javascript
let stringValue = 'hello world';
console.log(stringValue.slice(3)); // 'lo world' 从位置3开始到最后
console.log(stringValue.substring(3)); // 'lo world'
console.log(stringValue.substr(3)); // 'lo world'
console.log(stringValue.slice(3, 7)); // 'lo w' 从位置3到7 不包括7
console.log(stringValue.substring(3, 7)); // 'lo w' 从位置3到7 不包括7
console.log(stringValue.substr(3, 7)); // 'lo worl' 从位置3 往后7个 7是返回的字符串的字符数量
```

当某个参数是负数时

`slice()`两个参数都表示从后往前的位置，（或者看成用字符串长度加上那个负数）

`substring()`会将所有负参数都转换为0

`substr()`会将第一个负参数当做从后往前的位置（用字符串的长度加上负参数），第二个负参数转换为0

```javascript
let stringValue = 'hello world';
console.log(stringValue.slice(-3)); // 'rld'
console.log(stringValue.slice(-5, -1)); // 'worl'
console.log(stringValue.slice(5, 1)); // '' slice()不会自动把较小的数当做起点

console.log(stringValue.substring(-3, -4)); // ''
console.log(stringValue.substring(3, -4)); // 'hel' substring()会自动把较小的数当做起点
// 等价于console.log(stringValue.substring(0, 3))

console.log(stringValue.substr(-3)); // 'rld'
console.log(stringValue.substr(-3, -1)); // ''
```

**4. 字符串位置方法**

有两个方法用于在字符串中定位子字符串 `indexOf()`和`lastIndexOf()`

都是从字符串中搜索传入的字符串，并返回位置，如果没找到返回-1。`indexOf()`从前往后找，`lastIndexOf()`从后往前找

```javascript
let stringValue = 'hello world';
console.log(stringValue.indexOf('o')); // 4
console.log(stringValue.lastIndexOf('o')); // 7
```

两个方法都可以接收第二个参数，表示开始搜索的位置

```javascript
let stringValue = 'hello world';
console.log(stringValue.indexOf('o', 6)); // 7
console.log(stringValue.lastIndexOf('o', 6)); // 4
console.log(stringValue.indexOf('ld', 2)); // 9
console.log(stringValue.lastIndexOf('lo', 10)); // 3

console.log(stringValue.indexOf('o', 4)); // 4
console.log(stringValue.indexOf('o', 7)); // 7 从位置7包括位置7
// 不管是indexOf()从前往后 还是lastIndexOf()从后往前，指定第二个参数都会从指定的位置开始，不存在说从后往前的时候不包括
```

```javascript
let stringValue = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit';
let positions = new Array();
let pos = stringValue.indexOf('e');
while (pos > -1) {
    positions.push(pos);
    pos = stringValue.indexOf('e', pos + 1);
}
console.log(positions); // [3, 24, 32, 35, 52]
```

**5. 字符串包含方法**

ECMAScript增加了三个用于判断字符串中是否包含另一个字符串的方法

`startsWith()`，`endsWith()`，`includes()`都会从字符串中搜索传入的字符串，并返回一个表示是否包含的布尔值

`startsWith()`检查开始于索引0的匹配项，`endsWith()`检查开始于`string.length - substring.length`的匹配项，`includes()`检查整个字符串

```javascript
let message = 'foobarbaz';

console.log(message.startWith('foo')); // true
console.log(message.endWith('baz')); // true
console.log(message.includex('ba')); // true
```

`startWith()`和`includes()`可以接收第二各参数表示开始搜索的位置

`endWith()`方法也接收第二个参数，用于表示当做字符串末尾的位置，默认为整个字符串的长度

```javascript
console.log(message.endWith(bar, 6)); // true 不包括6位置
```

**6. trim()方法**

创建字符串的一个副本，删除所有空格，并返回结果

```javascript
let stringValue = ' hello world   ';
console.log(stringValue.trim()); // 'hello world'
```

`trimLeft()`和`trimRight()`

```javascript
console.log(stringValue.trimLeft()); // 'hello world   '
console.log(stringValue.trimRight()); // ' hello world'
```

**7. repeat()方法**

```javascript
let stringValue = 'na';
console.log(stringValue.repeat(3)); 'nanana'
```

**8. padStart()和padEnd()方法**

在左侧和右侧填充字符串，第一个参数为想要的长度，第二个参数为想填充的字符，默认为空格

```javascript
let stringValue = 'foo';
console.log(stringValue.padStart(8, 'bar')); // barbafoo 会截断
console.log(stringValue.padStart(2)); // foo
console.log(stringValue.padStart(2, 'o')); // foo
```

**9. 字符串的迭代与结构**

一周目迭代器是啥 :smiley_cat: 还有原型

字符串原型上暴露了一个`@@iterator`方法，表示可以迭代字符串的每个字符

手动使用迭代器：

```javascript
let message = 'abc';
let stringIterator = message[Symbol.iterator]();
console.log(stringIterator.next()); // {value: "a", done: false}
console.log(stringIterator.next()); // {value: "b", done: false}
console.log(stringIterator.next()); // {value: "c", done: false}
console.log(stringIterator.next()); // {value: undefined, done: true}
```

在`for-of`循环中，可以通过这个迭代器按序访问每个字符：

```javascript
for (const of 'abc') {
    console.log(c)
}
/* a
b
c */
```

有了这个迭代器之后，字符串就可以通过结构操作符来解构了

```javascript
let message = 'abcde';
console.log([...message]); // ['a', 'b', 'c', 'd', 'e']
```

**10. 字符串大小写转换**

`toLowerCase()`，`toUpperCase()`，`toLocaleLowerCase()`，`toLocaleUpperCase()`

后两个主要是旨在基于特定地区实现大小写转换

**11. 字符串模式匹配方法**

`match()`

本质上跟`RegExp`对象的`exec()`方法相同

接收一个参数，可以是一个正则表达式字符串，也可以是一个`RegExp`对象

```javascript
let text = 'fat cat mimi and pidan';
let pattern = /.at/;
let matches = text.match(pattern);
console.log(matches.index);
console.log(matches[0]);
console.log(pattern.lastIndex);

console.log(text.match('.at')); // 也是可以匹配的 这样怎么给flag呢
```

`search()`

接收一个参数，可以是一个正则表达式字符串，也可以是一个`RegExp`对象

返回模式第一个匹配的位置的索引，如果没找到返回-1

始终从字符串开头向后匹配

```javascript
let text = 'fat cat mimi';
let pos = text.search(/.at/); // 0
let pos1 = text.search('.i'); // 8
```

`replace()`

接收两个参数

第一个参数可以是一个`RegExp`对象，也可以是一个字符串(不会被当成正则表达式字符串)

第二个参数可以字符串可以是一个函数，表示想要替换成的内容

```javascript
let text = 'fat cat ate bat';
let result = text.replace('at'); // "fundefined cat ate bat"
let result = text.replace('at', 'ond'); // "fond cat ate bat" 只会替换第一个at
let result = text.replace(/at/g, 'ond'); // "fond cond onde bond"
console.log(text); // 'fat cat ate bat' text不会变，在副本上替换
```

在第二个参数是字符串的情况下，有几个特使的字符序列，可以用来插入正则表达式操作的值

| 字符序列 | 替换文本                                    |
| -------- | ------------------------------------------- |
| $$       | $                                           |
| $&       | 匹配整个模式的字符串(同RegExp.lastMatch)    |
| $'       | lastMatch之后的字符串（同RegExp.rightMatch) |
| $`       | lastMatch之前的字符串（同RegExp.leftMatch)  |
| $n       | 匹配的第n个捕获组（n 1~9）                  |
| $nn      | 匹配的第nn个捕获组（n 01~99）               |

```javascript
let text = 'cat, bat, sat, fat';
result = text.replace(/(.at)/g, 'word ($1)');
console.log(result); // 'word (cat), word (bat), word (sat), word (fat)'
```

```javascript
function htmlEscape(text) {
    return text.replace(/[<>"&]/g, function(match, pos, originalText) {
        switch (match) {
            case '<':
                return '&lt;';
            case '>':
                return '&gt';
            case '&':
                return '&amp;';
            case '"':
                return '&quot;';
        }
    });
}
console.log(htmlEscape('<p class="greeting">Hello</p>'));
// &lt;p class=&quot;greeting&quot;&gtHello&lt;/p&gt
```

```javascript
let text = 'fat cat mimi and pidan';
let pattern = /.i/;
let matches = text.match(pattern);
matches; // ["mi", index: 8, input: "fat cat mimi and pidan", groups: undefined]
// replace()里如果第二个参数是function时，match的结果会自己传给function吗？
```

`split()`方法

根据传入的分隔符将字符串拆分成数组

作为分隔符的参数可以是字符串也可以是`RegExp`对象，同样字符串不会被当成正则表达字符串

还可以传入第二个参数，即数组的大小

```javascript
let colorText = 'red, blue, green, yollow';
let color1 = colorText.split(','); // ["red", " blue", " green", " yollow"]
let color2 = colorText.split(',', 2); // ["red", " blue"]
let color3 = colorText.split(/[^,]+/); // ["", ",", ",", ",", ""]
```

**12. localeCompare()方法**

比较两个字符串，返回：

- [ ] 按字母表顺序，字符串排在字符串参数前面，返回负值(通常是-1)
- [ ] 按字母表顺序，字符串排在字符串参数后面，返回正值(通常是1)
- [ ] 字符串与字符串参数相等，返回0

```javascript
let stringValue = 'yellow';
console.log(stringValue.localeCompare('bule')); // 1
console.log(stringValue.localeCompare('zoo')); // -1
console.log(stringValue.localeCompare('yes')); // -1 l在s前
console.log(stringValue.localeCompare('yellow')); // 0
```

```javascript
function determinOrder (value) {
    let result = stringValue.localeCompare(value);
    if (result < 0) {
        console.log(`yellow comes before ${ value }`);
    } else if (result > 0) {
        console.log(`yellow comes after ${ value }`);
    } else {
        console.log(`yellow equals ${ value }`);
    }
}
determinOrder('haha');
```

`localeCompare()`会根据所在地区确定比较字符串的规则。

### 5.4 单例内置对象

> 任何由ECMAScript实现提供、与宿主环境无关，并在ECMAScript程序开始执行时就存在的对象

这意味着不用显式的实例化内置对象，`Object`，`Array`，`String`这些都算内置对象

另外两个单例内置对象

#### 5.4.1 Global

ECMAScript中最特别的对象，因为代码不会现实的访问它

`Global`对象为一种兜底对象，它针对的是不属于任何对象的属性和方法。事实上，不存在全局变量或全局函数这种东西，在全局作用域中定义的变量和函数都会变成`Global`对象的属性和方法。

前面接触到的一些方法，比如`isNaN()`, `isFinity()`, `parseInt()`, `parseFloat()`都是`Global`对象的方法。

`Global`对象的其他属性

**1. URL编码方法**

`encodeURI()`和`encodeURIComponent()`

`decodeURI()`和`decodeURIComponent()`

`encodeURI()`(`decodeURI()`)，不会编码（解码）属于URL组件的特殊字符，比如`: / ? #`。而`encodeURIComponent()`(`decodeURIComponent()`)，会编码（解码）它发现的所有非标准字符。

```javascript
let uri = 'http://www.wrox.com/illegal value.js#start';

// http://www.wrox.com/illegal%20value.js#start
console.log(encodeURI(uri));

// http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start
console.log(encodeURIComponent(uri));

// http%3A%2F%2Fwww.wrox.com%2Fillegal value.js%23start
console.log(decodeURI('http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start'));

// http://www.wrox.com/illegal value.js#start
console.log(decodeURIComponent('http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start'));
```

通常使用`encodeURI()`编码整个URI，但只使用`encodeURIComponent()`编码那些会追加到已有URI后面的字符串。

**2. `eval()`方法**

**3. `Global`对象属性**

| 属性             | 说明                       |
| ---------------- | -------------------------- |
| `undefined`      | 特殊值`undefined`          |
| `NaN`            | 特殊值`NaN`                |
| `Infinity`       | 特殊值`Infinity`           |
| `Object`         | `Object`的构造函数         |
| `Array`          | `Array`的构造函数          |
| `Function`       | `Function`的构造函数       |
| `Boolean`        | `Boolean`的构造函数        |
| `String`         | `String`的构造函数         |
| `Number`         | `Number`的构造函数         |
| `Date`           | `Date`的构造函数           |
| `RegExp`         | `RegExp`的构造函数         |
| `Symbol`         | `Symbol`的构造函数         |
| `Error`          | `Error`的构造函数          |
| `EvalError`      | `EvalError`的构造函数      |
| `RangeError`     | `RangeError`的构造函数     |
| `ReferenceError` | `ReferenceError`的构造函数 |
| `SyntaxError`    | `SyntaxError`的构造函数    |
| `TypeError`      | `TypeError`的构造函数      |
| `URIError`       | `URIError`的构造函数       |

**4. window对象**

虽然没有规定访问`Global`对象的方式，但浏览器将`window`对象实现为`Global`对象的代理，因此所有在全局作用域中生命的变量和函数都会变成`window`对象的属性和方法。

```javascript
var color = 'red';
function logColor () {
    console.log(window.color);
}
window.logColor(); // red
```

另一种获取`Global`对象的方法，==过段时间重看==

#### 5.4.2 Math

`Math`对象作为保存数学公式、信息和计算的地方

**1. Math对象属性**

| 属性           | 说明                   |
| -------------- | ---------------------- |
| `Math.E`       | e的值                  |
| `Math.LN10`    | $ln10$的值             |
| `Math.LN2`     | $ln2$的值              |
| `Math.LOG2E`   | $log_2e$的值           |
| `Math.LOG10E`  | $log_{10}e$的值        |
| `Math.PI`      | $\pi$的值              |
| `Math.SQRT1_2` | $\sqrt\frac{1}{2}$的值 |
| `Math.SQRT2`   | $\sqrt2$的值           |

**2. `min()`和`max()`**

```javascript
let max = Math.max(1, 2, 3, 4, 5, 6); // 6
let min = Math.min(1, 2, 3, 4, 5, 6); // 1
```

```javascript
let values = [1, 2, 3, 4, 5, 6];
let max = Math.max(...values);
```

**3. 舍入方法**

- [ ] `Math.ceil` 进一取整，向上取整，结果是整数
- [ ] `Math.floor`向下取整，结果是整数
- [ ] `Math.round()`四舍五入，结果是整数
- [ ] `Math.fround()`返回数值最接近的的单精度（32位）浮点表示

**4. `random()`**

`Math.random()`方法返回一个0~1范围内的随机数，包含0不包含1

基于如下公式使用`Math.random()`从一组整数中随机选择一个数

```
num = Math.floor(Math.random() * total_num_of_choices + first_possible_value)
```

如果从1~10中随机选一个整数

```javascript
let num = Math.floor(Math.random() * 10 +1);
```

如果想从2~100中随机选一个整数

```javascript
let num = Math.floor(Math.random() * 99 + 2);
```

用函数来计算

```javascript
function generateRandInt (lowerNum, upperNum) {
    let num = Math.floor(Math.random() * (upperNum - lowerNum +1) + lowerNum);
    return num;
}
```

```javascript
let colorArr = ['red', 'blue', 'yellow', 'purple', 'violet'];
console.log(colorArr[generateRandInt(0, colorArr.length - 1)]);
```

**5. 其他方法**

==P134==