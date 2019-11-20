## 事件
### 13.1 事件流
    事件流描述的是从页面中接受事件的顺序
#### 13.1.1 事件冒泡
    IE的事件流叫做事件冒泡，即时间开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点。
#### 13.1.2 事件捕获
    事件捕获的思想是不太具体的点应该更早地接收到事件，而最具体的点应该最后接收到事件。
#### 13.1.3 DOM事件流
    "DOM2级事件流”规定的事件流包括3个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。
### 13.2事件处理程序
    事件处理程序（或事件侦听器）:响应某个事件的函数。事件处理程序以“on"开头。
#### 13.2.1 HTML事件处理程序
```html
<input type="button" value="click Me" onclick="showMessage()">
```
```js
    <script>
        function showMessage(){
            alert("Hello world")
        }
    </script>
```
- event变量可以直接访问事件对象。在这个函数内部，this值等于事件的目标元素。
- 删除事件处理程序
```js
btn.onClick = null;   //删除事件处理程序
```
#### 13.2.3 DOM2级事件处理程序
    "DOM2级事件"定义了两个方法，用于处理指定和删除事件处理程序的操作：addEventListener()和removeEventListener()。接收3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后这个布尔值参数如果是true,表示在捕获阶段调用时间处理程序；如果是faLse,表示在冒泡阶段调用事件处理程序。
  - 在按钮上为click事件添加事件处理程序。
```js
 var btn = document.getElementById("myBtn");
      btn.addEventListener(
        "click",
        function() {
          alert(this.id);
        },
        false
      );
```
```js
 //这里省略了其他代码
      btn.removeEventListener("click",function(){   //没有用!
          alert(this.id);
      },false)
```
  -  addEventListener与removeEventListener中的事件处理程序必须相同。
```js
  var btn = document.getElementById("myBtn");
      var handler = function(){
          alert(this.id)
      }

      //这里省略了其他代码
      btn.addEventListener("click",handler,false)
      btn.removeEventListener("click",handler,false)
```
#### 13.2.4 IE事件处理程序
    IE实现了与DOM中类似的两个方法：attachEvent()和detachEvent()。接收两个参数：事件处理程序名称和事件处理程序函数。attachEvent()添加的事件处理程序都会被添加到冒泡阶段。
  ```js
   var btn = document.getElementById("myBtn");
      btn.attachEvent("onClick",function(){
          alert("Clicked")
      })
  ```
  - 使用attachEvent()添加的事件可以通过detachEvent()来移除，必须提供相同的参数。添加匿名函数将不能被移除。
#### 13.2.5 跨浏览器的事件处理程序
```js
    var EventUtil = {
        addHandler: function(element, type, handler) {
          if (element.addEventListener) {
            element.addEventListener(type, handler, false);
          } else if (element.attachEvent) {
            element.attachEvent("on" + type, handler);
          } else {
            element["on" + type] = handler;
          }
        },
        removeHandler: function(element, type, handler) {
          if (element.removeEventListener) {
            element.removeEventListener(type, handler, false);
          } else if (element.detachEvent) {
            element.detachEvent("on" + type, handler);
          } else {
            element["on" + type] = null;
          }
        }
      };
      var btn = document.getElementById("myBtn");
      var handler = function(){
          alert("Clicked")
      }
      EventUtil.addHandler(btn,"click",handler)
      //这里省略了其他的代码
      EventUtil.removeHandler(btn,"click",handler)
```
### 13.3 事件对象
---
#### 13.3.1 DOM中的事件对象
- this始终等于currentTarget的值，而target则只包含事件的实际目标，如果直接将事件处理程序指定给了目标元素，则this、currentTarget和target包含相同的值。
```js
var btn = document.getElementById("myBtn");
      btn.onclick = function(event) {
        alert(event.currentTarget === this);
        alert(event.target === this);
      };
```
```js
  var btn = document.getElementById("myBtn");
      var handler = function(event) {
        switch (event.type) {
          case "click":
            alert("Clicked");
            break;
          case "mouseover":
            event.target.style.backgroundColor = "red";
            break;
          case "mouseout":
            event.target.style.backgroundColor = "";
            break;
        }
      };
      btn.onclick = handler;
      btn.onmouseover = handler;
      btn.onmouseout = handler;
```
- 要阻止特定时间的默认行为，可以使用preventDefault()方法。
- stopProgration()方法用于立即停止事件在DOM层次中的传播，即取消进一步的事件捕获或冒泡。避免触发注册在document.body上的事件处理程序。

