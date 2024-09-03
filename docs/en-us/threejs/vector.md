## API

 - `.transformDirection ( m : Matrix4 ) : this`
通过传入的矩阵（m的左上角3 x 3子矩阵）变换向量的方向， 并将结果进行normalizes（归一化）。
用例： 在射线捕获中，获取到平面的法相量,对向量进行归一化处理
1. 为向量应用position\scale\rotation  
2. 进行归一化处理

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

- `.set ( x : Float, y : Float, z : Float ) : this`  
设置该向量的x、y 和 z 分量。

```js
// 实例：webgl_interactive_buffergeometry
// 叉乘求法向量
pA.set( ax, ay, az );
pB.set( bx, by, bz );
pC.set( cx, cy, cz );
cb.subVectors( pC, pB );
ab.subVectors( pA, pB );
cb.cross( ab );
cb.normalize();
```

- `.toArray`
返回一个数组[x, y ,z]，或者将x、y和z复制到所传入的array中。  
*即获position的方法*

```js
color.setHSL( 0.01 + 0.1 * ( i / l ), 1.0, 0.5 );
color.toArray( colors, i * 3 );
```

**向量和标量**
- `.multiplyScalar ( s : Float ) `: this    
向量乘以标量
向量与标量相乘的物理意义，向量沿（x,y,z）方向移动了s距离,表示位移


- `.add ( v : Vector4 )` : this  
将传入的向量v和这个向量相加。
向量与向量相加的物理意义，向量沿（x,y,z）方向移动了x,y,z距离，表示位置


```js
// `.multiplyScalar(50)`表示向量x、y、z三个分量和参数分别相乘
const walk = v.clone().multiplyScalar(50);
// 运动50秒结束位置B
const B = A.clone().add(walk);
```


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

## 速度、时间、位移
算法1
```js
const v = new THREE.Vector3(10, 0, 10);//物体运动速度
const clock = new THREE.Clock();//时钟对象
let t = 0;
const pos0 = mesh.position.clone();//物体初始位置
// 渲染循环
function render() {
    const spt = clock.getDelta();//两帧渲染时间间隔(秒)
    t += spt;
    // 在t时间内，以速度v运动的位移量
    const dis = v.clone().multiplyScalar(t);
    // 网格模型初始位置加上t时间段内运动的位移量
    const newPos = pos0.clone().add(dis);
    mesh.position.copy(newPos);
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();
```

算法2
```js
const v = new THREE.Vector3(10,0,10);//物体运动速度
const clock = new THREE.Clock();//时钟对象
// 渲染循环
function render() {
    const spt = clock.getDelta();//两帧渲染时间间隔(秒)
    // 在spt时间内，以速度v运动的位移量
    const dis = v.clone().multiplyScalar(spt);
    // 网格模型当前的位置加上spt时间段内运动的位移量
    mesh.position.add(dis);
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();

```

## 物理加速度位移公式`x = vt + 1/2gt^2`计算位置
```js
const v = new THREE.Vector3(30, 0, 0);//物体运动速度
const clock = new THREE.Clock();//时钟对象
let t = 0;
const g = new THREE.Vector3(0, -9.8, 0);
const pos0 = mesh.position.clone();
// 渲染循环
function render() {
    const spt = clock.getDelta();//两帧渲染时间间隔(秒)
    t += spt;
    // 在t时间内，以速度v运动的位移量
    const dis = v.clone().multiplyScalar(t).add(g.clone().multiplyScalar(0.5 * t * t));
    // 网格模型当前的位置加上spt时间段内运动的位移量
    const newPos = pos0.clone().add(dis);
    mesh.position.copy(newPos);
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();
```
简写方法2

```js
const v = new THREE.Vector3(30, 0, 0);//物体初始速度
const clock = new THREE.Clock();//时钟对象
const g = new THREE.Vector3(0, -9.8, 0);
// 渲染循环
function render() {
    if (mesh.position.y > 0) {
        const spt = clock.getDelta();//两帧渲染时间间隔(秒)
        //spV:重力加速度在时间spt内对速度的改变
        const spV = g.clone().multiplyScalar(spt);
        v.add(spV);//v = v + spV  更新当前速度
        // 在spt时间内，以速度v运动的位移量
        const dis = v.clone().multiplyScalar(spt);
        // 网格模型当前的位置加上spt时间段内运动的位移量
        mesh.position.add(dis);
    }
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();
```


## 四维向量（Vector4）

w - 向量的w值，默认为1

## LockAt
loockAt是一个十分重要的API,不仅可已调整相机的视角，还可以调整mesh的方向，
已知mesh的起点，和另一个点，通过lockAt调整物体的姿态，**这种方法不适用line**
startV.lockAt(endV)


沿当前方向从中心向外移动至少5个单位
```js
x = Math.random() * 100 - 50;
y = Math.random() * 100 - 50;
z = Math.random() * 100 - 50;

offset.set( x, y, z ).normalize();
// 沿着offset方向平移5个单位
offset.multiplyScalar( 5 ); // move out at least 5 units from center in current direction
offset.set( x + offset.x, y + offset.y, z + offset.z );
```