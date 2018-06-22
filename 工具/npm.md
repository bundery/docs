# npm

npm 是 `Node.js`的包文件管理器（Node Package Manager），可以在上面下载别人上传的 JavaScript 代码。最重要的是 npm 可以根据包的依赖关系把依赖的模块都下载下来。安装 Node.js 后会自带 npm。

> Node.js 是在后端运行 JavaScript 代码，所以本机要先安装 Node.js。

## 模块

一个 .js 文件就是一个模块（module），当一个模块编写完成之后，就可以被其他地方引用。

## 命令

### install 

- 本地安装 `npm install <packageName>` 安装模块到当前项目的 `node_modules` 目录（会先检查本地存在不存在，不存在才会安装）；
- 全局安装 ``npm install <packageName> -g`，安装到 node 的环境变量目录的 `node_modules` 目录下。全局安装只适合安装工具模块。

?> 如果想将下载的包加入到 `package.json`依赖，使用`--save`（dependencies）或`--save-dev`(devDependencies)。

?> `npm install`默认会安装`dependencies`字段和`devDependencies`字段中的所有模块，如果使用`--production`参数，可以只安装`dependencies`字段的模块。  

### list

- `npm list [-g]` 查看全局/本地安装的所有模块；
- `npm list packageName` 查看指定包的安装信息。

### uninstall

`npm uninstall packageName` 卸载指定的包。

### update

`npm update packageName` 更新指定的包。

### search

`npm search packageName` 搜索包。

### run

`npm`不仅可以用于模块管理，还可以用于执行脚本。`package.json`文件有一个`scripts`字段，可以用于指定脚本命令，供`npm`直接调用。 

```json
{
  "name": "myproject",
  "devDependencies": {
    "jshint": "latest",
    "browserify": "latest",
    "mocha": "latest"
  },
  "scripts": {
    "lint": "jshint **.js",
    "test": "mocha test/"
  }
}
```

上述 scripts 配置了俩项命令，`npm run-script lint ` /`npm run-script test`  就会执行 jshint / test，`npm run`是`npm run-script` 的缩写。

?> npm 内置了两个命令简写，`npm test`=`npm run test`，`npm start`=`npm run start`。 

?> `npm run`会创建一个 `shell`执行指定的命令，并临时将`node_modules/.bin`加入 `$PATH` 环境变量，也就是说本地模块可以直接运行。

## package.json

`package.json` 位于每个模块的目录下，用于定义包的属性。

`npm init` 创建 package.json 文件。

- **name** - 包名；
- **version** - 包的版本号；
- **description** - 包的描述；
- **homepage** - 包的官网 url ；
- **author** - 包的作者姓名；
- **contributors** - 包的其他贡献者姓名；
- **dependencies** - 依赖包列表，如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下，用于生产环境；
- **devDependencies** - 开发和测试环境；
- **repository** - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上；
- **main** - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js；
- **keywords** - 关键字。

