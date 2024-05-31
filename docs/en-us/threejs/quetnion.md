四元数`Quaternion`和欧拉角Euler一样，可以用来计算或表示物体在3D空间中的旋转姿态角度。

Three.js对四元数的数学细节和算法进行了封装，提供了一个四元数相关的类，平时写一些姿态角度的代码，可以使用`Quaternion`辅助。本节课，咱们就结合具体的threejs代码科普这个抽象的四元数概念，有了具体代码辅助，这样更容易使用四元数表示物体的姿态角度。

实例化`Quaternion`
```js
const quaternion = new THREE.Quaternion();
```

## 四元数方法`.setFromAxisAngle()`

`.setFromAxisAngle()`是四元数的一个方法，可以用来辅助生成表示特定旋转的四元数。

`.setFromAxisAngle(axis, angle)`生成的四元数表示绕axis旋转，旋转角度是angle。

`.setFromAxisAngle()`可以生成一个四元数，绕任意轴，旋转任意角度，并不局限于x、y、z轴。

```js
const quaternion = new THREE.Quaternion();
// 旋转轴new THREE.Vector3(0,0,1)
// 旋转角度Math.PI/2
// 绕z轴旋转90度
quaternion.setFromAxisAngle(new THREE.Vector3(0,0,1),Math.PI/2);
```

## 需要旋转的A点坐标
```js
// A表示3D空间一个点的位置坐标
const A = new THREE.Vector3(30, 0, 0);

```
为了方便观察，可以把旋转A点的位置用一个小球Mesh可视化表示出来
```js
// 黄色小球可视化坐标点A 
const Amesh = createSphereMesh(0xffff00,2);
Amesh.position.copy(A);
group.add(Amesh);
// 创建小球mesh
function createSphereMesh(color,R) {
    const geometry = new THREE.SphereGeometry(R);
    const material = new THREE.MeshLambertMaterial({
        color: color,
    });
    const mesh = new THREE.Mesh(geometry, material);
    return mesh;
}
```
## 四元数乘法运算`.multiply()`
两个四元数分别表示一个旋转，如果相乘，会得到一个新的四元数，新四元数表示两个旋转的组合旋转。

```js
// 在物体原来姿态基础上，进行旋转
const q1 = new THREE.Quaternion();
q1.setFromAxisAngle(new THREE.Vector3(1, 0, 0), Math.PI / 2);
fly.quaternion.multiply(q1);
// 在物体上次旋转基础上，进行旋转
const q2 = new THREE.Quaternion();
q2.setFromAxisAngle(new THREE.Vector3(0, 1, 0), Math.PI / 2);
fly.quaternion.multiply(q2);
// 在物体上次旋转基础上，进行旋转
const q3 = new THREE.Quaternion();
q3.setFromAxisAngle(new THREE.Vector3(0, 0, 1), Math.PI / 2);
fly.quaternion.multiply(q3);
```

```js
// 先变换q2,后变换q1，和上面代码效果不一样，
// q2.clone().multiply(q1)与q1.clone().multiply(q2)表示的旋转过程顺序不同
const q1 = new THREE.Quaternion();
q1.setFromAxisAngle(new THREE.Vector3(1, 0, 0), Math.PI / 2);
const q2 = new THREE.Quaternion();
q2.setFromAxisAngle(new THREE.Vector3(0, 1, 0), Math.PI / 2);
const newQ= q2.clone().multiply(q1);
fly.quaternion.multiply(newQ);
```

## .multiply()与.copy()总结
A.multiply(B)表示A乘以B，结果赋值给A，在A的基础上旋转B。

A.copy(B)表示用B的值替换A的值，A表示的旋转会被B替换。

可以先通过欧拉角改变物体的姿态，先物体一个初始的角度状态。

```js
//改变物体欧拉角，四元数属性也会同步改变
fly.rotation.x = Math.PI/2;
```
