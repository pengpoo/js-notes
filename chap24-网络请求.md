## chap 23 JSON

JSON 是一种数据格式，不是编程语言。

### 23.1 语法

支持表示 3 种类型的值

- [ ] 简单值：字符串、数值、布尔值、null
- [ ] 对象：有序键值对，每个值可以是简单类型，也可以是复杂类型
- [ ] 数组：值可以是任意类型，包括简单值、对象、或其他数组。

#### 23.1.1 简单值

JSON 的字符串必须使用双引号

#### 23.1.2 对象

在 JSON 中必须使用双引号把属性名包围起来

同一个对象中不能出现两个相同的属性。

#### 23.1.3 数组

数组和对象可以组合使用

### 23.2 解析与序列化

JSON 可被直接解析成可用的 JavaScript 对象

#### 23.2.1 JSON 对象

JSON 全局对象有两个方法 `stringify()`（将 JavaScript 序列化为 JSON 字符串） 和 `parse()`（将 JSON 解析为原生 JavaScript 值）

默认情况下，`JSON.stringify()` 会输出不包含空格或缩进的 JSON 字符串，在序列化 JavaScript 对象时，所有函数和原型成员都会有意地在结果中省略，值为 `undefined` 的任何属性也会被跳过，最终得到的就是所有实例属性均为有效 JSON 数据类型的表示。

#### 23.2.2 序列化选项

`JSON.stringify()` 除了要序列化的对象还可以接收两个参数，第一个参数是过滤器，第二个参数是指定缩进

**1. 过滤结果**

如果第二个参数是一个数组，那返回结果只会包含该数组中列出的对象属性。

如果第二个参数是一个函数，提供的函数接收两个参数，属性名和属性值，可以根据这个 Key 决定要对相应的属性执行什么操作，Key 始终是字符串，只是在值不属于某个键值对时会是空字符串。

```javascript
let book = {
    title: 'Professional JavaScript',
    authors: [
        'Nicholas',
        'Matt'
    ],
    edtion: 4,
    year: 2017
};

let jsonText = JSON.stringify(book, (key, value) => {
    switch(key) {
        case 'authors':
            return value.join(',');
        case 'year':
            return 5000;
        case 'edition':
            return undefined;
        default:
            return value;
    }
});

/*
{"title":"Professional JavaScript","authors":"Nicholas,Matt","year":5000}
返回 undefined 忽略该属性
*/
```

**2. 字符串缩进**

第三个参数是字符时，表示每一级缩进的空格数

如果缩进参数是一个字符串，就会使用这个字符串来缩进。

**3. `toJSON()` 方法**

有时候，对象需要在 `JSON.stringify()` 之上自定义 JSON 序列化，此时，可以在要序列化的对象中添加 `toJSON()` 方法。

#### 23.2.3 解析选项

`JSON.parse()` 方法，也可以接受一个额外的函数作为参数



## chap 24 网络请求

### 3.1 Fetch

“AJAX” 异步的 JavaScript 和 XML

有很多方式可以向服务器发送网络请求，并从服务器获取信息。

`fetch()` 方法是一种现代通用的方法，那么我们就从它开始吧。旧版本的浏览器不支持它（可以 polyfill），但是它在现代浏览器中的支持情况很好。

基本语法：

```javascript
let promise = fetch(url, [options])
```

- **`url`** —— 要访问的 URL。
- **`options`** —— 可选参数：method，header 等。

没有 `options`，那就是一个简单的 GET 请求，下载 `url` 的内容。

浏览器立即启动请求，并返回一个该调用代码应该用来获取结果的 `promise`。