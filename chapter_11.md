### 11.3.6  插入标记
----
##### 1.innerHtml 属性
    语法： `HTMLElementObject.innerHTML=text`;
    属性设置或返回表格行的的开始或结束标签之间的HTML。
- 使用 innerHTML 时若与 createElement 同级并且 innerHTML 在后会出现事件无法绑定，原因是 innerHTML 后父级 DOM 重新加载。
```javascript
/*有问题的情况*/
function c(){
  var a = document.createElement("div");
  var a1 = document.createElement("div");
  a1.innerText = "点我不会执行onclick";
  a1.onclick = function(){alert(1)};
  a.appendChild(a1);
  a.innerHTML += "<b></b>";
  document.getElementsByTagName("body")[0].appendChild(a);
}

/*没问题的情况*/
function c(){
  var a = document.createElement("div");
  a.innerHTML += "<b></b>";
  var a1 = document.createElement("div");
  a1.innerText = "点我会执行onclick";
  a1.onclick = function(){alert(1)};
  a.appendChild(a1);
  document.getElementsByTagName("body")[0].appendChild(a);
}
````
##### 2.outerHtml 属性
    语法：`let content = element.outerHTML;`
    返回调用它的元素及其所有子节点的HTML标签也。outerHTNL会根据指定的HTML创建新的DOM子树，然后用这个DOM子树替换调用元素。
###### 注意：如果元素没有父元素，即如果它是文档的根元素，则设置其outerHTML属性将抛出一个带有错误代码 NO_MODIFICATION_ALLOWED_ERR 的 DOMException 。
```js
let container = document.documentElement.outerHTML = "test";
 console.log(container);
 /*
 错误显示
 html:15 Uncaught DOMException: Failed to set the 'outerHTML' property on 'Element': This element's parent is of type '#document', which is not an element node.
 */
```
##### 3.insertAdjacentHTML()方法
    插入标记的最后一个新增方式是insertAdjacentHTML()方法。他接受两个参数：插入位置和要插入的HTML文本。
语法
> element.insertAdjacentHTML(position, text);

position是相对于元素的位置，并且必须是以下字符串之一：

    'beforebegin'：元素自身的前面。
    'afterbegin':插入元素内部的第一个子节点之前。
    'beforeend':插入元素内部的第一个子节点之后。
    'afterend':元素自身的后面。
```html
<div id="one">one</div>
```
```js
<script>
    //作为前一个同辈元素插入
      var d1 = document.getElementById("one");
      d1.insertAdjacentHTML("beforebegin", '<div id="two">two</div>');
    //插入元素内部的第一个子节点之前
      var d1 = document.getElementById("one");
      d1.insertAdjacentHTML("afterbegin", '<div id="two">two</div>');
    //插入元素内部的第一个子节点之后。
        var d1 = document.getElementById("one");
      d1.insertAdjacentHTML("beforeend", '<div id="two">two</div>');
    //元素自身的后面。
        var d1 = document.getElementById("one");
      d1.insertAdjacentHTML("afterend", '<div id="two">two</div>');
</script>
```
##### 4.内存与性能问题
    使用本节点介绍的方法替换自己点可能会导致浏览器内存占用问题。在使用innerHTML、outerHTML、属性和insertAdjacentHTML()方法时，最好先手工删除要替换的元素的所有时间处理程序和Javascript对象属性。
    设置innerHTML和outerHTML时，就会创建一个HTML解析器。不可避免地，创建好人销毁HTML解析器也会带来性能的损失，最好能够将设置innerHTML或outerHTML的次数控制在合理的范围内。
```js
    for (var i=0,len=values.length; i < len; i++){
        ul.innerHTML += "<li>"+values[i]+"</li>";  //要避免这种频繁操作！！ 效率太低
    }
    //最好方法是单独构建字符串，然后再一次性地将结果字符串赋值给innerHTML。
    var itemsHtml = "";
    for (var i=0,len=values.length; i < len; i++){
        itemsHtml += "<li>"+values[i]+"</li>";
    }  //这个例子的效率高得多
    ul.innerHTML = itemsHtml;
```
##### 5.scrollIntoView()方法
    scrollIntoView滚动浏览器窗口或某个容器元素，调用元素可以出现在视口中。如果给这个方法传入true作为参数，那么窗口滚动之后调用元素顶部与视口顶部尽可能平齐。如果传入false作为参数，调用元素尽可能全部出现在视口中（可能的话，调用元素的底部会与视口顶部平齐。）不过顶部不一定平齐。

语法
> element.scrollIntoView(); // 等同于element.scrollIntoView(true) 
  element.scrollIntoView(alignToTop); // Boolean型参数 
  element.scrollIntoView(scrollIntoViewOptions); // Object型参数


```js
    //让元素可见
    document.forms[0].scrollIntoView();
``` 
当页面发生变化时，一般会用这个方法来吸引用户的注意力。实际上，为某个元素设置焦点也会导致浏览器滚动并显示出获得焦点的元素。
## 11.4专有扩展
### 11.4.1文档模式
---
    要强制浏览器以某种模式渲染页面，可以使用HTTP头部信息 X-UA-Compatible,或通过等价的<meta>标签来设置：
```html
<meta http-equiv="X-UA-Compatible" content="IE=IEVersion">
```
```js
//通过document.documentMode属性可以知道给定页面使用的是什么文档模式。
var mode = document.documentMode;
console.log(mode)
```
### 11.4.2 children属性
---
    由于IE9之前的版本与其他浏览器在处理文本节点中的空白符时有差异，因此就出现了children属性。除此之外，children属性与childNodes没有什么区别，即在元素只包含元素节点时，这两个属性的值相同。
```js
var childCount = element.children.length;
var firstChild = element.children[0];
```
### 11.4.3 contains()方法
---
    在实际开发中，经常需要知道某个节点是不是另一个节点的后代。调用contains()方法的应该是祖先节点，也就是搜索开始的节点，这个方法接受一个参数，即要检测的后代节点。如果被检测的节点是后代节点，该方法返回true;否则返回false。
```js
 alert(document.documentElement.contains(document.body))
```
### 11.4.4 插入文本
    innerText与outerText没有被纳入HTML中。
- innerText属性：属性表示一个节点及其后代的“渲染”文本内容。
```js var d1 = document.getElementById("one");
      d1.innerText = "Hello world!";
        console.log(d1.innerText)
```
- outerText属性：outerText不只是替换调用它的子节点，而是替换整个元素(包括子节点)。
```js
const d = document.getElementById("d");
      //   d.outerText = "Hello world";
      var text = document.createTextNode("Hello world!");
      d.parentNode.replaceChild(text, d);
```
本质上，新的文本节点会完全取代调用outerText的元素。此后，该元素就从文档中被删除，无法访问。
### 11.4.5 滚动
---
- scrollIntoViewIfNeeded(alignCenter):只在当前元素在视口中不见得情况下，才滚动到浏览器窗口或容器元素，最终让他可见。
   - 如果为true，则元素将在其所在滚动区的可视区域中居中对齐。
   - 如果为false，则元素将与其所在滚动区的可视区域最近的边缘对齐

>//在当前元素不可见的时候，让它进入浏览器的视口
document.images[0].scrollIntoViewIfNeeded();

>document.body.scrollByLines(5); //将页面主体往上滚5行

>window.scrollByPages(pages)   
document.body.scrollByPages(-1)  //将页面主体回滚1页

由于scrollIntoView()是唯一一个所有浏览器都支持的方法，因此还是这个方法最常用。









