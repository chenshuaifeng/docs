四元数`Quaternion`和欧拉角Euler一样，可以用来计算或表示物体在3D空间中的旋转姿态角度。

Three.js对四元数的数学细节和算法进行了封装，提供了一个四元数相关的类，平时写一些姿态角度的代码，可以使用`Quaternion`辅助。本节课，咱们就结合具体的threejs代码科普这个抽象的四元数概念，有了具体代码辅助，这样更容易使用四元数表示物体的姿态角度。


Quaternion( x : Float, y : Float, z : Float, w : Float ) [-1,1]

实例化`Quaternion`
```js
const quaternion = new THREE.Quaternion();
```

## 四元数方法
`.setFromRotationMatrix ( m : Matrix4 ) : this`
从m的旋转分量中来设置该四元数。

```js
// 运动得相机调整其位置
if ( ! params.lookAhead ) lookAt.copy( position ).add( direction );
				splineCamera.matrix.lookAt( splineCamera.position, lookAt, normal );
				splineCamera.quaternion.setFromRotationMatrix( splineCamera.matrix );
        ```


- `.setFromAxisAngle()`
`.setFromAxisAngle(axis, angle)`生成的四元数表示绕axis旋转，旋转角度是angle。`.setFromAxisAngle()`可以生成一个四元数，绕任意轴，旋转任意角度，并不局限于x、y、z轴。
```js
const quaternion = new THREE.Quaternion();
// 旋转轴new THREE.Vector3(0,0,1)
// 旋转角度Math.PI/2
// 绕z轴旋转90度
quaternion.setFromAxisAngle(new THREE.Vector3(0,0,1),Math.PI/2);
```

- `.multiply( q : Quaternion )`:四元数乘法运算
两个四元数分别表示一个旋转，如果相乘，会得到一个新的四元数，新四元数表示两个旋转的组合旋转。**在原来的基础上进行旋转，物理含义是为某个物体赋予一定得角度**

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
.multiply()与.copy()总结:
A.multiply(B)表示A乘以B，结果赋值给A，在A的基础上旋转B。

A.copy(B)表示用B的值替换A的值，A表示的旋转会被B替换。

- `.setFromUnitVectors(vFrom : Vector3, vTo : Vector3 )`
表示从向量vFrom表示的方向旋转到向量vTo表示的方向,vTo单位向量表示方向

```js
//飞机初始姿态飞行方向a
const vFrom = new THREE.Vector3(0, 0, -1);
// 飞机姿态绕自身坐标原点旋转到b指向的方向
const vTo = new THREE.Vector3(-1, -1, -1).normalize();
// a旋转到b构成的四元数
const quaternion = new THREE.Quaternion();
//注意两个参数的顺序
quaternion.setFromUnitVectors(vFrom, vTo);
// quaternion表示的是变化过程，在原来基础上乘以quaternion即可
fly.quaternion.multiply(quaternion);

```
实例：地球上的光锥
```js
// 
// 球面上的物体方向旋转
// 从a向量(当前方向)旋转b方向
lightPillar.quaternion.setFromUnitVectors(
    new THREE.Vector3(0, 1, 0),
    position.clone().normalize()
);
```

## 球面坐标转换
phi是方位面（水平面）内的角度，范围0~360度
theta是俯仰面（竖直面）内的角度，范围0~180度

1. 仰角和方位角（Elevation and Azimuth）
仰角和方位角描述了物体在天空相对于观察者的位置。

仰角（Elevation）0-90
方位角（Azimuth）
```js
const phi = THREE.MathUtils.degToRad( 90 - parameters.elevation );
const theta = THREE.MathUtils.degToRad( parameters.azimuth );

sun.setFromSphericalCoords( 1, phi, theta );

```

2. 球坐标系位置计算
- `Spherical`：球面坐标计算
- `Matrix.lookAt ( eye : Vector3, target : Vector3, up : Vector3 ) : this `
- `Quaternion.rotateTowards`


示例：webgl_math_orientation_transform
```js
const spherical = new THREE.Spherical();
spherical.theta = Math.random() * Math.PI * 2;
spherical.phi = Math.acos( ( 2 * Math.random() ) - 1 );
spherical.radius = 2;

target.position.setFromSpherical( spherical );

// compute target rotation

rotationMatrix.lookAt( target.position, mesh.position, mesh.up );
targetQuaternion.setFromRotationMatrix( rotationMatrix );

function animate() {
    requestAnimationFrame( animate );
    const delta = clock.getDelta();
    if ( ! mesh.quaternion.equals( targetQuaternion ) ) {
        const step = speed * delta;
        mesh.quaternion.rotateTowards( targetQuaternion,  step);
        // 没有过度动画
				// mesh.quaternion.copy( targetQuaternion );
    }
    renderer.render( scene, camera );
}
```

构造一个旋转矩阵，从eye 指向 target，由向量 up 定向。 

1. 已知起点物体对象mesh。获取mesh.position和mesh.up
2. 已知朝向目前targe。获取了targe.position
3. 构造matrix, t

4. 通过构造的矩阵设置四元数Quaternion

示例步骤：webgl_math_orientation_transform
1. 使用方位角、俯仰构造球面上随机点
2. 赋值给物体位置
3. 通过target, origin构造旋转矩阵
4. 物体对象四元数通过旋转矩阵确定姿态
**确定物体mesh的姿态**

## 经纬度
### 经度：longitude

经线平行轴线
1. 东经正数，西经为负数。
2. 经度分东西，指南北
-180~180
纬度：latitude
纬线垂直于地轴

1. 北纬为正数，南纬为负数。
2. 纬度分南北，指东西
3. -90~90度

```js
let lat = Math.random() * 180 - 90;
let lon = Math.random() * 360 - 180;
const lon2xyz = (R, longitude, latitude) => {
  let lon = (longitude * Math.PI) / 180; // 转弧度值
  const lat = (latitude * Math.PI) / 180; // 转弧度值
  lon = -lon; // js坐标系z坐标轴对应经度-90度，而不是90度

  // 经纬度坐标转球面坐标计算公式
  const x = R * Math.cos(lat) * Math.cos(lon);
  const y = R * Math.sin(lat);
  const z = R * Math.cos(lat) * Math.sin(lon);
  // 返回球面坐标
  return new THREE.Vector3(x, y, z);
};
```