### 13.4 事件类型
---
13.4.1 UI事件
  - load 事件:当页面完全加载后，就会触发window上面的load事件。有两种定义onload事件处理程序的方式、第一种方式：
  ```js
  EventUtil.addHandler(window, "load", function(event) {
        alert("Loaded!");
    });
  ```
第二种方式：
```js
<body onload="alert('Loaded!')">
```
- unload 事件
load对应的是unload事件，这个事件在文档被完全卸载后触发只要从一个页面切换到另一个页面，就会发生unload事件。而利用这个事件最多的情况是清楚引用，以避免内存泄漏。
- resize 事件 当浏览器窗口被调整到一个新的高度或宽度时，就会触发resize事件。
  >浏览器窗口最小化或最大化时也会触发resize事件。
- scroll 事件  与resize事件类似，scroll事件也会在文档被滚动期间重复被触发。
#### 13.4.2 焦点事件
#### 13.4.3 鼠标与滚轮事件
  - click:在用户单击主鼠标按钮。
  - dbclick:在用户双击主鼠标按钮时触发。
  - mousedown:在用户按下任意按钮时触发。
  - mouseenter:在鼠标光标从元素外部首次移动到元素范围之内时触发。
  - mouseleave:在位于元素上方的鼠标光标按钮移动到元素范围之外时触发。这个事件不冒泡。
  - mousemove:当鼠标指针在元素内部移动时重复地触发。
  - mouseout:在一个鼠标指针唯一一个元素上方，然后用户将其移入另一个元素时触发。
  - mouseover:当一个鼠标指针位于一个元素外部，然后用户将其首次移入另一个元素边界之内时触发。
  - mouseup:在用户释放鼠标按钮时触发。
>客户区坐标位置
鼠标都是在浏览器视口中的特定位置上发生的。clientX和clientY

>页面坐标位置
通过客户区坐标能够知道鼠标是在视口中什么位置发生的。pageX和pageY。

>IE8及更早版本不支持事件对象上的页面坐标，不过可以使用客户区坐标和滚动信息可以计算出来。这时候需要用到document.body(混杂模式)或document.documentElement(标准模式中的scrollLeft和scrollTop属性)
```js
document.documentElement.scrollLeft;
```
>屏幕坐标位置  鼠标事件发生时，不仅会有相对于浏览器窗口的位置，还有一个相对于整个电脑屏幕的位置。screenX和screenY

>修改键   鼠标事件主要是使用鼠标来触发的但在按下键盘上的某些建的状态也可以影响到所要采取的操作。Shift、Ctrl、Alt、Meta他们经常被用来修改鼠标事件的行为。

>相关元素 mouseover事件的主目标是获得光标的元素，而相关元素就是那个失去光标的元素。mouseout事件，事件的主目标是失去光标的元素，而相关元素则是获得光标的元素。

>更多事件信息  altLeft、ctrlLeft、offsetX、offsetY、shiftLeft

>鼠标滚轮事件  与mousewheel时间对应的event对象除包含鼠标事件的所有标准信息外还包含一个whellDelta.

>触摸设备  mousemove mouseup click  mousemove事件也会触发mouseover和mouseout事件。两个手指放在屏幕上且页面随手指移动而滚动时触发mousewheel和scroll事件。

