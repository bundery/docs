# ES6

## 解构赋值

ES6 允许按照一定的模式，从数组和对象中提取值，对变量进行赋值，这种方式成为解构（Destructuring ）。

### 数组

```javascript
// a被赋值为1，b被赋值为2，c被赋值为3
let [a,b,c]=[1,2,3]

// c被赋值为3
let [,,c]=[1,2,3]

// a被赋值为1，b被赋值为2
let [a,b]=[1,2,3]

// a被赋值为1，b被赋值为[2,3]
let[a,...b]=[1,2,3]

// 均报错，因为都不可迭代（没有实现 Iterator 接口），所以无法解构
let [a]=1/false/NaN/undefined/null/{}

```

如果解构不成功，变量会被赋值为`undefined`：

```javascript
// b=undefined
let [b]=[]
let [a,b]=[1]
```

解构赋值可以指定默认值：

> 解构赋值使用 `===` 来判断要不要用默认值，只有`要赋的值  ===  undefined`，默认值才会生效。

```javascript
// a=true
let [a = true]=[]

// a=1,b=2
let [a,b=2]=[1]

// a=1,b=2
let [a,b=2]=[1,undefined]

// a=1,b=NaN
let [a,b=2]=[1,NaN]

```

### 对象

解构不仅可以用于数组，还可以用于对象：

```javascript
// a=1,b=2
let {a,b}={a:1,b:2}

/**
* 如果解构不成功，变量会被赋值为undefined
* a=1,b=2,c=undefined
*/
let {a,b,c}={a:1,b:2}
```

可以给赋值的变量起别名：

```javascript
/**
* A=1,B=2  a,b=undefined，真正被赋值的是后面的变量，前面的只是模式匹配
* 因此 let {a,b}={a:1,b:2} 就是 let {a:a,b:b}={a:1,b:2} 的缩写
*/
let {a:A,b:B}={a:1,b:2}
```

指定默认值，只有`要赋的值 === undefined`，默认值才会生效：

```javascript
// a=1
let {a=1}={a:undefined}

// a=null
let {a=1}={a:null}
```

```javascript
// 报错，因为JavaScript引擎会把{a}理解成一个代码块，因此要使用指定的语法
let a
{a}={a:1}

// 正确
let a
({a}={a:1})
```

### 用途

#### 函数参数

```javascript
// 为参数a,b赋初始值为1,2；为对象参数{}赋初始值为{}
function greet({a=1,b=2}={}){
	return [a,b]
}

// 只为对象参数赋初始值{a:1,b:2},并没有为a,b赋默认值
function greet({a,b}={a:1,b:2}){
	return [a,b]
}
```

#### 提取JSON数据

```javascript
let jsonData={
  a:1,
  b:2,
  c:true
}
// a=1,b=2,C=true
let {a,b,c:C} = jsonData
```

#### 遍历Map

```javascript
let map=new Map()
map.set('a',1)
map.set('b',2)
// 获取key,value
for(let [key,value] of map){
    console.log(key+':'+value)
}
// 获取key
for(let [key] of map){
    console.log('key='+key)
}
// 获取value
for(let [,value] of map){
    console.log('value='+value)
}
```

## Map和Set

虽然 JavaScript 的`{}`跟其他语言的`Map` 比较像，但 {} 的键值只能为字符串。

### Map

一个 `key` 只对应一个 `value`，添加多个 key 会覆盖前一个 value。

```javascript
// 1.传入二维数组初始化
let map=new Map([['a',1],['b',2],[2,'c']])
// 2.构造空的
let map=new Map()
// 添加新的key-value
map.set('d',4)
// 查找'a'对应的value，'a'=1
map.get('a')
```

### Set

`Set` 相当于 Map 的键，key 值也不能重复。

```javascript
// 1. 传入数组进行初始化
let set=new Set([1,2,3])
// 2. 构造空的
let set=new Set()
// 添加新值，3和'3'不同
set.add('3')
// 删除已存在的值
set.delete(3)
```

## 模块

ES6 实现了模块功能，设计思想是尽量的静态化，让编译时就能确定模块的依赖关系。一个模块是一个独立的文件，文件内部的所有变量外部无法获取，如果希望外部读取模块内部的某个变量，`export`导出`import` 导入功能。

