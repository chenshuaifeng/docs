## 第一个flow
将控件区内的输入节点“inject”与输出节点“debug”，使用鼠标左键拖入工作区内。

　　拖入以后发现“inject”变成了“时间戳”，“debug”变成了“msg.payload”，“inject”或是“debug”。拖到工作区以后，它就是“某个”节点，具体到某个节点，当然就是有名字的。前者是抽象的，后者是具体的。为了方便表述，前者可以称之为控件，后者可以称之为节点。如果有面向对象的编程经验，可以很轻松的理解，“inject”与 “时间戳”的关系，其实很像类与对象的关系。


双击“时间戳”，在屏幕的右侧会弹出如下窗口

　　点击“内容”选项后边的小三角，在下拉菜单选择文字列，并在输入框内输入“hello world”，然后点击完成。

　　可以观察到，工作区中的“时间戳”变成了“hello world”。


### 连接输入与输出节点

　Node-red总是默认数据从左流向右，所以输入节点都有一个特点：数据接口在右侧，见下图标记1；输出节点也有一个特点，数据的接口在左侧，见2；还有一些节点是特殊的，既有输入又有输出，那么左右两侧都有数据的接口。

### 部署

节点的右上角有一个蓝色的圆点，这个圆点的意思是，此节点还没有部署和保存。
点击部署按钮进行部署

nject”节点可以手动输入消息，节点左侧有一个小按钮，点击按钮可以手动注入消息，见按钮1。在点击inject节点的按钮之前，必须确保debug节点是可用的，即按钮必须是“伸出来”的，如按钮2，而不是像按钮3一样“缩回去”，按钮“缩回去”的debug节点不工作。点击按钮可以切换节点是否工作。

## 多条消息

```js
var msg1 = { payload:"first out of output 1" };
var msg2 = { payload:"second out of output 1" };
var msg3 = { payload:"third out of output 1" };
var msg4 = { payload:"only message from output 2" };
return [ [ msg1, msg2, msg3 ], msg4 ];

setup=2
```

### 异步消息
```js
setTimeout(function () {
    node.send(msg);
}, 2*1000);
```

### 记录事件

```js
// In function node, node.log, node.warn, and node.error functions can be used for logging
// See debug sidebar and console output
node.log("Something happened");
node.warn("Something happened you should know about");
node.error("Oh no, something bad happened");
return msg;
```

`catch`节点捕获错误

```js
node.error("Something happened you should know about", msg);
```