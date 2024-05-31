欧拉角`Euler`是用来表述物体空间姿态角度的一种数学工具，`Three.js`也提供了相关的类Euler

## 创建一个欧拉角表示特定旋转角度
```js
//创建一个欧拉角对象，表示绕x轴旋转60度
const Euler = new THREE.Euler();
Euler.x = Math.PI / 3;

Euler.y = Math.PI / 3;//绕y轴旋转60度

Euler.z = Math.PI / 3;//绕z轴旋转60度

```

## 欧拉角改变物体姿态角度(.rotation属性)
threejs模型对象都有一个角度属性`.rotation`，`.rotation`的值其实就是欧拉角对象Euler。你可以改变.rotation对应欧拉角x、y或z属性值，查看物体姿态角度变化

```js
// 物体fly绕x轴旋转60度
fly.rotation.x = Math.PI / 3;

const Euler = new THREE.Euler();
Euler.x = Math.PI / 3;
// 复制欧拉角的值，赋值给物体的.rotation属性
fly.rotation.copy(Euler);

```

## 物体旋转顺序`.order`

物体先后绕x、y、z轴旋转，旋转的顺序不同，物体的姿态角度也可能不同。

欧拉角对象的.order属性是用来定义旋转顺序的，也就是说你同时设置欧拉对象的x、y、z三个属性，在旋转的时候，先绕哪个轴，后绕那个轴旋转。

下面两段代码，欧拉角xyz属性是一样的，区别是.order表示的旋转顺序不同，你可以对比不同旋转顺序，物体旋转后姿态角度是否相同。

```js
const Euler = new THREE.Euler();
Euler.x = Math.PI / 3;
Euler.y = Math.PI / 3;
//先绕X轴旋转，在绕Y、Z轴旋转
Euler.order = 'XYZ';
fly.rotation.copy(Euler);
```