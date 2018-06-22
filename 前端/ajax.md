# 原生AJAX实现
AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。

> 在不重载整个网页的情况下，AJAX 通过后台加载数据，并在网页上进行显示。可以从远程服务器上请求文本,HTML,XML,JSON，同时能够把这些外部数据直接载入网页的被选元素中。

通过 AJAX，JavaScript 无需等待服务器的响应，而是：

1. 在等待服务器响应时执行其他脚本;
2. 当响应就绪后对相应进行处理。

## XMLHttpRequest
XMLHttpRequest对象是AJAX的基础。创建对象：`var variable=new XMLHttpRequest();`

### 服务器请求
- xmlHttpRequest.open(method,url,async)
    - method:（get/post）规定请求的类型；
    - URL:(文件在服务器上的位置)以及是否异步处理请求；
    - async:true(异步)/false(同步，这种情况下会等到服务器响应就绪才继续执行，如果服务器繁忙或缓慢，应用程序会挂起或停止。)
- xmlHttpRequest.send(string) 将将请求发送到服务器（带 string 参数仅用于 POST 请求）。

```javascript
// url参数是服务器上文件的地址，该文件可以回任何类型的文件，如txt,xml 或 php（在传回响应之前，能够在服务器上执行任务。）
xmlhttp.open("GET","test.txt",true);
xmlhttp.send();
```

使用 GET 请求可能得到的是缓存的结果，为了避免可以在 URL 添加一个唯一的 ID:

```javascript
xmlhttp.open("GET","demo_get.asp?t="+Math.random(),true);
xmlhttp.send();
```

如果需要像 HTML 表单那样 POST 数据，使用 setRequestHeader() 来添加 HTTP 头，然后在 send() 方法中指定要发送的数据：

```javascript
xmlhttp.open("POST","ajax_test.asp",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```

### 服务器响应
如果要获得来自服务器的响应，要使用XMLHttpRequest对象的responseText或者responseXML属性：

- responseText：获得字符串形式的响应数据。如果服务器的响应并非XML，就使用responseText属性，它以字符串形式返回，eg:`document.getElementById("myDiv").innerHTML=xmlhttp.responseText;`
- responseXML：获得XML形式的响应数据。如果来自服务器的响应是XML，而且需要作为XML对象进行解析，就使用responseXML属性

### onreadystatechange事件
>每当readyState改变时，就会触发onreadystatechange事件（也就是说该事件会触发5次，每次对应着readyState的每个变化），readyState属性存有XMLHttpRequest的状态信息。

XMLHttpRequest对象的三个重要属性：

- onreadystatechange :存储函数（或函数名），每当readyState属性改变时，就会调用该函数
- 存有XMLHttpRequest的状态，从0到4发生变化。
    - `0`：请求未初始化
    - `1`：服务器连接已建立
    - `2`：请求已接收
    - `3`：请求处理中
    - `4`：请求已完成，且响应已就绪
- status
    - `200`:"OK"
    - '404':未找到页面

eg:指定当readyState等于4且状态为200时，所执行的函数：

```javascript
xmlhttp.onreadystatementchange=function(){
    if(xmlHttp.readyState==4&&xmlhttp.status==200){
        document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
}
```
