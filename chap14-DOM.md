[TOC]

## Chap14 DOM

### 14.1 节点层级

DOM 中共有 12 种节点类型，每种类型又都继承自一种基本类型

#### 14.1 Node 类型

在 JavaScript 中，所有节点类型都继承 `Node` 类型，所有类型都共享基本的属性和方法。

每个节点都有 `nodeType` 属性，表示该节点的类型。节点类型由定义在 `Node` 类型上的12个数值常量表示。

最常用到的是元素节点（1）和文本节点（3）。

**1. `nodeName` 与 `nodeValue` 属性**

保存着节点的信息，`someNode.nodeName` 

对于元素节点而言，`nodeName` 就是它的标签名，`nodeValue` 为 `null`，文本节点的 `nodeValue` 就是它的文本内容。

**2. 节点关系**

文档中所有节点都与其它节点有关系。

每个几点都有一个 `childNodes` 属性，其中包含一个 `NodeList` 实例（是一个类数组对象）。

```javascript
let firstChild = someNode.childNodes[0];
let secondChild = someNode.childNodes.item(2);
let count = someNode.childNodes.length;
```

`NodeList` 是实时的活动对象，（看页面加载情况）

----

每个节点都有一个 `parentNode` 属性，

使用 `previousSibling` 和 `nextSibling` 可以在 `childNodes` 列表的节点之间导航

`firstChild` 和 `lastChild` 分别指向 `childNodes` 中第一个和最后一个子节点

`hasChildNodes()` 方法，如果返回 `true` 说明节点有一个或多个子节点。

所有节点都共享的关系，`ownerDocument` 属性是一个指向代表整个文档的文档节点的指针。

**3. 操纵节点**

上面的那些所有的关系指针都是只读的。

DOM 提供了一些专门操纵节点的方法

`appendChild()` 在 `childNodes` 列表末尾添加节点。添加节点会更新相关的关系指针

----

`insertBefore()`，

```javascript
someNode.insertBefore(newNode, oldNodes);
someNode.insertBefore(newNode, null); // 等价于 someNode.appenChild(newNode);
```

`replaceChild()`

```javascript
someNode.replaceChild(newNode, oldNode);
```

----

`removeChild()`

**4. 其他方法**

`cloneNode()`，返回和调用它的节点一模一样的节点。还可以接收一个布尔值参数，用于表示是否深复制。在传入 `true` 时会深复制，即复制节点及其整个 DOM 树。浅复制只会复制调用该方法的节点。不管深复制还是浅复制，返回的节点属于文档所有，但尚未指定父节点，所以可以称为孤儿节点。Chrome不支持吧？

----

`normalize()`。唯一的任务就是处理文档子树中的文本节点。在节点上调用 `normalize()` 方法会检测这个节点的所有后代，从中搜索，并不包含文本的文本节点或文本节点之间互为兄弟关系，如果发现空文本节点，则将其删除，如果两个节点是兄弟关系，就将它们合并为一个文本节点。

#### 14.1.2 Document 类型

在浏览器中，文档对象 `document` 是 `HTMLDocument` 的实例（`HTMLDocument` 继承 `Document` ）。

文档对象 `document` 表示整个 `HTML` 页面。是 `window` 对象的属性。是一个全局对象

**1. 文档子节点**

DOM 规范规定 `Document` 节点的子节点可以是 `DocumentType`（最多一个） 、`Element`（最多一个）、`ProcessingInstruction` 或 `Comment` 

`documentElement` 属性，始终指向 HTML 页面中的 `<html>` 元素

`body` 属性，指向 `<body>` 元素

`document.doctype` 取得对 `<!doctype>` 的引用。

文档类型是只读的，且只有一个 `Element` 类型的子节点

**2. 文档信息**

`document` 作为 `HTMLDocument` 的实例，有一些标准 `Document` 对象上所没有的属性，

`title` 属性，可读可写

`URL` 属性，包含当前页面的完整 URL

`domain` 属性，包含当前页面的域名

`referrer` 属性，包含链接到当前页面的那个页面的 URL，如果当前页面没有来源，是空字符串

在这些属性中，只有 `domain` 是可以设置的，如果 URL 包含子域名，如 `p2p.wrox.com` 则可以将 `domain` 设置为 `wrox.com`。不能设置 URL 中不包含的值

**3. 定位元素**

`getElementById()` 和 `getElementsByTagName()`

如果页面中有多个相同 ID 的元素，`getElementById()` 只会返回第一个

`getElementsByTagName()` 返回包含零个或多个包含元素的 `NodeList` 。在 HTML 文档里，这个方法返回一个 `HTMLCollection` 对象。都是实时列表

`HTMLCollenction` 对象还有一个额外的方法 `namedItem()` ，可通过标签的 `name` 属性取得某一项的引用

