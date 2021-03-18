[TOC]

## Chap6 集合引用类型

### 6.1 Object 

```javascript
// 显式创建Object对象的两种方法
// 使用构造函数
let person = new Object();
person.name = 'poo';
person.age = 24;

// 使用对象字面量
let person = {
  name: 'poo',
  age: 24
};

let person = {
  name: 'poo',
  age: 24,
  5: true // 数值属性会自动转换成字符串
};

let person = {};
person.name = 'poo';
person.age = 24;
```

```javascript
// 一般会比较倾向于使用对象字面量表示法
//代码更少也更有封装的感觉

function displayInfo (args) {
  let output = '';
  if (typeof args.name == 'string') {
    output += 'Name: ' + args.name + '\n';
  }
  if (typeof args.age == 'number') {
    output += 'Age: ' + args.age + '\n';
  }
  alert(output);
}

displayInfo({name: 'poo', age: 24});
```

![image-20210313153021345](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/13/20210313153021.png)

```javascript
// 存取属性
let person = {
  name: 'poo',
  age: 24
}

console.log(person.name);
console.log(person['name']);
```

----

### 6.2 Arrray

#### 6.2.1 创建数组

```javascript
// 创建数组
// 构造函数
let colors = new Array ();
let colors = new Array(20);
let colors = new Array('red', 'blue', 'yellow');
let colors = Array('red');
let colors = Array(3);
// 数组字面量
let colors = ['red', 'yellow', 'blue'];
let colors = [];
let nums = [1, 2, 3,];
let colors = ['red', 'blue', 'yellow',]                  
```

`Array`构造函数还有ES6新增的两个用于创建数组的静态方法`Array.from()`和`Array.of()`

`Array.from()`用于将类数组结构转换为数组实例，`Array.of()`用于将一组参数转换为数组实例

`Array.from()`第一个参数为类数组对象，即任何可迭代结构，或有一个length属性和可索引元素的结构

```javascript 
// 字符串是可迭代结构
// 集合对象（数组、Set/Map集合）和字符串都是可迭代对象，这些对象都有默认的迭代器和Symbol.iterator属性。
console.log(Array.from('matt')); // ['m', 'a', 't', 't']

// 可以使用任何可迭代对象
const iter = {
  *[Symbal.iterator] () {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
  }
};
console.log(Array.from(iter)); // [1, 2, 3, 4]

// 可以使用from()将集合和映射转换为一个新数组
const m = new Map().set(1, 2)
                   .set(3, 4);
const s = new Set().add(1)
                   .add(2)              
                   .add(3)              
                   .add(4);
console.log(Array.from(m)); // [[1, 2], [3, 4]]
console.log(Array.from(s)); // [1, 2, 3, 4]   

```

```javascript
// arguments对象可以被轻松地转换为数组
function getArgsArray() {
  return Array.from(arguments);
}
console.log(getArgsArray('red', 'yellow', 'blue')); // ['red', 'yellow', 'blue']

// from()也能转换带有必要属性的自定义对象
const arrayLikeObject = {
  0: 1,
  1: 2,
  2: 3,
  3: 4,
  length: 4
};
console.log(Array.from(arrayLikeObject)); // [1, 2, 3, 4]
// 没有length属性就不行
const arrayLikeObject = {
  0: 1,
  1: 2,
  2: 3,
  3: 4,
};
console.log(Array.from(arrayLikeObject)); // []
```

`arguments`对象，是一个对应于传递给函数的参数的类数组对象。`arguments`对象是所有（非箭头）函数中都可用的**局部变量**。你可以使用`arguments`对象在函数中引用函数的参数。此对象包含传递给函数的每个参数，第一个参数在索引0处。`arguments`对象不是一个 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array) 。它类似于`Array`，但除了length属性和索引元素之外没有任何`Array`属性。

```javascript
// Array.from()对现有数组执行浅复制
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1);
alert(a1 === a2); // false
a2[0] = 0;
console.log(a1); [1, 2, 3, 4]
console.log(a2); [0, 2, 3, 4]
```

