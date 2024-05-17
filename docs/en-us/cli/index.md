## 前言

项目名称**Ystore** ，现已经完全**开源**，已经上架[NPM](https://www.npmjs.com/package/@ystore/cli)官网
本项目完全基于**Nodejs**技术栈开发，转载、使用请注明作者`搬砖蜗牛`

### 使用教程
1. 全局安装
```js
    npm install -g @ystore/cli
```

2. 查看安装是否成功
```js
ystore -v
```

3. 初始化项目
```js
ystore cerate project
```
<a name="ILMNh"></a>
## 基本信息
| **项目名称** | 模板仓库 | **立项时间** | 2022-10-15 |
| --- | --- | --- | --- |
| **英文名** | ...Ystore | **开发人员** | 陈帅峰 |
| **版本号** | 1.0.0 |


<a name="WFAcY"></a>
##   1   文档介绍
<a name="NwFJf"></a>
### 1.1 设计思想
“凡事预则立，不预则废”。<br />Ysotre方案设计满足前端工程化设计理念，通过一系列自动化工具实现复杂应用构建<br />Ystore方案设计尽量考虑动态需求变化，预留增量空间，延长应用使用生命周期；<br />Ystore方案设计严格遵守"五化"代码开发规范，模块化、配置化、组件化、简单化、标准化，提高开发效率，保证开发质量<br />Ystore方案设计满足系统的品质要求，通过对代码结构组织、错误系统设计，保证产品在出现故障后仍能平稳运行。<br />Ystore方案设计通过标准化的实施，降低开发门槛，提高开发效率，快速实现应用交付部署
<a name="YxaVP"></a>
### 1.2 文档组成
《Ystore方案设计》共有四主要部分组成：

   1. 背景目标，介绍项目背景，实现目标
   2. ystore/cli，介绍项目脚手架设计方案
   3. Ystore， 介绍项目的模板设计方案
   4. 尾言，分享项目中的进程
<a name="mnNTD"></a>
##   2   背景与目标
<a name="YilGI"></a>
### 2.1 项目背景
前端模板系统"Ystore"旨在快速响应项目需求，高效完成应用的搭建、部署。
<a name="KBvnw"></a>
### 2.2 实现目标
Ystore作为前端模板生态系统，其组成有

- 1个脚手架
- 2个技术栈
- 3个模板仓库

其中1个脚手架为ystore/cli。YSOTRE CLI 是一个基于 Vue.js 进行快速开发的完整系统，实现一下能力：

- 通过 @ystore/cli 实现的交互式的项目脚手架。
- 基于webpack打包构建，实现最优的性能优化方案
- 通过@ystore/cli 实现零配置原型开发
- 提供最贴近生产产品、丰富的模板选择

其中2个技术栈是模板中可选择两种技术方案：

- Vue2
- Vue3

通过基础技术方案自由选型，由此搭建出庞大复杂的应用系统;<br />其中3个模板仓库是涵盖目前公司产品放向，即

- PC桌面端
- MO移动端、
- QK微应用

三个方向，更细分设计请看模板仓库目录

前端模板系统简称Ystore，主要由两部分构成，一为脚手架系统ystore/cli，一为模板仓库TPL Repository

![](https://cdn.nlark.com/yuque/0/2022/jpeg/1427138/1671584331539-79564e21-0d92-437c-9c57-e0239d2f34ff.jpeg)

<a name="i1GrQ"></a>
### 2.3 模板仓库是什么
在多年的项目开发中，对于项目产品开发，我们有如下几点体会：

1. 产品孵化排期紧张，产品经理一般关心的是具体的业务逻辑，而前期基础模块的搭建，如各模块如何组织，使用代码结构如何选择，图片、网络、本地存储等选用哪个 sdk 等，一般不会有专门排期。
2. 基础模块的需求具有相似性内容型产品，其搭建的基础模块基本上都会包含图片显示、网络请求、本地存储、通信等。
3. 基础模块的选型和工具类具有可重用性网上相关的第三方库有很多，当然一般的公司也是会有自己开发或者维护的各个基础 SDK。很多时候，SDK 选型会更偏向于自己公司开发维护的 SDK，或者选择自己最熟悉，或最主流、最可靠的 SDK。因此当开发多个相同类型产品时，这里的技术选型是可重用的。
4. 网络请求的代码具有机械性客户端开发需要根据网络接口协议，编写相关的 GET、POST 等请求代码和对应的请求拦截和响应拦截，这部分的代码编写其实是非常机械的。

对于各个基础模块，模板仓库会封装自己的模块，如网络库、本地存储库、页面管理库、图片库等。使用我们的工程模板生成的初始工程，就已经包含了我们提供的基础模块，产品研发团队的开发不需要再花费重复的时间做技术调研、选型、SDK封装集成等工作，而只需要关心自己的业务逻辑编写。我们期望产品团队研发只需 1 分钟就能得到自己的初始工程，并能马上投入业务逻辑开发，既能缩短开发周期，也能保证工程代码质量。
<a name="e5ZGO"></a>
##   3   YSTORE CLI
ystore/cli作为:

- 1个脚手架
- 2个技术栈
- 3个模板仓库

的构成部分，Ystore/cli担负着类似于vue/cli职责，负责快速构建应用系统，<br />**前端工程体系的功能涵盖范围广，封装的方案类型多，对应的配置项也非常复杂**。对于业务开发者来说，Ystore/cli就是一个黑盒，他们不需要了解其中的复杂原理，只需要知道如何配置即可，业务开发者就通过快速配置，生成满足项目需求的模板代码。<br />Ystore/cli通过调整配置，可以搭建不同生态环境的sing-spa应用，为前端产品赋能，为公司战略赋能.
<a name="XjhVS"></a>
### 3.1 竞品分析
什么是脚手架?<br />**脚手架作用是创建项目的初始文件，本质是方案的封装**。
<a name="R8p2m"></a>
#### 3.1.1 [vue/cli](https://cli.vuejs.org/zh/guide/)
vue/cli作为前端vue开发必备的工具，已经被行业广泛应用
<a name="koiAn"></a>
#### 3.1.2 vue/cli使用过程

- 全局安装脚手架
```javascript
npm install -g @vue/cli
```

- 创建一个hello-world项目
```javascript
vue create hello-world
```

- 通过配置加载插件

![Snipaste_2022-12-16_18-14-38.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1671185698462-762518cc-1dfd-441d-8a0a-e49ee361edd3.png#averageHue=%23292620&clientId=u4e38987a-802e-4&from=ui&id=u36ba0076&name=Snipaste_2022-12-16_18-14-38.png&originHeight=329&originWidth=907&originalType=binary&ratio=1&rotation=0&showTitle=false&size=65657&status=done&style=none&taskId=u6ca8d7fc-a3e0-409a-abb4-de7c6931c1f&title=)

通过上述几项操作，我们就能快速搭建一个基于Vue技术栈的项目
<a name="i1X19"></a>
### 3.2 Ystore/cli功能分析
Ystore将集成vue/cli强大的功能

- 通过 @ystore/cli 实现的交互式的项目脚手架。
- 基于webpack打包构建，实现最优的性能优化方案
- 通过@ystore/cli 实现零配置原型开发
- 提供最贴近生产产品、丰富的模板选择

> 根据ystore/cli功能分析，设计出脚手架的使用步骤。


![ystore-cli.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1671587862400-a69cff20-fcb5-4196-aa03-e39e312e4343.png#averageHue=%23090706&clientId=ue90efd6e-235e-4&from=ui&id=u8af2d6c1&name=ystore-cli.png&originHeight=691&originWidth=919&originalType=binary&ratio=1&rotation=0&showTitle=false&size=54670&status=done&style=none&taskId=u8715f7fd-f2d8-4c80-bd16-32e1842d151&title=)

<a name="MIFes"></a>
#### 3.2.1 create
`ystore create app`是创建应用程序的指令。

1. 运行`ystore create app`第一步进行版本Check

![Snipaste_2022-12-28_13-50-34.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672206658033-03c8f4b7-b555-43bc-b77e-dd0b84af72d7.png#averageHue=%232b2925&clientId=u1e2df6a0-003a-4&from=ui&id=u95ce7a11&name=Snipaste_2022-12-28_13-50-34.png&originHeight=71&originWidth=752&originalType=binary&ratio=1&rotation=0&showTitle=false&size=10114&status=done&style=none&taskId=u693867f4-1c59-4d93-a852-b2c1432add1&title=)

2. 打印`YXT`log

![Snipaste_2022-12-28_13-43-08.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672206222540-9316518f-015a-4967-a51a-d8513290b534.png#averageHue=%231f1e1e&clientId=u1e2df6a0-003a-4&from=ui&id=u3abc3725&name=Snipaste_2022-12-28_13-43-08.png&originHeight=139&originWidth=729&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8341&status=done&style=none&taskId=ua64043d7-ed3b-4e45-9611-a67c16148fe&title=)

3. 拉取远程模板仓库列表

![Snipaste_2022-12-28_13-56-36.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672207013090-bb594003-99d9-4900-b9c8-5acca1f310be.png#averageHue=%23242322&clientId=u1e2df6a0-003a-4&from=ui&id=uc06b0253&name=Snipaste_2022-12-28_13-56-36.png&originHeight=69&originWidth=710&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5939&status=done&style=none&taskId=uedc4f7ec-c602-4fc4-bbc0-15a1892c505&title=)

4. 拉取远程模板tag

![Snipaste_2022-12-28_13-57-47.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672207076119-fdbfe8ce-4020-443c-a765-01c6d8d12e17.png#averageHue=%23242322&clientId=u1e2df6a0-003a-4&from=ui&id=uee91b5d9&name=Snipaste_2022-12-28_13-57-47.png&originHeight=86&originWidth=688&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7576&status=done&style=none&taskId=u8eef7f55-40bb-4359-af3d-77fef9e6316&title=)

5. 选择依赖管理工具

![Snipaste_2022-12-28_15-12-22.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672211563453-6f2061b6-a140-4433-a3f8-1cf84c5d93b5.png#averageHue=%23242322&clientId=u1e2df6a0-003a-4&from=ui&id=u604de4a4&name=Snipaste_2022-12-28_15-12-22.png&originHeight=55&originWidth=664&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3984&status=done&style=none&taskId=u5aad46f2-4398-4c09-ae54-c51af9efe3d&title=)

6. 缓存模板仓库

![Snipaste_2022-12-28_16-09-48.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672215015867-58a408e4-ec1e-44fb-892f-3f78799f737b.png#averageHue=%23272625&clientId=u1e2df6a0-003a-4&from=ui&id=u1dd9c4b8&name=Snipaste_2022-12-28_16-09-48.png&originHeight=35&originWidth=655&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3784&status=done&style=none&taskId=u08b54122-f80a-42d8-986b-540ff640712&title=)

7. 拷贝操作

如果模板项目中没提供ask-for-cli.js文件，则使用ncp直接拷贝代码到本地，如果存在则使用inquirer根据用户输入和选择渲染（consolidate.ejs）变量最终通过metalsmith遍历所有文件做插入修改<br />1）项目中没提供ask-for-cli.js文件

- 从缓存中拷贝模板代码
- install依赖
- 初始化git
- 删除缓存

![Snipaste_2022-12-28_16-43-08.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672216999740-82c9376f-bcb5-4400-8d7f-0015832072b8.png#averageHue=%231f1f1f&clientId=u1e2df6a0-003a-4&from=ui&id=u4167dbcc&name=Snipaste_2022-12-28_16-43-08.png&originHeight=78&originWidth=699&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3227&status=done&style=none&taskId=ua4ac5318-958c-48c9-af36-3b1661d535b&title=)

2) 项目中有ask-for-cli.js文件请看3.1.4槽语法

<a name="mu36H"></a>
### 3.3 Ystore/cli设计方法
ystore/cli基于NodeJs编写，脚手架的需要实现的一些流程<br />如下架构图，采用Lerna做项目的管理工具，目前babel、vue-cli、create-react-app大型项目均采用Lerna进行管理。它的优势在于：

- **大幅减少重复操作**。多个Package时的本地link、单元测试、代码提交、代码发布，可以通过Lerna一键操作。
- **提升操作的标准化**。多个Package时的发布版本和相互依赖可以通过Lerna保持一致性。

     



下图为ystore的设计细节图<br /> 7b   

![ystore-cli-detail.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1671681691934-198ee5c5-0ed5-4891-b8c6-630f20e8f92e.png#averageHue=%2315100e&clientId=u06444dc9-adc2-4&from=ui&id=u7ec14071&name=ystore-cli-detail.png&originHeight=583&originWidth=672&originalType=binary&ratio=1&rotation=0&showTitle=false&size=40156&status=done&style=none&taskId=u73f2806c-9996-4caa-af2d-67135a9cadd&title=)
<a name="jyZaO"></a>
#### 3.3.1 依赖准备

- chalk：控制台字符样式
- commander：node.js命令行接口的完整解决方案
- fs-extra：增强的基础文件操作库
- inquirer：实现命令行之间的交互
- ora：优雅终端Spinner等待动画
- axios：结合Gitlab API获取仓库列表、Tags...
- download-git-repo：从Github/Gitlab中拉取仓库代码
- ncp ：像cp -r一样拷贝目录、文件
- semver ：获取库的有效版本号
- ini ：一个用于节点的ini格式解析器和序列化器。主要是对配置做编码和解码。
- jscodeshift ：可以解析文件将代码从AST-to-AST。例如新建一个页面后需要在routes.ts中新建一份路由。
- single-line-lo:Node.js模块，它不断写入控制台（或流）中的同一行。在较长的操作期间编写进度条或状态消息时非常有用。支持多线。
<a name="KesEg"></a>
#### 3.3.2 环境检测

1. 检查node版本
```javascript
const semver = require('semver') // 版本号处理库，包含版本号比较，格式化等功能
const colors = require('colors/safe') // 命令行输出颜色处理
const LOWEST_NODE_VERSION = '12.0.0' // 定义最低node版本

let currentVersion = process.version
let lowestVersion = LOWEST_NODE_VERSION
if (!semver.gte(currentVersion, lowestVersion)) {
		throw new Error(colors.red(`脚手架 需要安装 v${lowestVersion} 以上版本的 node.js`))
}
```

2. root权限降级

避免脚手架开发者使用 root 权限进行文件操作，导致其他协作开发者无法对文件进行修改
```javascript
// root-check 尝试降级具有root权限的进程的权限，如果失败，则阻止访问 const rootCheck = require('root-check') rootCheck()
```
<a name="HW3ex"></a>
#### 3.3.3 指令系
指令系统是ystore/cli的核心，在开发中我们通过对不同指令的编码，最终组织出ystore/cli。可以这么说，指令系统就像人体细胞一样，承担着非常重要的角色。<br />对于指令的开发，首先要对指令进行分类统计，目前正在开发的指令有`create`、`clean`。在对指令的开发中，我们借鉴了Docker的思想，指令之间是隔离的，一个指令内部发生报错，不会影响其他指令的运行。在代码构建中，每个指令独享作用域，然后在`operation.ts`中注册指令，这种开发模式提高了工作效率
```javascript
├── create

├── clean
```

<a name="CtVNR"></a>
#### 3.3.4 命令执行
命令注册通过 commander 包实现，文档请移步[ commander 使用文档 ](https://www.npmjs.com/package/commander), 这里给出官网提供的命令注册栗子，<br />在 action 回调中编写命令执行代码，若当前命令需要提供命令行交互，如我们执行 vue create myProject 后，命令行出现 vue 版本选择等一系列交互流程，可使用 inquirer 库来实现
```javascript
var inquirer = require('inquirer');
inquirer
  .prompt([
    type: 'list',
		name: 'size',
		message: 'What size do you need',
		choices: [
			{value: '0', name: 'Jumbo'},
			{value: '1', name: 'Large'},
			{value: '2', name: 'Standard'},
			{value: '3', name: 'Medium'},
			{value: '4', name: 'Small'},
			{value: '5', name: 'Micro'},
		]
  ])
  .then((answers) => {
    // answers.value 为用户选择的size value
  })
```
<a name="Zmwxv"></a>
### 3.4 生产力革命
ystore/cli不仅是模板代码生成工具，它的内置特性赋予了产品开发强大生命力
<a name="p4eEU"></a>
#### 3.4.1 插槽语法
ystore/cli 借鉴Vue插槽的思想，在模板通过在ask-for-cli.js中配置变量模拟Vue插槽功能的实现<br />如果模板项目中如果存在ask-for-cli.js文件则使用inquirer根据用户输入和选择渲染（consolidate.ejs）变量最终通过metalsmith遍历所有文件做插入修改
```javascript
// 模板仓库根目录ask-for-cli.js
// 需要根据用户填写修改的字段
const requiredPrompts = [
  {
    type: 'input',
    name: 'repoNameEn',
    message: 'please input repo English Name ? (e.g. `smart-case`.focus.cn)',
  },
  {
    type: 'input',
    name: 'repoNameZh',
    message: 'please input repo Chinese Name ?(e.g. `智慧水利`)',
  },
];
// 需要修改字段所在文件
const effectFiles = [
  `README.md`,
  `code/package.json`
  // ...
]
module.exports = {
  requiredPrompts,
  effectFiles,
};
```

```javascript
// 例如在package.json文件中使用
// 最终生成项目名为<智慧水利>
{
  "name": "<%=repoNameEnCamel%>",
  "version": "1.0.0",
  "private": true,
  "description": "<%=repoNameEn%> fe"
  // ...
}
```

在应用创建过程中，ystore/cli会根据ask-for-cli.js中配置通过终端将变量插入模板插槽中<br />![Snipaste_2022-12-30_14-25-50.png](https://cdn.nlark.com/yuque/0/2022/png/1427138/1672381567409-0c986914-4160-4971-8780-f04bb515031a.png#averageHue=%23302d2b&clientId=u1e2df6a0-003a-4&from=ui&height=45&id=u05b90b3d&name=Snipaste_2022-12-30_14-25-50.png&originHeight=38&originWidth=624&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4066&status=done&style=none&taskId=ud1dfb8d6-39db-4618-80ed-31f0ae02811&title=&width=745)<br />渲染后的代码：
```javascript
// 例如在package.json文件中使用
// 最终生成项目名为<智慧水利>
{
  "name": "你好吗",
  "version": "1.0.0",
  "private": true,
  "description": "很好"
  // ...
}
```

我们还能将**变量**使用到项目的其他配置，例如publicPath、base、baseURL...<br />通过以上步骤实现了项目的初始化，项目开发者不必关注各种繁琐的配置，即可愉快的进入业务编码。
<a name="WaBPC"></a>
#### 3.4.2 Metalsmith
在开发一个页面的过程中，你可能需要如下几个步骤

1. 在src/pages/新建NewPage目录，以及index.tsx/index.less/index.d.ts
2. 在src/models/新建NewPage.ts文件，去做状态管理
3. 在src/api/新建NewPage.ts文件，去管理接口调用
4. 在src/routes.ts文件中插入一条NewPage的路由

每次新增页面都需要这么繁琐的操作，我们的脚手架ystore/cli将会实现这个步骤

## 未完待续...