取得文档中所有元素，可以 `document.getElementsByTagName('*')`

----

`getElementsByName()`，返回具有 `name` 属性的所有元素。最常用于单选按钮。

**4. 特殊集合**

访问文档中公共部分的快捷方式

`document.anchors`，包含文档中所有带 `name` 属性的 `<a>` 元素

`document.forms`，包含文档中所有的 `<form>` 元素

`document.images`，包含文档中所有的 `<img>` 元素

`document.links`，包含文档中所有带 `href` 属性的 `<a>` 元素

**5. DOM 兼容性检测**

**6. 文档写入**

`write()`、`writeln()`、`open()`、`close()`

`write()`、`writeln()`：接收一个字符串参数，`writeln()` 会在末尾追加一个换行符。常用于动态包含外部资源。

`open()`、`close()`：用于打开和关闭网页输出流

#### 14.1.3 Element 类型

`Element` 表示 XML 或 HTML 元素，对外暴露出访问元素标签名，子节点和属性的能力

可通过 `nodeName` 或 `tagName` 属性来获取元素的标签名，返回同样的值

`div.tagName` 回返回 `'DIV'` ，大写的，HTML 中，元素标签名始终以全大写表示

**1. HTML 元素**

所有 HTML 元素都通过 `HTMLElement` 类型表示。`HTMLElement` 直接继承自 `Element`

所有 HTML 元素上都有的标准属性

`id`

`title`

`lang`

`dir` 书写方向，`ltr` `rtl`

`className`

所有属性都可以这样读 `someEle.id`....

可以这样修改 `someEle.id = ''` ...

**2. 取得属性**

与属性相关的操作 `getAttribute()`、`setAttribute()` 、`removeAttribute()`

`someEle.calssName`   `someEle.getAttribute('class')`   

`getAttribute()` 可以取得自定义属性，自定义属性不会成为 DOM 对象的属性

`someEle.style` 和 `someEle.getAttribute('style')`  返回值不一样

事件处理程序，比如 `someEle.onclick` （返回的是函数）和 `someEle.getAttribute('onclick')` （返回的是字符串形式的源代码），返回值不一样

通常会放弃使用 `getAttribute()` ，只使用对象属性. `getAttribute()` 主要用于获取自定义属性

**3. 设置属性**

`div.setAttribute('id', 'someId')`

在 DOM 对象上添加自定义属性，不会自定让它变成元素属性

```javascript
div.mycolor = 'red';
alert(div.getAttribute('mycolor')); // null
```

**4. attribute 属性**

`Element` 类型是唯一使用 `attributes` 属性的 DOM 节点类型，这个属性包含一个 `NamedNodeMap()` 实例，是一个类似 `NodeList` 的 “实时” 集合。元素的每个属性都表示为一个 `Attr` 节点

`getNamedItem(name)` 返回 `nodeName` 属性等于 `name` 的节点

`removeNamedItem(name)` 返回被删除属性的 `Attr`节点

`setNamedItem(node)` 接收一个属性节点，给元素添加一个属性，不常用

`item(pos)` ，返回索引位置 pos 处的节点

`attribute` 属性，最有用的场景是需要迭代元素上所有的属性时。

**5. 创建元素**

`document.createElement('div')`

**6. 元素后代**

元素可以拥有任意多个子元素和后代元素，子节点可以是其他元素，文本节点，注释或处理指令。

通常在执行某个操作需要先检测一下节点的 `nodeType` 。

#### 14.1.4 Text 类型

`Text` 节点中包含的文本可以通过 `nodeValue` 属性访问，也可以通过 `data` 属性访问。

`appendData(text)`

`deleteData(offset, count)` 从位置 `offset` 开始删除 `count` 个字符

`insertData(offset, text)` 在 `offset` 位置插入文本 `text`

`replaceData(offset, count, text)`

`splitText(offset)`

`substringData(offset, count)`.

可以通过 `length` 属性获取文本节点中包含的字符数量，等价于 `nodeValue.length()` 和 `data.length`

**1. 创建文本节点**

`document.createTextNode()` 

将一个文本节点作为另一个文本节点的同胞插入后，两个文本节点之间不会包含空格

**2. 规范化文本节点**

```javascript
let element = document.createElement('div');
element.className = 'message';

let textNode = document.createTextNode('hello world!');
element.appendChild(textNode);

let anotherTextNode = document.createTextNode('Yippee!');
element.appendChild(anotherTextNode);

document.body.append(element);

alert(element.childNodes.length); // 2

element.normalize();
alert(element.childNodes.length); // 1
alert(element.firstChild.nodeValue); 'hello world!Yippee!'
```

浏览器在解析文档时，永远不会创建同胞文本节点。同胞文本节点只会出现在 DOM 脚本生成的文档树中

**3. 拆分文本节点**

