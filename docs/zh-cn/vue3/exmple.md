> 添加Vue类型声明
```js
找不到模块“./App.vue”或其相应的类型声明。ts(2307)
```

```js
/// <reference types="vite/client" />
declare module "*.vue" {
    import type { DefineComponent } from "vue";
   
    const vueComponent: DefineComponent<{}, {}, any>;
   
    export default vueComponent;
}
```

> SFC

单文件组件
`script` `setup`一个 普通多个 `template`一个 ` style`多个

### vite-plugin
vue3 vscode 插件 Volar `Vue Language Fearures `和`Typescript Vue Plugin`  

vue2 vscode `vetur`禁用
**重新加载插件**

### npm run dev
> vite

执行vite命令过程：
```js
  "bin": {
    "vite": "bin/vite.js"
  },
```
```js
// unix
#!/bin/sh
basedir=$(dirname "$(echo "$0" | sed -e 's,\\,/,g')")

case `uname` in
    *CYGWIN*|*MINGW*|*MSYS*) basedir=`cygpath -w "$basedir"`;;
esac

if [ -x "$basedir/node" ]; then
  "$basedir/node"  "$basedir/../vite/bin/vite.js" "$@"
  ret=$?
else 
  node  "$basedir/../vite/bin/vite.js" "$@"
  ret=$?
fi
exit $ret
```
```js
// windows平台
@ECHO off
SETLOCAL
CALL :find_dp0

IF EXIST "%dp0%\node.exe" (
  SET "_prog=%dp0%\node.exe"
) ELSE (
  SET "_prog=node"
  SET PATHEXT=%PATHEXT:;.JS;=;%
)

"%_prog%"  "%dp0%\..\vite\bin\vite.js" %*
ENDLOCAL
EXIT /b %errorlevel%
:find_dp0
SET dp0=%~dp0
EXIT /b
```

### 查找机制
1. 先去本地node_modules找vite可执行程序
2. 本地全局node_modules
3. 环境变量中查找