## chap17 事件

JavaScript 与 HTML 交互是通过事件实现的，事件代表浏览器窗口中某个有意义的时刻。可以使用仅在事件发生时执行的处理程序订阅事件。

### 17.1 事件流

事件冒泡

事件捕获

DOM 事件流

### 17.2 事件处理程序

事件意味着用户或浏览器执行的某种动作，比如单击、加载、鼠标悬停

为响应事件而调用的函数被称为事件处理函数。

#### 17.2.1 HTML 事件处理函数

特定元素支持的每个事件都可以使用事件处理程序的名字以 HTML 属性的形式来制定。属性的值必须是可执行的 JavaScript 代码。

```html
<input type="button" value="Click Me" onclick="console.log('clicked')">

// 调用页面其他地方定义的函数
<script>
	function showMsg () {
        console.log('clicked');
    }
</script>
<input type="button" value="Click Me" onclick="showMsg()"> // 可执行的代码 写成 showMsg 没用

// 也可以调用外部脚本中定义的函数
```

作为事件处理程序执行的代码可以访问全局作用域中的一切。

以这种方式指定的事件处理程序，首先会创建一个函数来封装属性的值，这个函数有一个特殊的局部变量 `event`，其中保存的是 `event` 对象。

```html
<input type="button" value="Click Me" onclick="console.log(event.type)">  输出 'click'

在这个函数中 this 的值相当于事件的目标元素
<input type="button" value="Click Me" onclick="console.log(this.value)"> 输出 'Click Me'
```

这个动态创建的包装函数的作用域链被扩展了，在这个函数中 document 和元素自身的成员都可以被当做局部变量来访问，这是通过 with 来实现的：

```html
function () {
    with (document) {
        with (this) {
            // 属性值
        }
    }
}
事件处理程序可以更方便的访问自己的属性
<input type="button" value="Click Me" onclick="console.log(value)"> 输出 'Click Me'
```

如果这个元素是一个表单输入框，则作用域中还会包含表单元素：

```html
function () {
    with (document) {
        with (this.form) {
            with (this) {
				// 属性值
			}
        }
    }
}

<form>
    <input type="text" name="username" value="">
    <input type="button" value="Echo Username" onclick="console.log(username.value)">
</form>
直接引用了 username
```

问题一：如果 `showMsg()` 函数是在按钮后面定义的，那么用户在 `showMsg()` 函数定义之前点击按钮就会发生错误，为此大多数 HTML 事件处理程序都会封装在 `try/catch` 块中

```html
<input type="button" value="Click Me" onclick="try{showMsg();}catch(ex) {}">
```

问题二：对事件处理程序作用域链的扩展在不同的浏览器中可能有不同的结果

问题三：HTML 与 JavaScript 的强耦合

#### 17.2.2 DOM0 事件处理程序

每个元素（包括 document 和 window）都有通常小写的事件处理程序属性，比如 `onclick` ，只要把这个属性赋值为一个函数即可

```javascript
let btn = document.getElementById('mybtn');
btn.onclick = function () {
    console.log('Clicked');
};
// 代码在运行之后才会给事件处理程序赋值，如果在页面中上面这些代码出现在按钮之后，可能出现用户点击没反应的情况
```

像这样使用 DOM0 的方式为事件处理程序赋值时，所赋函数被视为元素的方法，事件处理函数会在元素的作用域中运行，this 等于元素：

```javascript
let btn = document.getElementById('mybtn');
btn.onclick = function () {
    console.log(this.id);
};
// 不能 console.log(id); 没有 with(this) {}
```

以这种方式添加事件处理程序是注册在事件流的冒泡阶段的

可以移除通过 DOM0 方式添加的事件处理程序 `btn.onclick = null`

#### 17.2.3 DOM2 事件处理程序

`addEventListener()` 和 `removeEventListener()`

这两个方法暴露在所有 DOM 节点上，接收三个参数，事件名，事件处理函数和一个布尔值，true 表示在捕获阶段调用事件处理程序，false（默认值）表示在冒泡阶段调用事件处理程序。

```javascript
let btn = document.getElementById('mybtn');
btn.addEventListener('click', () => console.log('this.id'), false);
```

使用 DOM2 方式的主要优势是可以同时为同一个事件添加多个事件处理程序

```javascript
let btn = document.getElementById('mybtn');
btn.addEventListener('click', () => {console.log(this.id)}, false);
btn.addEventListener('click', () => {console.log('hello world')}, false);
// 多个事件处理程序以添加顺序来触发
```

通过 `addEventListener()` 添加的事件处理程序只能通过 `removeEventListener()` 来移除

```javascript
let btn = document.getElementById('mybtn');
btn.addEventListener('click', () => {console.log(this.id)}, false);
// 添加的匿名函数不能移除 因为没有对匿名函数的引用
```