```javascript
let element = document.createElement('div');
element.className = 'message';

let textNode = document.createTextNode('hello world!');
element.appendChild(textNode);

document.body.append(element);

let newNode = element.firstChild.splitText(5);
alert(element.firstChild.nodeValue); // 'hello'
alert(element.lastChild.nodeValue); // ' world!'
alert(newnode.nodeValue); // ' world'
alert(element.childNodes.length); // 2
```

拆分节点最常用于从文本节点中提取数据的 DOM 解析技术。

#### 14.1.9 Attr 类型

元素数据在 DOM 中通过 Attr 类型表示。从技术上讲，属性是存在于元素 `attribute` 属性中的节点。

**属性节点尽管是节点，却不认为是 DOM 文档树的一部分**。

### 14.2 DOM 编程

#### 14.2.1 动态脚本

动态加载外部脚本：

```javascript
let script = document.createElement('script');
script.src = 'foo.js';
document.body.appendChild(script);
```

动态嵌入 JavaScript 源码：

```javascript
var script = document.createElement('script');
var code = 'function sayHi () {alert(\'Hi\');}';
try {
    script.appendChild(documen.createTextNode(code));
} catch (ex) {
    script.text = code;
}

document.body.appendChild(script);
```

通过 `innerHTML` 创建的 `<script>` 元素永远不会执行。

#### 14.2.2 动态样式

```javascript
let link = document.createElement('link');
link.rel = 'stylesheet';
link.type = 'text/css';
link.href = 'stylr.css';
let head = document.getElementsByTagName('head')[0];
head.appendChild(link);
```

```javascript
let style = document.createElement('style');
style.type = 'text/css';
try {
    style.appendChild(document.createTextNode('body{background-color:bule}'));
} catch (ex) {
    style.styleSheet.cssText = 'body{background-color:bule}';
}
let head = document.getElemensByTagName('head')[0];
head.appendChild(style)
```

#### 14.2.3 操作表格

```html
<table>
    <tbody>
    	<tr>
        </tr>
    </tbody>
</table>
```

HTMLDOM 给 `<table>` `<tbody>`  `<tr>` ，增加了一些属性和方法，以便于创建表格 P430

```javascript 
let table = document.createElement('table');
table.border = 1;
table.width = '100%'

let tbody = document.createElement('tbody');
table.appendChild(tbody);

// 创建第一行
tbody.insertRow(0);
tbody.rows[0].insertCell(0);
tbody.rows[0].cells[0].appendChild(document.createTextNode('cell 1_1'));
tbody.rows[0].insertCell(1);
tbody.rows[0].cells[1].appendChild(document.createTextNode('cell 1_2'));

// 创建第二行
tbody.insertRow(1);
tbody.rows[1].insertCell(0);
tbody.rows[1].cells[0].appendChild(document.createTextNode('cell 2_1'));
tbody.rows[1].insertCell(1);
tbody.rows[1].cells[1].appendChild(document.createTextNode('cell 2_2'));

document.body.appendChild(table);
```



## Chap15 DOM 扩展

### 15.1 Selectors API

核心两个方法 `querySelector()` 和 `querySelectorAll()`

在兼容 Selector API 的浏览器中，Document 类型和 Element 类型的实例上都会暴露这两个方法。

#### 15.1.1 `querySelector()` 

接收一个 CSS 选择符参数，返回匹配该模式的第一个后代元素，如果没有匹配项返回 `null`。

```javascript
let body = document.querySelector('body');
let myDiv = document.querySelector('#myDiv');
let selected = document.querySelector('.selected');
let img = document.body.querySelector('img.button'); // 取得类名为 button 的 img 节点
```

#### 15.1.2 `querySelectorAll()`

接收一个 CSS 选择符参数，返回匹配的所有节点，返回的是一个 NodeList 的静态实例。如果没有匹配项，返回一个空的 NodeList 实例。

```javascript
let ems = document.getElementById('myDiv').querySelectorAll('em');

let selecteds = document.querySelectorAll('.selected');

let strongs = document.querySelectorAll('p strong');
```

#### 15.1.3 `matches()`

接收一个 CSS 选择符参数，如果元素匹配返回 `true` 否则返回 `false` 

```javascript
if (document.body.matches('body.page1')) {
    // do something
}
```

### 15.2 元素遍历

Element Traversal API 为 DOM 元素添加了五个属性：

`childElementCount`

`firstElementChild`

`lastElementChild`

`previousElementSibling`

`nextElementSibling`

```javascript
let parenElement = document.getElementById('parent');
let currentChildNode = parentElement.firstChild;

// 没有子元素 跳过循环
while (currentChildNode) {
    if (currentChildNode.nodeType === 1) {
        processChild(currentChild);
    }
    if (currentChildNode === parentElement.lastChild) { // 不要 break 循环也结束
        break;
    }
    currentChildNode = currentChildNode.nextSibling;
}

// 用新添的属性
let parentElement = document.getElementById('parent');
let currentChildNode = parentElement.firstElementChild;

while (currentChildNode) {
    processChild(currentChildNode);
    if (currentChildNode === parentElement.lastElementChild) {
        break;
    }
    currentChildNode = currentChildNode.nextElementSibling;
}
```