#### 13.4.4 键盘与文本事件
- 3个键盘事件：
    - keydown:当用户按下键盘上的任意键时触发，而且如果按住不放的话，会重复触发此事件。
    - keypress:当用户按下键盘上的字符建时触发，而且如果按住不放的话，会重复触发此事件。
    - keyup:当用户释放键盘的键时触发。
- 4.textInput 事件
    ”DOM3级事件“规范中引入了一个新事件”textInput“。当用户在可编辑区域中输入字符时，就会触发这个事件。
    textInput:1.任何可以获得焦点的元素都可以触发keypress事件，但只有编辑区才能出发textInput事件。2.textInput事件只会在用户按下能够输入实际字符的键时才会被触发，而keypress事件则在按下那些能够影响文本显示的键时也会触发（例如退格键）。
- 5.设备中的键盘事件    当用户按下十字键（键码为175~178）
#### 13.4.5 复合事件
  复合事件是DOM3级事件中新添加的一类事件，用于处理IME的输入序列。IME（输入法编辑器）可以让用户在物理键盘上找不到的字符。
#### 13.4.6 变动事件
  略
    1.删除节点：removeChild()或replaceChild()从DOM中删除节点时，首先会触发DOMNodeRemoved事件。
    2.插入节点：appendChild()、replaceChild()或insertBefore()向DOM中插入节点。
#### 13.4.7 H5事件
  - 1.contextmenu contextmenu 事件会在用户尝试打开上下文菜单时被触发。该事件通常在鼠标点击右键或者按下键盘上的菜单键时被触发，如果使用菜单键，该上下文菜单会被展示 到所聚焦元素的左下角，但是如果该元素是一棵DOM树的话，上下文菜单便会展示在当前这一行的左下角。
  >任何没有被禁用的鼠标右击事件 (通过调用事件的 preventDefault() 方法) 将会使得 contextmenu 事件在目标元素上被触发
  ```js
  noContext = document.getElementById('noContextMenu');

noContext.addEventListener('contextmenu', e => {
  e.preventDefault();
});
```
```html
<p id="noContextMenu">这个段落右键菜单已被禁用。</p>
<p>但是这个段落没有被禁用。</p>
```
  - 2.beforeunload 事件  当浏览器窗口关闭或者刷新时，会触发beforeunload事件。当前页面不会直接关闭，可以点击确定按钮关闭或刷新，也可以取消关闭或刷新。
```js
   window.addEventListener("beforeunload", function (event) {
  event.returnValue = "\o/";
});
```
如果处理函数为Event对象的returnValue属性赋值非空字符串，浏览器会弹出一个对话框，来询问用户是否确定要离开当前页面。

- 2.DOMContentLoaded 事件：则在形成完整的DOM树之后就会触发，不理会图像、Javascript文件、css文件或其他资源是否已经下载完毕。
```js
document.addEventListener('DOMContentLoaded', (event) => {
    console.log('DOM fully loaded and parsed'); // 译者注："DOM完全加载以及解析"
});
```
- 4.readystatechange 事件  这个事件的目的是提供与文档或元素的加载状态有关的信息，但这个事件的行为有时候也很难预料。

- 5.pageshow和pagehide 事件
  pageshow:当一条会话历史记录被执行的时候将会触发页面显示(pageshow)事件。(这包括了后退/前进按钮操作，同时也会在onload 事件触发后初始化页面时触发)
  pagehide:改时间会在浏览器卸载页面的时候触发，而且是在unload事件之前触发。

- 6.haschange 事件：之所以有这个参数，是因为在ajax应用中，开发人员要经常利用URL参数数列来保存状态或导航信息。

#### 13.4.8 设备事件
  - 1.orientationchange 事件。
  - 2.MozOrientation 事件。
  - 3.deviceorientation 事件。
  - 4.devicemotion 事件
#### 13.4.9 触摸与手势事件
### 13.5 内存和性能
---
#### 13.5.1 事件委托
  >“对事件处理程序过多”问题的解决方案就是事件委托。事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。
#### 13.5.2 移除事件处理程序


