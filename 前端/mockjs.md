# Mock.js

使用 Mock.js 可以拦截 Ajax 请求，返回前端自定义的 json 数据，或者根据自身的模板生成 json 数据。

## 安装

官方文档：[mock](https://github.com/nuysoft/Mock/wiki)

```bash
npm install mockjs --save-dev
```

## 使用

![mock](https://wx3.sinaimg.cn/large/9e6aadb3gy1frncginapij204706lmx5.jpg)

(1). 新建 mock 文件夹，并在文件夹下新建 json 文件（要返回的数据文件），然后再新建 mock.js，并配置以下内容：

```javascript
// 引入mockjs
const Mock = require('mockjs')
// 引入json数据文件
const deliveries = require('./deliveries.json')

// 使用mockjs拦截请求，返回 deliveries.json 的内容
Mock.mock('/api/deliveries', (req, res) => {
  return deliveries
})

// 使用mockjs拦截请求，返回 {"name": "zhangsan"}
Mock.mock('/api/user', (req, res) => {
  return {
    'name': 'zhangsan'
  }
})
```

(2). 在 `src/main.js` 文件下引入

```javascript
require('../mock/mock')
```

(3). 获取数据

```javascript
this.$http.get('/api/deliveries').then(res => {
  this.db = res.data
}
```
