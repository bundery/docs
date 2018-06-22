# DOM规范

## addEventListener 

`addEventListener (type,listenner,{capture:false,passive:false,once:false}[optional])`;为元素增加事件监听器。

### 事件流

事件监听流存在三个阶段，事件捕获阶段、处于目标阶段、事件冒泡阶段。触发顺序是先捕获，捕获到之后再通过事件传播进行事件冒泡。如果不想让事件传播到下一个阶段，可以在事件监听器中调用 `event.stopPropagation()`。

#### 捕获阶段

当触发监听器时，浏览器自动从根节点开始由外到内进行事件传播，也就是说子元素触发监听器时，如果父元素启用了事件捕获方式注册了响应的监听器，则先会触发父元素的绑定。

#### 冒泡阶段

与事件捕获相反，是由内到外触发事件传播。

```javascript
document.getElementById('grandson').addEventListener('click',function(e){
                console.log('grandson');
    		    //阻止事件传播到下一个阶段。
                //e.stopPropagation()；
            },{
                capture: false,
                passive: false,
                once: false
            })

document.getElementById('son').addEventListener('click',function(e){
                console.log('son');
            },{
                capture: false,
                passive: false,
                once: false
            })
            document.getElementById('father').addEventListener('click',function(e){
                console.log('father');
            },{
                capture: false,
                passive: false,
                once: false
            })
```

- 如果三个 capture 都是 false，意味着没有事件捕获阶段，只有冒泡阶段，如果点击 grandson 元素，会打印 grandson,son,father；如果点击 son 元素，会打印 son,father；点击 father 元素，只会打印 father；

- 如果 son 的 capture=true 时，点击 grandson 元素，会打印 son,grandson,father（事件捕获之后再传播给事件冒泡）；如果点击 son 元素，会打印 son,father（事件冒泡）；点击 father 元素，只会打印 father；

### capture

事件捕获。

## passive

由于元素的默认行为可以被监听器通过 `preventDefault()` 方法阻止，例如`touchstart` 事件对象的默认行为是滚动当前页面，如果它的默认行为被某个监听器调用 `preventDefault()` 方法阻止了，那么它就必须静止不动，但浏览器无法知道监听器是否会调用此方法来阻止它滚动，所以只能让它等待监听器执行完成后再去执行默认行为，但监听器执行就算是个空函数也会耗时，从而导致页面卡顿。

所以`passive`就所为了解决这个问题的，只要设置了 passive=true ，它就不会阻止默认行为，即使监听器中调用了 `preventDefault()`方法，它可以在来个线程里同时执行监听器中的代码和默认行为了。

监听器是一次性的，执行一次之后就被自动 `removeEventListener` 。