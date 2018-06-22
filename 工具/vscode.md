# vscode

## 配置Vue开发环境

1. 安装 `VueHelper` 插件，并修改配置（ `User Settings`文件）：

   ```json
   // 配置语言的文件关联(如: "*.extension": "html")，让 VueHelper 插件支持 .vue 格式的文件
   "files.associations": {
       "*.vue": "html"
   }，
   ```

2. 安装`eslint`插件，并修改配置（ `User Settings`文件）：：

   ```json
   // eslint 保存时自动按照定义的规则修复
   "eslint.autoFixOnSave": true,
   
   // eslint 校验的语言
   "eslint.validate": [
       "javascript",
       "javascriptreact",
       {
           "language": "html",
           "autoFix": true
       },
       {
           "language": "vue",
           "autoFix": true
       }
   ],
   ```

   