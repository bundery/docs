# Vue.js

一个 Vue 程序由一个使用 `new Vue`创建的 `Vue 根实例`和`可复用的组件 `组成。

```html
//创建一个Vue实例
<script>
    //vm是ViewModel的缩写
	var vm = new Vue({
  		// 选项
        el: '#app',
        data: {
          message: 'Hello Vue!'
        }
	})
</script>
```

