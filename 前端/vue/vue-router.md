# vue-router

前端`router`一般是指根据 url 来显示或隐藏 DOM 节点，`Vue-router` 是指根据 url 来显示或隐藏`	组件`，用于开发 `SPA` （单页面应用）。使用路由时，`url` 也会改变。

> 其实 vue-router 的实现原理就是封装的的动态组件技术，类似于 jQuery 封装的 $.ajax 和 XMLHttpRequest 的关系。

```html
<div id="app">
    <!--会被解析为a标签-->
    <router-link to="/home">主页</router-link>
    <router-link to="/second">第二页</router-link>
    <h3>---------------</h3>
    <!--路由匹配到组件会显示在此标签内-->
    <router-view></router-view>
</div>

<script>
    // 定义俩个组件
    const home = Vue.extend({
        template: '<h1>home</h1>'
    });
    const second = Vue.extend({
        template: '<h1>second</h1>'
    });

    // 配置路由
    const routes = [
        { path: '/home', component: home },
        { path: '/second', component: second },
        //默认打开时的组件
        { path: '*', component: second }
    ]

    // 生成路由实例
    const router=new VueRouter({routes:routes});

    // 挂载在vue上，es6的router:router可以替换省略:router
    var vm = new Vue({
        router:router,
        el: '#app'
    }) 
</script>
```