> Node.js 的模块机制是通过 `require` 和 `module.exports` ,`exports`实现的，它们的实现机制是动态的，在运行时才能确定，rquire 引用后返回的是对象，而 ES6 是静态加载。

?> export 和 module.exports 指向同一个对象，即 exports =module.exports，而 require 引用后返回的是 model.exports。

### export

export 命令用于规定模块的对外接口。

```javascript
// hello.js文件
export let a=1
export let b=2
export let c=3
```

上面代码就是一个模块，使用了 export 命令对外部输出了三个变量。

也可以写成这种形式（推荐）：

```javascript
// hello.js文件
let name='jerry'
let age='23'
let person={name:'jerry',age:'23'}

function getPersonInfo(name,age){
    return '名字：'+name+'年龄：'+age;
}

// 不仅可以输出变量也可以输出函数，而且可以使用as重命名对外的接口
export{name as fullName,age,person,getPersonInfo}
```

#### export default

为模块指定默认输出，一个模块只能有一个默认输出。

```javascript
// './hello.js'
function getPersonInfo(){}
/**
* 可以不用加大括号
* 可以看作 export {getPersonInfo as default}
*/
export default getPersonInfo

//'greet.js'
/**
* 可以不用加大括号，因为只有一个默认输出，而且可以取任意名字
* 可以看作 export{default as getPersonInfo_f}
*/
import getPersonInfo_f from './hello.js'

/**
* 正确，因为export default就是相当于执行:
* defalut=a,export {default}
*/
let a=1
export default a

// 正确，default=1,export {default}
export default 1

// 正确,default={},export {default}
export default {}
```

#### export from

```javascript
// greet.js
export {name,age} from './hello.js'

// 上述语句简单看作为如下代码
import {name,age} from './hello.js'
export {name,age}
```

使用这种语句后，name 和 age 并没有被导入当前模块，仅仅起到了对外转发这俩个接口的作用。

!> 这种情况下 `export * from './hello.js'`将不输出`default`方法。

### import

import 用于输入其他模块提供的功能，使用 export 命令定义了模块的对外接口以后，其他 JavaScript 文件就能通过 import 加载这个模块了。

```javascript
// 加载hello.js文件，并从中输入变量及函数，同样也支持重命名
import {person,getPersonInfo as getPersonInfo_f} from './hello.js';

// 报错，输入的变量是只读的，不能修改引用
person={}
// 合法，但其他模块也访问到的是改写后的值
person.age=24


// 报错，因为是静态输入，编译时不能知道 module 的值是什么
let module = './hello.js'
import {name} from module

// 只执行hello.js，但不会导入任何变量或函数
import './hello.js'
```

## 对象字面量

### 属性

`ES6`之前：

```javascript
function getPersonInfo(){
    return{
        name: name,
        age: age
    }
}
```

`ES6`时，如果局部变量名和属性名相同，可以省略冒号及后面的内容：

```javascript
function getPersonInfo(){
    return{
        name,
        age
    }
}
```

### 方法

`ES6`之前：

```javascript
var Person={
    getPersonInfo:function(name){
        return name
    }
}
```

`ES6`时，可以省略冒号及`function`关键字：

```javascript
var Person={
    getPersonInfo(name){
        return name
    }
}
```

## Promise

Promise 对象有三种状态：

1. `pending`：进行中，操作尚未完成，一旦异步操作执行结束就变成已完成的状态；
2. `fulfilled`：已成功，`resolve(value)`执行后会变成此状态；
3. `rejected`：已失败，`reject(value)`执行后会变成此状态。

Promise 对象有`then()`方法，定义在`Promise.prototype`原型对象上:

```javascript
promise.then(
  function(value){ // 变成fulfilled状态时执行的函数，value 由 resolve(value) 传递
  
	},
  function(value){ // 变成rejected状态时执行的函数，value 由 reject(value) 传递
  
	}
)
```

> 如果一个对象有上述的`then()`方法，那就是`thenable`对象，promise 对象属于 thenable 对象。

也有`catch()`方法：

```javascript
promise.catch(
  function(value){ // 变成rejected状态时执行的函数，value 由 reject(value) 传递
  
	}
)
// 就是下面写法的别名
promise.then(
  null,
  function(value){ // 变成rejected状态时执行的函数，value 由 reject(value) 传递
  
	}
)
```