大多数情况下事件处理程序会被添加到事件流的冒泡阶段，主要是因为跨浏览器兼容性好，把事件处理程序注册到捕获阶段通常用于在事件到达其指定目标之前拦截事件。

#### 17.2.4 IE事件处理程序

`attachEvent()` 和 `detachEvent()`

接收两个参数，事件处理程序的名字（不是事件名了），和事件处理函数。`attachEvent()`  添加的事件处理程序会添加到冒泡阶段

```javascript
let btn = document.getElementById('mybtn');
btn.attachEvent('onclick', () => {console.log('clicked')});
```

在 IE 中使用 `attachEvent()` 与使用 DOM0 方式的主要区别是事件处理程序的作用域。DOM0 方式，事件处理程序中的 this 值等于目标元素。使用 `attachEvent()` 时，事件处理程序是在全局作用域中运行的，因此 this 值等于 window。

多个事件处理程序以添加顺序反向触发

添加的匿名函数不能移除

#### 17.2.5 跨浏览器事件处理程序

创建一个 `addHandler()` 方法，根据需要分别使用 DOM0 方式、DOM2 方式或 IE 方式来添加事件处理程序，接收三个参数：目标元素、事件名和事件处理函数。

```javascript
var EventUtil = {
    addHandler: function (element, type, handler) {
        if (element.addEventListener) {
            element.addEventListener(type, handler, false);
        } else if (element.attachEvent) {
            element.attachEvent('on' + type, handler);
        } else {
            element['on' + type] = handler;
        }
    },
    
    removeHandler: function (element, type, handler) {
        if (element.removeEventListener) {
            element.removeEventListener(type, handler, false);
        } else if (element.deatchEvent) {
            element.deatchEvent('on' + type, handler);
        } else {
            element['on' + type] = handler;
        }
    }
}
```

这里的 `addHandler()` 和 `removeHandler()` 并没有解决所有跨浏览器一致性问题，比如 IE 的作用域问题、多个事件处理程序执行顺序问题。

### 17.3 事件对象

在 DOM 中发生事件时，所有相关信息都会被收集并存储在一个名为 `event` 的对象中。

#### 17.3.1 DOM 事件对象

在 DOM 合规的浏览器中 `event` 对象是传给事件处理程序的唯一参数，不管以哪种方式（DOM0 DOM2）都会传入这个 `event` 对象，在通过 HTML 属性指定的事件处理程序中，同样可以使用变量 `event` 引用事件对象。

```javascript
let btn = document.getElementById('mybtn');
btn.onclick = function (event) {
    console.log(event.type); // 'click'
};

btn.addEventListener('click', function(event) {
    console.log(event.type); // click
}, false);

<input type="button" value="Click Me" onclick="console.log(event.type)"> // 'click'
```

| 属性/方法                  | 类型   | 读/写 | 说明                                                         |
| -------------------------- | ------ | ----- | ------------------------------------------------------------ |
| bubbles                    | 布尔值 | 只读  | 表示事件是否冒泡                                             |
| cancelable                 | 布尔值 | 只读  | 表示是否可以取消事件的默认行为                               |
| currentTarget              | 元素   | 只读  | 当前事件处理程序所在的元素                                   |
| defaultPrevent             | 布尔值 | 只读  | true 表示已经调用 preventDefault() 方法                      |
| detail                     | 整数   | 只读  | 事件相关的其他信息                                           |
| eventPhase                 | 整数   | 只读  | 表示调用事件处理程序的阶段 1捕获阶段 2到达目标 3冒泡         |
| preventDefault()           | 函数   | 只读  | 用于取消事件的默认行为                                       |
| stopImmediatePropagation() | 函数   | 只读  | 用于取消所有后续事件捕获或事件冒泡                           |
| stopPropagation()          | 函数   | 只读  | 取消所用后续事件捕获或冒泡，只有bubbles 是 true 才可调用     |
| target                     | 元素   | 只读  | 事件目标                                                     |
| trusted                    | 布尔值 | 只读  | true 表示事件是由浏览器生成的 false 表示事件是开发者通过 JavaScript 创建的 |
| type                       | 字符串 | 只读  | 被触发的事件类型                                             |
| View                       |        |       |                                                              |

在事件处理程序内部，this 对象始终等于 currentTarget 的值，而 target 只包含事件的实际目标

```javascript
let btn = document.getElementById('mybtn');
btn.onclick = function (event) {
    console.log(event.currentTarget === this); // true
    console.log(event.target === this); // true
}

document.onclick = function (event) {
    console.log(event.currentTarget === document.body); // true
    console.log(this === document.body); // true
    console.log(event.target === document.getElementById('mybtn')); // true
}
// （下半部分）由于按钮本身没有注册事件处理程序，因此click事件冒泡到document.body，从而触发了在它上面注册的处理程序
```

