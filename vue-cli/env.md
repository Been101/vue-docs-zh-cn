## 环境变量和模式

- [概述](#概述)
- [模式](#模式)
- [在客户端侧代码中使用环境变量](#在客户端侧代码中使用环境变量)
- [只在本地有效的变量](#只在本地有效的变量)

### 概述

你可以替换你的项目根目录中的下列文件来指定环境变量：

``` sh
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```

一个环境文件只包含环境变量的“键=值”对：

```
FOO=bar
VUE_APP_SECRET=secret
```

被载入的变量将会对 `vue-cli-service` 的所有命令、插件和依赖可用。

### 模式

**模式**是 Vue CLI 项目中一个重要的概念。默认情况下，一个 Vue CLI 项目有三个模式：

- `development` 模式用于 `vue-cli-service serve`
- `production` 模式用于 `vue-cli-service build` 和 `vue-cli-service test:e2e`
- `test` 模式用于 `vue-cli-service test:unit`

注意模式不同于 `NODE_ENV`，因为一个模式可以包含多个环境变量。说到这里，每个模式都会将 `NODE_ENV` 的值设置为模式的名称——比如在 development 模式下 `NODE_ENV` 的值会被设置为 `"development"`。

你可以通过为 `.env` 文件增加后缀来设置某个模式下的环境变量。比如，如果你在项目根目录创建一个名为 `.env.development` 的文件，那么在这个文件里声明过的变量就只会在 development 模式下被载入。

你可以通过传递 `--mode` 选项标记为命令行覆写默认的模式。例如，如果你想要在构建命令中使用开发环境变量，请在你的 `package.json` 脚本中加入：

```
"dev-build": "vue-cli-service build --mode development",
```

### 在客户端侧代码中使用环境变量

只有以 `VUE_APP_` 开头的变量会被 `webpack.DefinePlugin` 静态嵌入到客户端侧的包中。你可以在应用的代码中这样访问它们：

``` js
console.log(process.env.VUE_APP_SECRET)
```

在构建过程中，`process.env.VUE_APP_SECRET` 将会被相应的值所取代。在 `VUE_APP_SECRET=secret` 的情况下，它会被替换为 `"sercet"`。

除了 `VUE_APP_*` 变量之外，在你的应用代码中始终可用的还有两个特殊的变量：

- `NODE_ENV` - 会是 `"development"`、`"production"` 或 `"test"` 中的一个。具体的值取决于应用运行的[模式](#模式)。
- `BASE_URL` - 会和 `vue.config.js` 中的 `baseUrl` 选项相符，即你的应用会部署到的基础路径。

### Index HTML 中的环境变量

通过 [lodash 模板插入](https://lodash.com/docs/4.17.5#template)，所有解析出来的环境变量在 `public/index.html` 是可用的：

- `<%= VAR %>` 用于非转义插入；
- `<%- VAR %>` 用于 HTML 转义后的插入；
- `<% expression %>` 用于 JavaScript 控制流。

举个例子，为了引用被拷贝到 `public` 根目录的静态资源，你会用到 `BASE_URL` 变量：

``` html
<link rel="shortcut icon" href="<%= BASE_URL %>favicon.ico">
```

### 只在本地有效的变量

有的时候你可能有一些不应该提交到代码仓库中的变量，尤其是当你的项目托管在公共仓库时。这种情况下你应该使用一个 `.env.local` 文件取而代之。本地环境文件默认会被忽略，且出现在 `.gitignore` 中。

`.local` 也可以加在指定模式的环境文件上，比如 `.env.development.local` 将会在 development 模式下被载入，且被 git 忽略。
