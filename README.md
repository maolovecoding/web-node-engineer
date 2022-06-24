# 前端工程化学习

## 解决node中使用esm问题

`ESM`不再提供`Node`某些特性与不能灵活引用`json文件`了，因此`__dirname`、`__filename`、`require`、`module`和`exports`这几个特性将无法使用。

不过我们可以自动打上补丁，来解决这些问题：

- `__filename`与`__dirname`可用`import.meta`对象重建
- `require`、`module`和`exports`可用`import`与`export`代替
- `json文件`的引用可用`Fs模块`的`readFileSync`与`JSON.parse()`代替

```js
import { readFileSync } from "fs";
import { dirname } from "path";
import { fileURLToPath } from "url";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
console.log(__filename, __dirname);

const json = readFileSync("./info.json");
const info = JSON.parse(json);
```



## 使用babel转义高级es语法

-  **@babel/cli**：提供支持`@babel/core`的命令运行环境
-  **@babel/core**：提供转译函数
-  **@babel/node**：提供支持`ESM`的命令运行环境
-  **@babel/preset-env**：提供预设语法转换集成环境

```json
{
    "script":{
        "start":"babel-node src/index.js"
    },
    	"babel": {
		"presets": [
			"@babel/preset-env"
		]
	}
}
```



### 安装nodemon监控文件变化

```shell
pnpm i nodemon -D
```

在`package.json`中指定`nodemonConfig`相关配置，将`start`命令替换为`nodemon -x babel-node src/index.js`

```json
{
	"nodemonConfig": {
		"env": {
			"NODE_ENV": "dev"
		},
		"execMap": {
			"js": "node --harmony"
		},
		"ext": "js json",
		"ignore": [
			"dist/"
		],
		"watch": [
			"src/"
		]
	}
}
```