<a id='深/浅拷贝'>[深拷贝浅拷贝](https://juejin.cn/post/6844903493925371917) 现在看的迷迷糊糊的，等有一定知识储备后一定要回来看看啊</a>

`Array.from()`还接受第二个可选的映射函数参数。这个函数可以直接增强新数组的值，而无需通过 `Array.from().map()` 先创建一个中间数组。

还可以接收第三个参数用于指定映射函数中 `this` 的值。重写 `this` 在箭头函数中不适用

```javascript
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1, x => x * x);
const a3 = Array.from(a1, function (x) { return x**this.exponent }, {exponent: 3});
console.log(a2); // [1, 4, 9, 16]
console.log(a3); // [1, 8, 27, 64]
```



----

`Array.of()`可以把一组参数转换为数组，用于替代ES6之前的`Array.prototype.slice.call(arguments)`

```javascript
console.log(Array.of(1, 2, 3, 4)); // [1, 2, 3, 4]
console.log(Array.of(undefined)); // [undefined]
```

```javascript
function test () {
    let args1 = Array.prototype.slice.call(arguments, 1, 3);
    let args2 = Array.prototype.slice.apply(arguments, [1, 3]);
    console.log(args1, args2)
}
test('a', 'b', 'c', 'd'); // ['b', 'c'] ['b', 'c']
```

调用 `Array` 原型链上的函数 `slice()` ，`slice()` 是一个函数是有 `call()` 方法的，使用 `call()` 方法将 `slice()` 函数体内的 `this` 值设置为指向 `arguments` 对象，同时将 `1, 3` 作为参数传给 `slice()` 函数。

----



#### 6.2.2 数组空位

ES6新增的方法和迭代器和之前版本存在的方法行为不同。

```javascript
const options = [,,,,,]; // 创建包含五个元素的数组
console.log(options.length); // 5
console.log(options); [,,,,,]
```

ES6新增的方法普遍将这些空位当成存在的元素，只不过值为`undefined`

```javascript
console.log(Array.from([,,,])); // [undefined, undefined, undefined]
console.log(Array.of(...[,,,])); // [undefined, undefined, undefined]
```

```javascript
let options = [1, , , , 5];
for (const [idx, value] of options.entries()) {
    console.log(value);
}
/* 1
undefined
undefined
undefined
5 */
```

ES6 之前的方法则会忽略这个空位，但具体的行为也会因具体的方法不一样

```javascript
const options = [1, , , , 5];
// map()会跳过该位置
console.log(options.map(() => 6)); // [6, empty × 3, 6]
// join()会当做空字符串
console.log(options.join('-')); // 1----4
```

避免使用数组空位



#### 6.2.3 数组索引

```javascript
let colors = ['red', 'blue'];
colors[3] = 'yellow';
console.log(colors); // ["red", "blue", empty, "yellow"]
console.log(colors[2]); // undefined
```

数组`length`属性特殊在，它并不是一个只读的，通过修改`lengh`属性可以再数组末尾增减元素

```javascript
let colors = ['red', 'yellow', 'blue'];
colors.length = 2;
console.log(colors); // ["red", "yellow"]
```

可以这样很方便的向数组的末尾添加元素，不用去数数了

```javascript
let colors = ['red', 'yellow', 'blue'];
colors[colors.length] = 'black';
console.log(colors); // ["red", "yellow", "blue", "black"]
```



#### 6.2.4 检测数组

在只有一个网页（因而只有一个全局作用域）的情况下，使用`instanceof`操作符就够了。   <a id='作用域'>一周目到这儿不深究作用域哈</a>

```javascript
if (value instanceof Array) {
    // 
}
```

使用`instanceof`假定只有一个全局执行上下文，如果网页里有多个框架，可能涉及两个不同的全局执行上下文。

为解决这个问题ECMAScript提供了`Array.isArray()`方法。确定一个值是否为数组，而不用管它是那个全局执行上下文中创建的，

```javascript
if (Array.isArray(value)) {
    //
}
```



#### 6.2.5 迭代器方法

ES6中，`Array`原型上暴露了3个用于检索数组内容的方法：

- [ ] `keys()`，返回数组索引迭代器
- [ ] `values()`，返回数组元素迭代器
- [ ] `entries()`，返回索引/元素对的迭代器

```javascript
const a = ['foo', 'bar', 'baz', 'qux'];
// 这些方法都返回迭代器，所以可以将它们的内容通过Array.from()转换为数组实例
const aKeys = Array.from(a.keys()); // [0, 1, 2, 3]
const aValues = Array.from(a.values()); // ['foo', 'bar', 'baz', 'qux']
const aEntries = Array.from(a.entries()); // [[0, 'foo'], [1, 'bar'], [2, 'baz'], [3, 'qux']]

for (const [idx, element] of a.entries()) {
  alert(idx);
  alert(element);
}
```



#### 6.2.6 复制和填充方法

ES6新增

- [ ] `copyWithin()`
- [ ] `fill()`

`fill()`向一个已有的数组中插入全部或部分相同的值，不会改变原有数组的大小。包含开始索引不包含结束索引。开始索引指定开始填充的位置（可选），结束索引用于指定结束的位置（不指定的话，默认到末尾）。负索引从数组末尾开始算。

```javascript
const zeroes = [0, 0, 0, 0, 0];
zeroes.fill(); // [undefined, undefined, undefined, undefined, undefined]
zeroes.fill(5); // [5, 5, 5, 5, 5]

// 用6填充索引大于等于3的元素
zeroes.fill(6, 3); // [5, 5, 5, 6, 6]

// 用7填充索引大于等于1切小于3的元素
zeroes.fill(7, 1, 3); // [5, 7, 7, 6, 6]

// 用8填充索引大于等于1且小于4的元素
zeroes.fill(8, -4, -1); // [5, 8, 8, 8, 6]

console.log(zeroes); // [5, 8, 8, 8, 6] 这里是变了的
```

`fill()`静默忽略超出数组边界、零长度及方向相反的索引范围的内容

```javascript
zeroes.fill(0);
zeroes.fill(1, -10, -6); // 开始结束索引都过低 [0, 0, 0, 0, 0]
zeroes.fill(1, 10, 15); // 开始结束索引都过高 [0, 0, 0, 0, 0]
zeroes.fill(1, 4, 1); // 索引反向 [0, 0, 0, 0, 0]
zeroes.fill(1, 3, 10); // 索引部分可用，填充可用部分 [0, 0, 0, 1, 1] // fill()不改变数组大小
```

用`const`声明了变量`const zeroes = [0, 0, 0, 0, 0]`，如果再`zeroes = [1, 1, 1, 1, 1]`会报错，因为不能重新赋值给一个`constant`变量。使用`zeroes.fill(1)`后再`console.log(zeroes)`发现输出的结果变了。为啥呢？

数组作为对象，在建立后存储在堆内存中，而变量`zeroes`实际上是一个存放在栈内存的指针，这个指针指向堆内存中的地址。

`const zeroes = [0, 0, 0, 0, 0]`就是把数组对象`[0, 0, 0, 0, 0]`再堆内存中的地址赋值给常量`zeroes`。

`const zeroes = [1, 1, 1, 1, 1]`相当于是想重新创建了一个数组对象`[1, 1, 1, 1, 1]`，存在堆区，然后把这个地址再给`zeroes`，让它记着。这不就相当于是要改变常量`zeroes`的值嘛，这不可以。

`zeroes.fill(1)`，只是改变了`zeroes`指向的数组对象的值，`zeroes`自己从始至终都没变过。

----

`copyWithin()`，批量复制方法，会按照指定范围浅复制数组中的部分内容，然后将它们插入到指定索引开始的位置，不会改变数组的长度。开始和结束索引的规则都和`fill()`一样。

```javascript
let ints,
	reset = () => ints = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
reset();

// 从ints中复制索引0开始的内容，插入到索引5开始的位置
// 在源索引或目标索引到达数组边界时停止
ints.copyWithin(5); // [0, 1, 2, 3, 4, 0, 1, 2, 3, 4]

// 复制索引5开始的内容，插到索引0的位置
ints.copyWithin(0, 5); // [0, 1, 2, 3, 4, 0, 1, 2, 3, 4]

reset();
// 复制索引0开始到3的内容插入到索引4的位置
ints.copyWithin(4, 0, 3); // [0, 1, 2, 3, 0, 1, 2, 7, 8, 9]

reset();
ints.copyWithin(4, 7, 10); // [0, 1, 2, 3, 7, 8, 9, 7, 8, 9]
```

####  6.2.7 转换方法

所有对象都有`toLocaleString()`，`toString()`，`valueOf()`方法。

数组的`valueOf()`返回数组本身，`toString()`返回的是由数组中每个值得等效字符串拼接而成的一个逗号分隔的字符串。

```javascript
let colors = ['red', 'yellow', 'blue'];
console.log(colors.toString()); // 'red,yellow,blue'
console.log(colors.valueOf()); // ["red", "yellow", "blue"]
alert(colors); // 'red,yellow,blue' alert期待一个字符串，后台调用toString()
```

`toLocaleString()`方法，会得到一个逗号分隔的数组值的字符串。为了得到最终字符串，会调用每个元素的`toLocaleString()`方法。

```javascript
let person1 = {
    toLocaleString () {
        return 'Nikolaos';
    },
    toString () {
        return 'Nicholas';
    }
};

let person2 = {
    toLocaleString () {
        return 'Grigorios';
    },
    toString () {
        return 'Greg';
    }
}

let people = [person1, person2];
alert(people); // Nicholas,Greg
alert(people.toString()); // Nicholas,Greg
alert(people.toLocaleString()); // Nikolaos,Grigorios
```

如果不想使用逗号做分隔符，可以使用`join()`

```javascript
let colors = ['red', 'yellow', 'blue'];
console.log(colors.join('-')); // red-yellow-blue
```

如果不给`join()`传入任何参数，或传入`undefined`，仍然会使用`,`做分隔符

```javascript
console.log(colors.join()); // red,yellow,blue
console.log(colors.join(undefined)); // red,yellow,blue
```

#### 6.2.8 栈方法

数组对象可以像栈一样，是一种可以限制插入和删除项的数据结构。

栈是一种后进先出（LIFO）的数据结构

ECMAScript提供了`push()`和`pop()`方法，以实现类似栈的行为

`push()`方法接受任意数量的参数，并将他们添加到数组末尾，返回数组的最新长度。`pop()`方法则用于删除数组的最后一项，同时减少数组的`length`，返回被删除的项。

```javascript
let colors = new Array();
let count = colors.push('red', 'yellow');
alert(count); // 2
count = colors.push('blue');
alert(count); // 3
let item = colors.pop();
alert(item); // 'blue'
alert(colors.length); // 2
```

#### 6.2.9 队列方法

队列是一先进先出（FIFO）形式限制访问

`shift()`，会删除数组的第一项并返回它，数组长度减一。

使用`push()` 和 `shift()` 可以把数组当成队列来使用。

```javascript
let colors = new Array();
let count = colors.push('red', 'yollow', 'blue');
alert(count); // 3

count = colors.push('white');
alert(count); // 4

let item = colors.shift();
alert(item); // 'red'
alert(colors.length); // 3
```

#### 6.2.10 排序方法

`reverse()` 和 `sort()`

```javascript
let values = [1, 2, 3, 4, 5];
values.reverse();
alert(values); // '5,4,3,2,1'

let values = [1, 2, 3, 5, 4];
values.reverse();
alert(values); // '4,5,3,2,1'
```

`sort()`默认按升序排列，`sort()`会在每一项元素上调用 `String()` 转型函数，然后比较字符串来决定顺序。

```javascript
let values = [0, 1, 5, 10, 15];
values.sort();
alert(values); // '0,1,10,15,5'
```

`sort()`方法可以接收一个**比较函数**，用于判断哪个值应该排在前面

比较函数接收两个参数，如果第一个参数排在第二个参数的左边，返回负值。如果第一个参数和第二个参数相等，返回0。如果第一个参数排在第二个参数右边，返回正值。

升序排列：

```javascript
function compare (value1, value2) {
    if (value1 < value2) {
        return -1;
    } else if (value1 > value2) {
        return 1;
    } else {
        return 0;
    }
}

let values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); // '0,1,5,10,15'
// 因为这里的 < > 是数值比较符 不管两边的数据类型是啥都先试着换成Number原始类型
// 不一定非得是-1，1， 可以是其他负值，正值
```

降序排列：

```javascript
function compare (value1, value2) {
    if (value1 < value2) {
        return 1;
    } else if (value1 > value2) {
        return -1;
    } else {
        return 0;
    }
}
alert(values.sort(compare)); // '15,10,5,1,0'
```

比较函数还可以简写为一个箭头函数

```javascript
let values = [0, 1, 5, 10, 15];
values.sort((val1, val2) => val1 > val2 ? 1 : val1 < val2 ? -1 : 0); // 升序
alert(values) // 0,1,5,10,15
```



如果数组的元素是数值，或者是`valueOf()`方法返回数值的对象（如 Date 对象），这个比较函数可以写的更简单

```javascript
function compare (value1, value2) {
    return value1 - value2; // - 数值操作符 这里这个比较函数是升序排列的
}
// 降序排列 return value2 - value1;
```

#### 6.2.11 操作方法

`concat()`，在现有数组全部元素的基础上创建一个新数组。会先创建一个当前数组的副本，然后把参数添加到副本的末尾，最后返回这个新构建的数组。

```javascript
let colors = ['red', 'yollow', 'blue'];
let colors2 = colors.concat('green', ['black', 'violet']);
console.log(colors); // ["red", "yollow", "blue"]
console.log(colors2); // ["red", "yollow", "blue", "green", "black", "violet"]  被打平
```

打平参数的行为可以重写，在参数数组上指定一个特殊的符号`Symbol.isConcatSpreadable`

```javascript
let colors = ['red', 'green'];
let newColors = ['white', 'blue'];
let moreColors = {
    [Symbol.isConcatSpreadable]: true,
    length: 2,
    0: 'yellow',
    1: 'violet'
}
newColors[Symbol.isConcatSpreadable] = false;
console.log(colors.concat(newColors)); // false 不打平 ["red", "green", ['white', 'blue']]
console.log(colors.concat(moreColors)); // true 打平 ["red", "green", "yellow", "violet"]
// moreColors是一个Object对象，所以concat()的时候是调用了Array.from()吗？
```

打平不能用来一下子把嵌套数组全都打平了，嵌套数组想要获得所有的内容，要遍历，现在还不会弄

```javascript
let a = [1];
let c = [1, [2, 3]];
a.concat(c); // [1, 1, Array(2)]
```

`slice()`用于创建一个包含原有数组中一个或多个元素的新数组

接收一个或两个参数，开始索引和结束索引，返回的不包含结束索引位置的元素

```javascript
let colors = ['red', 'purple', 'blue', 'yellow'];
let colors2 = colors.slice(1);
let colors3 = colors.slice(1, 3);
alert(colors2); // 'purple,blue,yellow'
alert(colors3); // 'purple,blue'
```

`splice()`，主要目的是在数组中插入元素，三种不同的使用方式

- [ ] **删除**。传入两个参数，要删除的第一个元素位置和要删除的元素的数量
- [ ] **插入**。传入三个参数，开始位置，0（要删除的元素数量），要插入的元素
- [ ] **替换**。传入开始位置，要删除的元素的个数，要插入的任意多个元素

`splice()`方法始终返回一个包含从原数组中被删除的元素的数组，如果没有删除元素，则返回一个空数组。

```javascript
let colors = ['red', 'purple', 'blue', 'yellow'];
let removed = colors.splice(0, 1);
console.log(colors); // ['purple', 'blue', 'yellow']
console.log(removed); // ['red']

removed = colors.splice(1, 0, 'white', 'violet');
console.log(colors); // ['purple', 'white', 'violet', 'blue', 'yellow']
console.log(removed); // []

removed = colors.splice(1, 2, 'black', 'skyblue');
console.log(colors); // ['purple', 'black', 'skyblue', 'blue', 'yellow']
console.log(removed); // ['white', 'violet']
```

#### 6.2.12 搜索和位置方法

两种搜索数组的方法，按严格相等搜索和按断言函数搜索。

**1. 严格相等**

`indexOf()`，接收两个参数，要查找的元素和可选的起始搜索位置。从前往后搜，返回要查找的元素在数组中的位置，没找到返回-1。

`lastIndexOf()`，接收两个参数，要查找的元素和可选的起始搜索位置。从后往前搜，返回要查找的元素在数组中的位置，没找到返回-1。

`includes()`，ECMAScript7新增，接收两个参数，要查找的元素和可选的起始搜索位置。从前往后搜。返回布尔值。

这三种方法在比较第一个参数跟元素是否相等时都是使用全等`===`比较的

```javascript
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
alert(numbers.indexOf(4)); // 3
alert(numbers.lastIndexOf(4)); // 5
alert(numbers.includes(4)); // true

let person = { name: 'Nicholas' };
let people = [{ name: 'Nicholas' }];
let morePeople = [person];
alert(people.indexOf(person)); // -1
alert(morePeople.indexOf(person)); // 0 
alert(people.includes(person)); // false
alert(morePeople.includes(person)); // true
```

**2. 断言函数**

ECMAScript允许按照定义的断言函数搜索数组，每个索引都会调用这个函数，断言函数的返回值决定了相应索引的元素是否被认为匹配

断言函数接收三个参数，元素，索引和数组本身

`find()` 和 `findIndex()` 函数可以接收断言函数，`find()`返回第一个匹配的元素，`findIndex()` 返回第一个匹配元素的索引，都是从数组的最小索引开始验证匹配。都可以接收第二个可选参数，用于指定<a id='断言函数内部this'>断言函数内部this的值</a>

```javascript
const people = [
    {
        name: 'Matt',
        age: 30
    },
    {
        name: 'Nicholas',
        age: 27
    }
];
console.log(people.find((element, idx, arr) => element.age < 28)); // {name: "Nicholas", age: 27}
alert(people.find((element, idx, arr) => element.age < 28)); // [object Object]
alert(people.findIndex((element, idx, arr) => element.age < 28)); // 1
```

```javascript
const people = [
    {
        toString: () => 'name: Matt\nage: 30',
        name: 'Matt',
        age: 30
    },
    {
        toString: () => 'name: Nicholas\nage: 27', // 这样也挺麻烦的 对象怎么引用自己的属性呢？引用自己的属性 写成模板字面量
        name: 'Nicholas',
        age: 27
    }
];
alert(people.find((element, idx, arr) => element.age < 28)); // 
```



找到匹配项后，这两个方法都不会再继续搜索

```javascript
const evens = [2, 4, 6, 4];
evens.find((element, idx, arr) => {
    console.log(element);
    console.log(idx);
    console.log(arr);
    return element === 4;
});
/*
2
0
[2, 4, 6, 4]
4
1
[2, 4, 6, 4]
*/
```

#### 6.2.13 迭代方法

ECMAScript为数组定义了5个迭代方法，每个方法接收两个参数：以每一项为参数运行的函数，以及可选择的作为函数运行上下文的作用域对象（影响函数中的this值）

- [ ] `every()`，对数组的每一项都运行传入的函数，如果对每一项函数都返回`true`，则这个方法返回`true`
- [ ] `filter()`，对数组的每一项都运行传入的函数，函数返回`true`的项会组成数组之后返回
- [ ] `forEach()`，对数组的每一项都运行传入的函数，没有返回值
- [ ] `map()`，对数组的每一项都运行传入的函数，返回由每次函数调用的结果组成的数组
- [ ] `some()`，对数组的每一项都运行传入的函数，如果有一项函数返回`true`，则这个方法返回`true`

这些方法都不改变调用它们的数组。

```javascript
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let everyResult = numbers.every((item, idx, arr) => item > 2);
alert(everyResult); // false

let someResult = numbers.some((item, idx, arr) => item > 2);
alert(someResult); // true

let filterResult = numbers.filter((item, idx, arr) => item > 2);
alert(filterResult); // 3,4,5,4,3

let mapResult = numbers.map((item, idx, arr) => item ** 2);
alert(mapResult);
```

`forEach()`函数，本质上相当于使用`for`循环遍历数组

#### 6.2.14 归并方法

`reduce()` 和 `reduceRight()`，两个方法都会迭代数组的所有选项，并在此基础上构建一个最终返回值。

`reduce()` 从第一项开始遍历到最后一项，`reduceRight()` 从最后一项开始遍历到第一项。

两个方法都接收两个参数：对每一项都会运行的归并函数，以及可选的以之为归并起点的初始值。

作为参数传给`reduce()` 和 `reduceRight()` 的函数接收4个参数：上一个归并值，当前项，当前项的索引和数组本身。这个函数返回的任何值都会作为下一次调用同一个函数的第一个参数。

如果没有给`reduce()` 和 `reduceRight()` 传入作为归并起点的第二个参数，则会从数组的第二项开始第一次迭代，数组的第一项会传给归并函数作第一个参数

```javascript
let values = [1, 2, 3, 4, 5];
let reduceRes = values.reduce((a, b, idx, arr) => a + b);
alert(reduceRes); // 15
let reduceRes1 = values.reduce((a, b, idx, arr) => a + b, 0);
alert(reduceRes1); // 15
let reduceRes2 = values.reduce((a, b, idx, arr) => a + b, 1);
alert(reduceRes2); // 16
let reduceRes3 = values.reduce((a, b, idx, arr) => a + b, 5);
alert(reduceRes3); // 20
```

是使用`reduce()` 还是 `reduceRight()`，只取决于遍历数组元素的方向

### 6.3 定型数组

<a id='定型数组'>先过了哈</a>

### 6.4 Map

ES6之前，JavaScript中实现“键/值”存储主要要靠`Object`来实现，使用对象属性作为键，通过对象属性的引用来得到对应的值。

ES6新增的特性`Map` ，是一种新的集合类型，给JS带来了真正的键/值存储机制。

#### 6.4.1 基本API

创建一个空映射

```javascript 
let m = new Map();
```

在创建的同时初始化需要传入一个==可迭代对象==（要可迭代还要是对象），需要包含**键/值对数组**。可迭代对象中的每个键/值对都会按照迭代顺序插入到映射实例中。

使用嵌套数组实现初始化：

```javascript
const m1 = new Map([
    ['firstName', 'poo'],
    ['lastName', 'peng']
]);
alert(m1.size); // 2
// 数组是可迭代对象
```

使用自定义可迭代器初始化对象：

```javascript
const m2 = new Map({
    [Symbol.iterator]: function* () { // 生成器 后面会学到的
        yield ['color', 'orange'];
        yield ['food', 'rice'];
        yield ['game', 'BOTW'];
    }
});
alert(m2.size); // 3
```

```javascript
const m3 = new Map([[]]);
alert(m3.has(undefined)); // true
alert(m3.get(undefined)); // undefined
console.log(m3);
```

![image-20210315092729318](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/15/20210315092736.png)

初始化之后可以使用`set()` 方法再添加键值对

可以使用 `get()` 和 `has()` 方法查询

通过 `size` 属性获取映射中的键值对数量

通过 `delete()` 和 `clear()` 方法删除值

```javascript
const m = new Map();
alert(m.has('firstName')); // false
alert(m.get('firstName')); // undefined
alert(m.size); // 0

m.set('firstName', 'poo')
 .set('lastName', 'peng');
alert(m.has('firstName')); // true
alert(m.get('firstName')); // poo
alert(m.size); // 2

m.delete('firstName'); // 只删除这一个键值对
alert(m.has('firstName')); // false
alert(m.get('firstName')); // undefined
alert(m.size); // 1

m.clear(); // 删除全部键值对

alert(m.has('lastName')); // false
alert(m.get('lastName')); // undefined
alert(m.size); // 0
```

```javascript
const m = new Map().set('firstName', 'poo')
				   .set('lastName', 'peng');
```

`Object`只能使用数值、字符串、符号(Symbol) 作为键，`Map`可以使用任何JavaScript数据类型作为键。

`Map`内部使用`SameValueZero`比较操作（ECMAScript规范内部定义，语言中不能使用）

> |                 | 发生类型转换 | 对待`NaN` `-0` `+0`                            |
> | --------------- | ------------ | ---------------------------------------------- |
> | 抽象相等比较    | 是           | `NaN == NaN // false`  `-0 == +0 // true`      |
> | 严格相等比较    | 否           | `NaN === NaN // false`  `-0 === +0 // true`    |
> | `SameValue`     | 否           | 认为 `NaN` 等于 `NaN`，认为 `+0` 不等于 `-0`   |
> | `SameValueZero` | 否           | 认为 `NaN` 等于 `NaN`，认为 `+0` zzzz等于 `-0` |
>
> `NaN` 与自身不相等，`+0` 等于 `-0`
>
> 特殊处理`NaN` `-0` `+0`：
>
> `SameValue` ：`Object.is()` 方法内部就是用了这个比较操作，
>
> ```javascript
> Object.is(NaN, NaN) // true 认为 NaN 等于 NaN
> Object.is(0, -0) // false 认为 +0 不等于 -0
> ```
>
> `SameValueZero` : `String.prototype.includes()`、`Array.prototype.includes()` 内部用的比较操作
>
> ```javascript
> var array = [NaN, -0]
> array.includes(NaN) // true 认为 NaN 等于 NaN
> array.includes(0) // true  认为 +0 等于 -0
> ```

```javascript
const m = new Map();

const functionKey = function () {};
const symbolKey = Symbol();
const objectKey = {};

m.set(functionKey, 'functionValue')
 .set(symbolKey, 'symbolValue')
 .set(objectKey, 'objectValue');

alert(m.get(functionKey)); // functionValue
alert(m.get(function () {})); // undefined
```

----

在映射中作键和值的对象及其他“集合”类型，在自己的内容或属性被修改时，仍然保持不变。是说，虽然内容或属性变了，但该是那个键还是那个键，该对应那个值还是对应那个值。就比如说，在一起的两个人，都会成长，但咋整都在一起。bond forever 的意思吗？<a id='比如'>跟下面一个一起比比</a>

```javascript
const m = new Map();

const objKey = {},
      objVal = {},
      arrKey = [],
      arrVal = [];

m.set(objKey, objVal)
 .set(arrKey, arrVal);

objKey.foo = 'foo';
objVal.bar = 'bar';

arrKey.push('foo');
arrVal.push('bar');

console.log(m.get(objKey)); // {bar: "bar"}
console.log(m.get(arrKey)); // ["bar"]
```

![image-20210315103848725](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/15/20210315103848.png)

`SameValueZero` 比较操作也可能导致意想不到的冲突：

```javascript
const m = new Map();

const a = 0 / '', // NaN
      b = 0 / '', // NaN
      pz = +0,
      nz = -0;

alert(a === b); // false
alert(pz === nz); // true

m.set(a, 'foo')
 .set(pz, 'bar');

alert(m.get(b)); // foo
alert(m.get(nz)); // bar
```

![image-20210315105647303](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/15/20210315105647.png)

#### 6.4.2 顺序与迭代

----

`Object` 类型本来是不能迭代的，不能使用`for...of`

```javascript
let obj = {
    name: 'peng',
    age: 24
};
for (i in obj) {
    console.log(i);
}
/*
name
age
*/
for (i of obj) {
    console.log(i);
}
// Uncaught TypeError: obj is not iterable
```

> `for-in` 和 `for-of` 的区别：
>
> **`for...of`语句**在[可迭代对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)（包括 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)，[`Map`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)，[`Set`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)，[`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)，[`TypedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)，[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/arguments) 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句。
>
> `for...in`语句以任意顺序迭代对象的[可枚举属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)。
>
> `for...of` 语句遍历可迭代对象定义要迭代的数据。
>
> 以下示例显示了与`Array`一起使用时，`for...of`循环和`for...in`循环之间的区别。
>
> ```javascript
> Object.prototype.objCustom = function() {};
> Array.prototype.arrCustom = function() {};
> 
> let iterable = [3, 5, 7];
> iterable.foo = 'hello';
> 
> for (let i in iterable) {
>   console.log(i); // logs 0, 1, 2, "foo", "arrCustom", "objCustom"
> }
> 
> for (let i in iterable) {
>   if (iterable.hasOwnProperty(i)) {
>     console.log(i); // logs 0, 1, 2, "foo"
>   }
> }
> 
> for (let i of iterable) {
>   console.log(i); // logs 3, 5, 7
> }
> ```
>
> ```javascript
> Object.prototype.objCustom = function() {};
> Array.prototype.arrCustom = function() {};
> 
> let iterable = [3, 5, 7];
> iterable.foo = 'hello'; // 给对象属性
> ```
>
> 每个对象将继承`objCustom`属性，并且作为`Array`的每个对象将继承`arrCustom`属性，因为将这些属性添加到[`Object.prototype`](https://developer.mozilla.org/zh-CN/docs/conflicting/Web/JavaScript/Reference/Global_Objects/Object)和[`Array.prototype`](https://developer.mozilla.org/zh-CN/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Array/prototype)。由于[继承和原型链](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)，对象`iterable`继承属性`objCustom`和`arrCustom`。
>
> ```javascript
> for (let i in iterable) {
>   console.log(i); // logs 0, 1, 2, "foo", "arrCustom", "objCustom"
> }
> ```
>
> 此循环仅以原始插入顺序记录`iterable` 对象的可枚举属性。它不记录数组**元素**`3`, `5`, `7` 或`hello`，因为这些**不是**枚举属性。但是它记录了数组**索引**以及`arrCustom`和`objCustom`。如果你不知道为什么这些属性被迭代，[`array iteration and for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in#array_iteration_and_for...in)中有更多解释。
>
> ```javascript
> for (let i in iterable) {
>   if (iterable.hasOwnProperty(i)) {
>     console.log(i); // logs 0, 1, 2, "foo"
>   }
> }
> ```
>
> 这个循环类似于第一个，但是它使用[`hasOwnProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) 来检查，如果找到的枚举属性是对象自己的（不是继承的）。如果是，该属性被记录。记录的属性是`0`, `1`, `2`和`foo`，因为它们是自身的属性（**不是继承的**）。属性`arrCustom`和`objCustom`不会被记录，因为它们**是继承的**。
>
> ```javascript
> for (let i of iterable) {
>   console.log(i); // logs 3, 5, 7
> }
> ```
>
> 该循环迭代并记录`iterable`作为可迭代对象定义（引用类型）的迭代值，这些是数组元素 `3`, `5`, `7`，而不是任何对象的**属性**。

----

与`Object`类型的一个主要差异是，`Map` 实例会维护键值对的插入顺序，因此可以插入顺序执行迭代操作。

映射实例可以提供一个迭代器，能以插入顺序生成`[key, value]`形式的数组。可以通过`entries()` 方法（或者 `Symbol.iterator` 属性，它引用`entries()`）取得这个迭代器。

```javascript
const m = new Map([
    ['key1', 'val1'],
    ['key2', 'val2'],
    ['key3', 'val3'],
]);

alert(m.entries === m[Symbol.iterator]); // true
alert(m['entries'] === m[Symbol.iterator]); // true 
alert(m[entries] === m[Symbol.iterator]); // 报错
// 对象键（给对象的属性或方法）只能是字符串，如果试图使用非字符串值作为对象的键，那么该值将被强制转换为字符串 
// Map可以使用任何JavaScript数据类型作为键（作为Map对象定义的数据。

for (let pair of m.entries()) {
    alert(pair);
}
for (let pair of m[Symbol.iterator]()) {
    alert(pair);
}
```

因为`entries()` 是默认迭代器，所以可以直接对映射实例使用扩展操作，把映射转换为数组：

```javascript
const m = new Map([
    ['key1', 'val1'],
    ['key2', 'val2'],
    ['key3', 'val3'],
]);

console.log([...m]);
console.log(Array.from(m.entries()));
```

![image-20210315133853401](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/15/20210315133853.png)

如果不使用迭代器，而是使用回调方式，则可以调用映射的`forEach(callback, opt_thisArg`) 方法。传入回调函数，依次迭代每个键值对。可接收第二个参数用于重写回调内部`this`的值：

