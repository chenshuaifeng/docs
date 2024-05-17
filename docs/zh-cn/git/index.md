## 生成ssh

1. 打开git操作面板
2. 在git命令窗口配置用户，输入命令：`git config --global user.name "chenshuaifeng"`
3. 接着进行邮箱配置，输入命令：`git config --global user.email "997174616@qq.com"`。"997174616@qq.com"就是填入你自己的邮箱地址;
4. 此时在C:\Users\Administrator目录下会生成.gitconfig配置文件
5. 查看.gitconfig配置文件里的内容;
6. 继续在git命令窗口中输入命令：ssh-keygen -t rsa -C "997174616@qq.com"，就可以生成SSH公钥和私钥了;一直点继续就行了
7. 进入C:\Users\Administrator\.ssh目录，查看生成的SSH密钥;
8. 将公钥pub配置进gitlab就行

## 离线安装vscode插件
1. 默认安装目录
按Win+E打开资源管理器，C:\Users\你的用户名\.vscode\extensions，就是Vscode的扩展安装目录。
其他：
在扩展面板中可以查看到Vscode安装的所有扩展程序，点击扩展程序可以查看其详细信息。

2. 将下载的文件拷贝进去


## vue代码统一格式规范

```js
root = true

[*]
indent_style = space
indent_size = 4

# we recommend you to keep these unchanged
# 我们建议你保持这些不变
# 换行符类型 = lf
end_of_line = lf
# 字符集=utf-8
charset = utf-8
# 删除行尾空格 = 是
trim_trailing_whitespace = true
# 插入最后一行=真
insert_final_newline = true

[*.md]
# 删除行尾空格 = 否
trim_trailing_whitespace = false

[package.json]
# 缩进样式=空格
indent_style = space
# 缩进大小=2
indent_size = 4

```

`shift + alt + f`

## Git提交规范

1. 下载包

```js
npm install commitizen
npm install cz-customizable
```

package.json配置
```js
"config": {
    "commitizen": {
      "path": "node_modules/cz-customizable"
    },
    "cz-customizable": {
      "config": ".cz-config.js"
    }
  },

```

2. vscode安装Visual Studio Code Commitizen Suppor插件
3. 根目录下创建.cz-config.js文件

```js
module.exports = {
	skipQuestions: ['scope', 'body', 'breaking', 'footer'],
	types: [
		{ value: 'feat', name: '新功能' },
		{ value: 'fix', name: '修补bug' },
		{ value: 'docs', name: '文档更新' },
		{ value: 'style', name: '样式更新' },
		{ value: 'refactor', name: '结构变动' },
		{ value: 'test', name: '添加测试' },
		{ value: 'chore', name: '构建过程或辅助工具的变动' },
	],
}

```
## Husky限制

1. 安装 ` npm i -D husky`



2. package.json
```js
"husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
```

### 自定义提交规范
1. 安装包 `npm i -D commitlint-config-cz  cz-customizable`

2. 添加配置文件 `commitlint.config.js`
3. package.json

```js
"config": {
    "commitizen": {
      "path": "node_modules/cz-customizable"
    }
  },
```
4. .cz-config

```js
module.exports = {
  types: [
    {      value: 'init',      name: 'init:     初始提交'    },
    {      value: 'feat',      name: 'feat:     增加新功能'    },
    {      value: 'fix',      name: 'fix:      修复bug'    },
    {      value: 'ui',      name: 'ui:       更新UI'    },
    {      value: 'refactor',      name: 'refactor: 代码重构'    },
    {      value: 'release',      name: 'release:  发布'    },
    {      value: 'deploy',      name: 'deploy:   部署'    },
    {      value: 'docs',      name: 'docs:     修改文档'    },
    {      value: 'test',      name: 'test:     增删测试'    },
    {      value: 'chore',      name: 'chore:    更改配置文件'    },
    {      value: 'style',      name: 'style:    样式修改不影响逻辑'    },
    {      value: 'revert',      name: 'revert:   版本回退'    },
    {      value: 'add',      name: 'add:      添加依赖'    },
    {      value: 'minus',      name: 'minus:    版本回退'    },
    {      value: 'del',      name: 'del:      删除代码/文件'    }
  ],
  scopes: [],
  messages: {
    type: '选择更改类型:\n',
    scope: '更改的范围:\n',
    // 如果allowcustomscopes为true，则使用
    // customScope: 'Denote the SCOPE of this change:',
    subject: '简短描述:\n',
    body: '详细描述. 使用"|"换行:\n',
    breaking: 'Breaking Changes列表:\n',
    footer: '关闭的issues列表. E.g.: #31, #34:\n',
    confirmCommit: '确认提交?'
  },
  allowCustomScopes: true,
  allowBreakingChanges: ["feat", "fix"]};
```

## icon使用
<el-icon><Plus /></el-icon>
<el-icon><Expand /></el-icon>
<el-icon><HelpFilled /></el-icon>