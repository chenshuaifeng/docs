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

## LockAt
loockAt是一个十分重要的API,不仅可已调整相机的视角，还可以调整mesh的方向，
已知mesh的起点，和另一个点，通过lockAt调整物体的姿态，**这种方法不适用line**
startV.lockAt(endV)

## 光线捕获Raycaste

使用光线捕获技术，给物体平面添加直线

1. 捕获物体`intersectObjects`

point (Vector3): 射线与物体的交点。
object (Object3D): 与射线相交的物体。
face (Face3 或 Face4): 如果射线与物体的几何体相交，则此属性表示相交的面的索引。Face3代表三角形面，Face4代表四边形面。
faceIndex (Integer): 相交面的索引。
uv (Vector2 或 Vector3): 交点在面上的纹理坐标。对于UV映射，这将是一个Vector2；对于立方体纹理或其他3D纹理映射，它可能是一个Vector3。
distance (Float): 从射线原点到交点的距离

2. 求两个点坐标
```js
	const p = intersects[ 0 ].point;
    mouseHelper.position.copy( p );

    const n = intersects[ 0 ].face.normal.clone();
    // 改变向量的方向
    n.transformDirection( mesh.matrixWorld );
    n.multiplyScalar( 10 );
    n.add( intersects[ 0 ].point );

    mouseHelper.lookAt( n );

    const positions = line.geometry.attributes.position;
    positions.setXYZ( 0, p.x, p.y, p.z );
    positions.setXYZ( 1, n.x, n.y, n.z );
    positions.needsUpdate = true;
 ```




## 属性方法

 - `.transformDirection ( m : Matrix4 ) : this`
通过传入的矩阵（m的左上角3 x 3子矩阵）变换向量的方向， 并将结果进行normalizes（归一化）。
用例： 在射线捕获中，获取到平面的法相量,对向量进行归一化处理
```js
// 获得平面的法向量
const n = intersects[ 0 ].face.normal.clone();
// 按照物体朝向进行方向变换
n.transformDirection( mesh.matrixWorld );
n.multiplyScalar( 10 );
// n点的坐标 = point坐标+n的坐标
n.add( intersects[ 0 ].point );

const positions = line.geometry.attributes.position;

positions.setXYZ( 0, p.x, p.y, p.z );
positions.setXYZ( 1, n.x, n.y, n.z );
positions.needsUpdate = true;
```