```javascript
<a href="https://gitee.com/pengpoo" id="mylink">pengpoo 的 gitee 主页</a>

let link = document.getElementById('mylink');
link.onclick = function () {
  window.event.preventDefault();
};
// 取消默认跳转的行为
```

```javascript
btn.onclick = function (event) {
  console.log('clicked');
  event.stopPropagation();
};

document.body.onclick = function () {
  console.log('body clicked');
};
// 只会输出 'clicked', 没有 event.stopPropagation() 会输出 'click' 和 'body click'
```

`eventPhase` 虽然到达目标是在冒泡阶段发生的，但其`eventPhase` 仍然等于2.

```javascript
let btn = document.getElementById('mybtn');
btn.onclick = function (event) {
    console.log(event.eventPhase); // 2 点击的是按钮
};

document.body.addEventListener('click', (event) => console.log(event.eventPhase), true); // 1

document.body.onclick = function (event) {
    console.log(event.eventPhase); // 3
};
```

#### 17.3.2 IE 事件对象

与访问DOM中的event 对象不同，要访问IE 中的event 对象有几种不同的方式，取决于指定事件处理程序的方法。在使用DOM0 级方法添加事件处理程序时，event 对象作为window 对象的一个属性存在。

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function () {
    var event = window.event;
    alert(event.type); //"click"
};
```

通过window.event 取得了event 对象，并检测了被触发事件的类型（IE 中的type属性与DOM中的type 属性是相同的）。可是，如果事件处理程序是使用attachEvent()添加的，那么就会有一个event 对象作为参数被传入事件处理程序函数中。

```javascript
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function (event) {
    alert(event.type); //"click"
});
```

在像这样使用attachEvent()的情况下，也可以通过window 对象来访问event 对象，就像使用DOM0 级方法时一样。不过为方便起见，也会将其作为参数传递。

如果是通过HTML特性指定的事件处理程序，那么还可以通过一个名叫event 的变量来访问event对象（与DOM 中的事件模型相同）。

```html
<input type="button" value="Click Me" onclick="alert(event.type)">
```

IE 的event 对象同样也包含与创建它的事件相关的属性和方法。其中很多属性和方法都有对应的或者相关的DOM属性和方法。与DOM的event 对象一样，这些属性和方法也会因为事件类型的不同而不同，但所有事件对象都会包含下表所列的属性和方法。

![20180812120807867](https://gitee.com/pengpoo/pictures/raw/master/notes_imgs/2021/03/27/20210327180738.png)

因为事件处理程序的作用域是根据指定它的方式来确定的，所以不能认为this 会始终等于事件目标。故而，最好还是使用event.srcElement 比较保险。例如：

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function () {
    alert(window.event.srcElement === this); //true
};
btn.attachEvent("onclick", function (event) {
    alert(event.srcElement === this); //false
});
```

在第一个事件处理程序中（使用DOM0 级方法指定的），srcElement 属性等于this，但在第二个事件处理程序中，这两者的值不相同。

如前所述，returnValue 属性相当于DOM 中的preventDefault()方法，它们的作用都是取消给定事件的默认行为。只要将returnValue 设置为false，就可以阻止默认行为。来看下面的例子。

```javascript
var link = document.getElementById("myLink");
link.onclick = function () {
    window.event.returnValue = false;
};
```

这个例子在onclick 事件处理程序中使用returnValue 达到了阻止链接默认行为的目的。与DOM不同的是，在此没有办法确定事件是否能被取消。

cancelBubble 属性与DOM 中的stopPropagation()方法作用相同，都是用来停止事件冒泡的。由于IE 不支持事件捕获，因而只能取消事件冒泡；但stopPropagatioin()可以同时取消事件捕获和冒泡。

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function () {
    alert("Clicked");
    window.event.cancelBubble = true;
};
document.body.onclick = function () {
    alert("Body clicked");
};
```

#### 17.3.3 跨浏览器事件对象

可以对前面介绍的EventUtil 对象加以增强，

```javascript

var EventUtil = {
    addHandler: function (element, type, handler) {
        //省略的代码
    },
    getEvent: function (event) {
        return event ? event : window.event;
    },
    getTarget: function (event) {
        return event.target || event.srcElement;
    },
    preventDefault: function (event) {
        if (event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },
    removeHandler: function (element, type, handler) {
        //省略的代码
    },
    stopPropagation: function (event) {
        if (event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
};
```

为EventUtil 添加了4 个新方法。第一个是getEvent()，它返回对event对象的引用。考虑到IE 中事件对象的位置不同，可以使用这个方法来取得event 对象，而不必担心指定事件处理程序的方式。在使用这个方法时，必须假设有一个事件对象传入到事件处理程序中，而且要把该变量传给这个方法，如下所示。

```javascript
btn.onclick = function (event) {
    event = EventUtil.getEvent(event);
};
```