### 创建未完成的Promise

Promise 构造函数接收一个用于初始化 Promise 代码的 executor 执行器函数，执行器接收由 JS 引擎提供的 resolve 和 reject 函数。

```javascript
/** 
* resolve 和 reject 是由 JS 引擎提供的俩个函数
* 由于是单线程异步操作，所以执行成功的回调函数会在执行 promise 的主线程末尾执行
*
*/
console.log('start')

let promise = new Promise(function(resolve, reject) {
  console.log('promise'); 
  resolve()
})

promise.then(function() {
  console.log('fulfilled')
})

console.log('end')

// start
// promise
// end
// fulfilled
```

执行器函数会**立即执行**，当调用 resolve() 或者 reject() 时才会触发异步操作，传入 then() 和 catch() 方法的函数会被添加到任务队列的末尾异步执行。

```javascript
console.log('start')
// then(handler)和catch(handler)类似于setTimeout
setTimeout(function(){
  console.log('timeout')
},0)
console.log('end')

// start
// end
// timeout
```

#### 捕获异常

```javascript
let promise = new Promise(function(resolve, reject) {
 	throw new Error('error')
});

promise.catch(function(error) {
  console.log(error.message) // error
})
```

每个执行器都隐含一个`try-catch`块，所以错误会被捕获并进入 reject 处理程序，相当于：

```javascript
let promise = new Promise(function(resolve, reject) {
  try {
    throw new Error('error')
  } catch (ex) {
    reject(ex)
  }
});

promise.catch(function(error) {
  console.log(error.message) // error
})
```

所以如果不指定 reject 错误处理程序，执行器中抛出的所有错误会被忽略掉。

### 创建已完成的Promise

如果 Promise 对象不需要执行初始化执行器，可以使用 `Promise.resolve(value)`或`Promise.reject(value)`创建状态完成的 Promise 对象。

```javascript
let promise = Promise.resolve(10);
// 可以理解为下列语句
let promise = new Promise(function(resolve,reject){
  resolve(10)
})

promise.then(function(value){
  console.log(value) // 10 
})
```

> 如果向 Promise.resolve() 或 Promise.reject() 传递一个 Promise 对象，那返回的是此 Promise 对象而不再新建一个 Promise 对象返回。

#### 接收非Promise的Thenable对象

如果传入一个非 Promise 的 Thenable 对象，会先创建一个新的 Promise 对象，并把 then 方法作为参数传递到构造函数里，即：`new Promise(then)`。

```JavaScript
console.log('start')
let thenable = {
  then: function(resolve, reject){
    console.log('thenable')
    resolve(10)
  }
}
let promise = Promise.resolve(thenable)
promise.then(function(value){
  console.log(value) 
})
console.log('end')

// start
// end
// thenable
// 10
```

### 链式调用

每次调用 then() 或者 catch() 方法都会创建并返回一个新的 Promise 对象，只有第一个 Promise 对象处理完成之后才会调用第二个 Promise 对象的 then() 方法。

```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('promise'); 
  resolve(10)
})

let promise2 = promise.then(function(value) { // 接收上面传递的10
  console.log(value) // 10
  return value+1
})

promise2.then(function(value2){ // 接收上面传递的 value+1
  console.log(value2) // 11
})

// -------------------链式调用写法-------------------

promise.then(function(value) { // 接收上面传递的10
  console.log(value) // 10
  return value+1
}).then(function(value2){ // 接收上面传递的 value+1
  console.log(value2) // 11
})
```

```javascript
let promise2 = promise.then(function(value) { // 接收上面传递的10
  console.log(value) // 10
  return value+1
})
// 上面代码可以理解为（仅个人理解，暂时还没看过源码）：
let promise2 = new Promise(value,function(resolve,reject){
  console.log(value)
  resolve(value+1)
})
```

#### 返回Promise对象

```javascript
let promise = new Promise(function(resolve,reject){
  resolve(10)
})
let promise2 = new Promise(function(resolve,reject){
  resolve(20)
})
promise.then(function(value){
  console.log(value) // 10
  return promise2
}).then(function(value){
  console.log(value) // 20
})
```

个人理解：如果返回的是 Promise 对象，promise.then() 就不再创建 Promise 对象了，而是直接返回。