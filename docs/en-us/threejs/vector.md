## 三维向量Vector3简介
threejs Vector3的文档，很多方法和属性都可以快速理解，比如点乘.dot()、叉乘.cross()，这时候你可以回忆你学过的数学知识来理解theejs的API`Vector3`中的部分方法和属性。

## 向量和标量
向量和标量，简单地说向量就是一个有方向的量，比如在3D空间中描述一个人的速度，你需要表达速度的大小，同时也需要表达速度运动的方向，这样才能计算出3D空间中人经过一段时间，在x、y、z三个方向分别走了多长距离。至于标量，就是一个没有方向的量，比如模型的位置具体坐标mesh.position

## Vector3表示3D空间中位置坐标
`Vector3`对象具有属性.x、.y、.z三个属性，这意味着你可以用Vector3对象表示3D空间中的位置坐标x、y、z。

```js
const v3 = new THREE.Vector3(30,30,0);
console.log('v3',v3);

```
比如已知人在3D空间中的坐标A点是`(30,30,0)`,此人运动到B点，从A到B的位移变化量可以用一个向量Vector3表示，已知AB在x轴上投影长度是100，y方向投影长度是50，这个变化可以用三维向量`THREE.Vector3(100,50,0)`表示,换句话u说，你也可以理解为人沿着x轴走了100，沿着y方向走了50，到达B点。

```js
const A = new THREE.Vector3(30, 30, 0);// 人起点A
// walk表示运动的位移量用向量
const walk = new THREE.Vector3(100, 50, 0);
const B = new THREE.Vector3();// 人运动结束点B
// 计算结束点xyz坐标
B.x = A.x + walk.x;
B.y = A.y + walk.y;
B.z = A.z + walk.z;
console.log('B',B);

```
## 向量加法运算.addVectors()
```js
const A = new THREE.Vector3(30, 30, 0);// 人起点A
// walk表示运动的位移量用向量
const walk = new THREE.Vector3(100, 50, 0);
const B = new THREE.Vector3();// 人运动结束点B
// addVectors的含义就是参数中两个向量xyz三个分量分别相加
B.addVectors(A,walk);
console.log('B',B);
```
## 向量复制方法.copy()
```js
// A.clone()克隆一个和A一样对象，然后再加上walk，作为B
// A不执行.clone()，A和B本质上都指向同一个对象
const B = A.clone().add(walk);
```
## 向量方法.multiplyScalar()
向量方法.multiplyScalar(50)表示向量x、y、z三个分量和参数分别相乘。

v.clone().multiplyScalar(50)的含义和Vector3(v.x * 50, v.y * 50, v.z * 50)是一样的。
```js
// `.multiplyScalar(50)`表示向量x、y、z三个分量和参数分别相乘
const walk = v.clone().multiplyScalar(50);
// 运动50秒结束位置B
const B = A.clone().add(walk);
```

mesh.scale.multiplyScalar(5)

## 四维向量（Vector4）

w - 向量的w值，默认为1


## Scale

一个放大缩小的动画
```js
object.scale.setScalar( Math.cos( time ) * 0.125 + 0.875 );
```


## LockAt
loockAt是一个十分重要的API,不仅可已调整相机的视角，还可以调整mesh的方向，
已知mesh的起点，和另一个点，通过lockAt调整物体的姿态，**这种方法不适用line**
startV.lockAt(endV)