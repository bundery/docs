# jQuery

- `$(document).ready(function)`：将函数绑定到文档的就绪事件（当页面 DOM 元素加载完成后执行），可以在同一个页面中无限次地使用该事件，其中注册的函数会按照代码的先后顺序依次执行；

  !> 在 html 的头部的 script 标签中不处于 ready() 中的代码将早于 ready() 执行。

- `$(window).load()`：当 dom元素 以及页面的资源文件（如 css、scripts、图片等）加载完毕后执行此方法；


## 执行器链
> 可在相同的元素上运行多个 jQuery 函数。

```javascript
// 先变红再向上滑动，然后向下滑动
$('#p1').css('color','red').slideUp(2000).slideDown(2000) 
```

## DOM
- `$(selector).text()`： 设置或返回所选元素的文本内容；
- `$(selector).html()`： 设置或返回所选元素的内容（包括 HTML 标签）；
- `$(selector).val()`： 设置或返回表单字段的值；
- `$(selector).attr()`： 设置或返回属性值；
- `$(selector).append()`：在被选元素的结尾插入内容；
- `$(selector).after()`：在被选元素之后插入内容。

## CSS
- `$(selector).addClass()`：向被选元素添加一个或多个class属性；
- `$(selector).removeClass()`：向被选元素移除一个或多个属性；
- `$(selector).toggleClass()`：对被选元素进行添加/移除class的切换操作；
- `$(selector).css()`：设置或者返回被选元素的一个或者多个样式属性。

## 遍历
> jQuery 遍历根据跟其他元素的相对关系来查找 HTML 元素。从当前元素开始，在家族树中向上移动（祖先），向下移动（子孙），水平移动（同胞）。

- `$(selector).parent()` ：返回指定元素的直接父元素；
- `$(selector).first()`：返回指定元素的首个元素；
- `$(selector).filter('.intro')`：返回所有类名带 intro 的指定元素；
- `$(selector).not('.intro')`：返回所有类名不带 intro 的指定元素。

## GET&POST
> GET：`$.get(URL,callback)` 从指定的资源请求数据，基本用于从服务器取回数据，可能会返回缓存数据;
POST：向指定的资源提交要处理的数据，也可用于从服务器获取数据，不会缓存数据，且常用于连同请求一起发送数据。