### 15.3 HTML5

#### 15.3.1 CSS类扩展

**1. getElementByClassName()**

暴露在 document 对象和所有 HTML 元素上。

接收一个参数，即包含一个或多个类名的字符串，返回类名中包含相应的元素的 NodeList，如果提供了多个类名，则顺序无关紧要。

```javascript
// 取得所有类名中包含 username 和 current 的元素
let allCurrentUsernames = document.getElementByClassName('username current');

let selected = document.getElementById('myDiv').getElementByClassName('selected');
```

如果要给包含特定类的元素添加事件处理函数，使用这个方法会很方便。

**2. classList 属性**

HTML5 通过给元素增加 classList 属性，提供了对类名进行增删改操作的实现方式，classList 是一个新的集合类型 DOMTokenList 的实例。有 length 属性，可以通过中括号或 `item()` 取得个别元素，还有以下方法：

`add(value)`

`contains(value)`：返回 true 或 false

`remove(value)`：

`toggle(value)`：如果类名列表中存在指定的 value 则删除；如果不存在，则添加。

#### 15.3.2 焦点管理

HTML5 增加了辅助 DOM 焦点管理的功能。`document.activateElement` 始终包含当前拥有焦点的 DOM 元素。`document.hasFocus()` ，返回布尔值，表示文档中是否拥有焦点。

#### 15.3.3 HTMLDocument 扩展

**1. readyState 属性**

有两个可能的值 `loading` 表示文档正在加载，和 `complete` 表示文档加载完毕。

在这个属性得到广泛支持以前，通常要依赖 `onload` 事件处理程序设置一个标记，基本用法如下：

```javascript
if (document.readyState === 'complete') {
    // do something
}
```

**2. compatMode 属性**

指示浏览器当前出于什么渲染模式

**3. head 属性**

`document.head` 属性，指向文档的 `<head>` 元素。

#### 15.3.4 字符集属性

`characterSet` 属性表示文档实际使用的字符集

#### 15.3.5 自定义数据属性

HTML5 允许给元素指定非标准的属性，但要使用前缀 `data-` ，表示这些属性不包含语义信息也不包含与渲染有关的信息

```html
<div id="myDiv" data-appId="123" data-myname="Nicholas">
    
</div>
```

定义了自定义数据属性后，可以通过 `dataset` 属性访问。

```javascript
let div = document.getElementById('myDiv');
let appId = div.dataset.appId;
let myname = div.dataset.myname;

div.dataset.appId = 234;
div.dataset.myname = 'Michael';

if (div.dataset.myname) {
    alset(`hello, ${ div.dataset.myname }`);
}
```

自定义属性非常适合给元素附加某些数据的场景。

#### 15.3.6 插入标记

**1. innerHTML 属性**

**2. 旧 IE 中的 innerHTML**

**3. outerHTML 属性**

```html
<div id="content">
    <p>
        a paragraph
    </p>
    <ul>
        <li>item 1</li>
        <li>item 2</li>
        <li>item 3</li>
    </ul>
</div>
```

```javascript
div.outerHTML = "<p>something</p>";
// 相当于
let p = document.createElement('p');
p.appendChild(document.createTextNode("something"));
div.parentNode.replaceChildNode(p, div);
// 新的 <p> 元素会取代 DOM 树中原来的 <div> 元素
```

**4. insertAdjacentHTML() 和 insertAdjacentText()**

**5. 内存与性能问题**

**6. 跨站点脚本**

#### 15.3.7 `scrollIntoView()`

存在于所有 HTML 元素上，可以滚动浏览器窗口或容器元素以便包含元素进入视口。参数如下

- [ ] `alignToTop` 是一个布尔值。
  - true ： 窗口滚动后元素顶部与视口顶部对齐
  - false ： 窗口滚动后元素的底部与视口底部对齐

- [ ] `scrollIntoViewOptions` 是一个选项对象
  - behavior ： 定义过渡动画，可取的值有 smooth，auto，默认 auto
  - block ： 定义垂直方向的对齐，可取的值为 start，center，end，nearest，默认 start
  - inline ： 定义水平方向的对齐，可取的值为 start，center，end，nearest，默认 nearest

```javascript
// 确保元素可见
document.forms[0].scrollIntoView();

// 同上
document.forms[0].scrollIntoView(ture);
document.forms[0].scrollIntoView({block: 'start'});

// 尝试将元素平滑的滚入视口
document.forms[0].scrollIntoView({behavior: 'smooth', block: 'start'});
```