```javascript
const m = new Map([
    ['key1', 'val1'],
    ['key2', 'val2'],
    ['key3', 'val3']
]);

m.forEach((key, val) => alert(`${ key } -> ${ val }`));
// val1 -> key1
// val2 -> key2
// val3 -> key3

m.forEach((val, key) => alert(`${ key } -> ${ val }`));
// key1 -> val1
// key2 -> val2
// key3 -> val3
```

`keys()` 和 `values()`分别返回以插入顺序生成的键和值的迭代器

```javascript
const m = new Map([
    ['key1', 'val1'],
    ['key2', 'val2'],
    ['key3', 'val3']
]);

for (let key of m.keys()) {
    alert(key);
}

for (let val of m.values()) {
    alert(val);
}
```

键和值在迭代器遍历时是可以修改的，但映射内部的引用是无法修改的。

这并不妨碍修改作为键或值的对象内部的属性（[比如](#比如)），因为这样并不影响它们在映射实例中的身份。

```javascript 
const m1 = new Map([
    ['key1', 'val1']
]);

// 作为键的字符串原始值是不能修改的 字符串是原始类型，如果这里是个对象的话是可以修改对象的属性的
for (let key of m1.keys()) {
    key = 'newKey';
    alert(key); // 'newKey'
    alert(m1.get('newKey')); // 'undefined'
    alert(m1.get('key1')); // 'val1'
}

const keyObj = {id: 1};
const m = new Map([
    [keyObj, 'val1']
]);
// 修改了作为键的对象的属性，但对象在映射内部仍然引用相同的值
for (let key of m.keys()) {
    key.id = 'newKey';
    alert(key); // [Object Object]
    alert(JSON.stringify(key)); // {'id':'newKey'}
    alert(m.get(keyObj)); // 'val1'
    alert(m.get(key)); // 'val1'
}
console.log(keyObj); // {id: "newKey"}
```

#### 6.4.3 选择Object还是Map

对于多数任务来说，只是个人偏好问题。

比较在乎内存和性能的时候，两者之间有显著差别。

**1. 内存占用**

给定固定大小的内存，Map大约可以比Object多存储50%的键值对

**2. 插入性能**

向Object和Map中插入新键值对的消耗大致相当，Map会稍微快一点

**3. 查找速度**

从大型Object 和Map 中查找键值对的性能差异极小，但如果只包含少量的键值对，Object有时候会稍微快一点。再把Object当成数组使用的情况下（比如使用连续整数作为属性），浏览器引擎会进行优化

**4. 删除性能**

Map的`delete()`操作都比插入和查找更快，涉及大量删除操作，使用Map

### 6.5 WeakMap

<a id='WeakMap'>WeakMap</a>

### 6.6 Set

`Set`是一种新增的集合类型，带来集合数据结构，`Set` 在很多方面都像是加强的`Map` 。因为它们大多数的API和行为都是共有的

#### 6.6.1 基本API

```javascript
let s = new Set();
```

在创建的同时初始化，需要传入一个可迭代对象，需要包含插入到新集合实例中的元素：

```javascript
// 使用数组初始化集合
const s1 = new Set(['val1', 'val2', 'val3']);
alert(s1.size); // 3

// 使用自定义迭代器初始化集合
const s2 = new Set({
    [Symbol.iterator]: function* () {
        yield 'val1';
        yield 'val2';
        yield 'val3';
    }
});
alert(s2.size); // 3
```

`add()`增加值，`has()`查询，`delete()`，`clear()`删除元素，`size`属性取得元素数量

```javascript
const s = new Set();

alert(s.has('Matt')); // false
alert(s.size); // 0

s.add('Matt')
 .add('Poo')
alert(s.has('Matt')); // true
alert(s.size); // 2

s.delete('Matt');
alert(s.has('Matt')); // false
alert(s.has('Poo')); // true
alert(s.size); // 1

s.clear();
alert(s.has('Poo')); // false
alert(s.size); // 0
```

与`Map`类似，`Set` 可以包含任何JavaScript数据类型作为值。集合也使用`SameValueZero`操作来检查值的匹配性。

```javascript
const s = new Set();

const functionVal = function() {};
const symbolVal = Symbol();
const objVal = new Object();

s.add(functionVal)
 .add(symbolVal)
 .add(objVal);

alert(s.has(functionVal)); // true
alert(s.has(symbolVal)); // true
alert(s.has(objVal)); // true

alert(s.has(function() {})); // false
```

与严格相等一样，用作值的对象和其他”集合“类型在自己的内容或属性被修改时也不会改变：

```javascript
let obj = {};
let obj1 = obj;
obj.name = 'poo';
console.log(obj === obj1); // true
console,log(obj1); // {name: "poo"}
```

```javascript
const s = new Set();

const objVal = {},
      arrVal = [];

s.add(objVal)
 .add(arrVal);

objVal.bar = 'bar';
arrVal.push('foo');

alert(s.has(objVal)); // true
alert(s.has(arrVal)); // true  // 这些东西存的都是指针吗？
```

`add()` 和 `delete()` 的操作是幂等的

> **幂等函数**，或**幂等**方法，是指可以使用相同参数重复执行，并能获得相同结果的**函数**。 

```javascript
const s = new Set([1, 2, 3]);
s.add(4); // {1, 2, 3, 4}
s.add(4); // {1, 2, 3, 4}
s.add(4); // {1, 2, 3, 4} 集合里的元素不能重复
```

`delete()`返回的是一个布尔值，表示集合中是否存在要删除的值。

```javascript
const s = new Set();
s.add('foo');
alert(s.size); // 1
s.add('foo');
alert(s.size); // 1

alert(s.delete('foo')); // true
alert(s.delete('foo')); // false
```

#### 6.6.2 顺序与迭代

`Set`会维护值插入时的顺序，因此支持按顺序迭代

集合实例提供一个迭代器，能按插入顺序生成集合内容。可以通过`values()` 方法及其别名方法`keys()`（或者`Symbol.iterator`属性，它引用`values()`）取得这个迭代器：

```javascript
const s = new Set(['val1', 'val2', 'val3']);

alert(s.values === s[Symbol.iterator]);
alert(s.keys === s[Symbol.iterator]);

for (let value of s.values()) {
    alert(value);
}

for (let value of s[Symbol.iterator]()) {
    alert(value);
}
```

因为`values()`是默认迭代器，所以可以直接对集合实例使用扩展操作

```javascript
const s = new Set(['val1', 'val2', 'val3']);
consol.log([...s]);
```

> **展开语法(Spread syntax),** 可以在函数调用/数组构造时, 将数组表达式或者string在语法层面展开；还可以在构造字面量对象时, 将对象表达式按key-value的方式展开。
>
> 语法：
>
> 函数调用：
>
> ```javascript
> myFunction(...iterableObj);
> ```
>
> 字面量数组构造或字符串：
>
> ```javascript
> [...iterableObj, '4', ...'hello', 6];
> ```
>
> 构造字面量对象时,进行克隆或者属性拷贝（ECMAScript 2018规范新增特性）：
>
> ```javascript
> let objClone = { ...obj };
> ```
>
> **剩余语法**(**剩余参数**)：**剩余参数**语法允许我们将一个不定数量的参数表示为一个数组。
>
> ```javascript
> function(a, b, ...theArgs) {
>   // ...
> }
> ```
>
> [剩余参数和arguments对象的区别](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Rest_parameters)
>
> ```javascript
> let a = 6;
> let b = 9;
> function simpleTag (string, ...expressions) {
>     console.log(string);
>     for (const expression of expressions) {
>         console.log(expression)
>     }
>     return "foobar";
> }
> 
> let Tagged = simpleTag`${ a } + ${ b } = ${ a + b }`;
> // ["", " + ", " = ", ""] //注意这里的" + " 前后是有空格的 模板字面量里面的空格啊这些字符是会保留的 ${ a }里的空格不会,是因为{}框起来的是javascript表达式
> // 6
> // 9
> // 15
> console.log(Tagged); // "foobar"
> ```

集合`entries()`方法返回一个迭代器，可以按照插入顺序产生包含两个元素的数组，这两个元素是集合中每个值得重复出现：

```javascript
const s = new Set(['val1', 'val2', 'val3']);
for (let pair of s.entries()) {
    console.log(pair);
}
/*
["val1", "val1"]
["val2", "val2"]
["val3", "val3"]
*/
```

如果不使用迭代器，而是使用回调方式，可以调用集合的`forEach()`方法并传入回调，依次迭代每个键值对。可接收第二个用于重写回调this的值的参数。

```javascript
const s = new Set(['val1', 'val2', 'val3']);
s.forEach((val, dupVal) => alert(`${ val }, ${ dupVal }`))
```

修改集合中值的属性不会影响其作为集合值的身份：

```javascript
const s1 = new Set(['val1']);
// 字符串原始值作为值不会被修改
for (let val of s1.values()) {
    val = 'poo';
    alert(val); // 'poo'
    alert(s1.has('poo')); // false
    alert(s1.has('val1')); // true
}

const objVal = {id: 1};
const s2 = new Set([objVal]);
for (let val of s2.values()) {
    val.id = 2;
    alert(JSON.stringify(val)); // {'id':2}
    alert(s2.has(objVal)); // true
}
```

#### 6.6.3 定义正式集合操作

### 6.7 WeakSet











----

一周目先不深究，马住：

- [深/浅拷贝](#深/浅拷贝)
- [全局作用域，全局执行上下文](#作用域)
- [断言函数内部this的值](#断言函数内部this)
- [定型数组](#定型数组)
- [WeakMap](#WeakMap)

