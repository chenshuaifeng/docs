## API
- `.Position(x, yz )`
给当前矩阵设置位置
- `.compose ( position : Vector3, quaternion : Quaternion, scale : Vector3 ) : this`
设置将该对象位置 position，四元数quaternion 和 缩放scale 组合变换的矩阵。将位置、旋转、缩放属性赋值给矩阵
```js
    const position = new THREE.Vector3();
    const rotation = new THREE.Euler();
    const quaternion = new THREE.Quaternion();
    const scale = new THREE.Vector3();
            
    position.x = Math.random() * 40 - 20;
    position.y = Math.random() * 40 - 20;
    position.z = Math.random() * 40 - 20;

    rotation.x = Math.random() * 2 * Math.PI;
    rotation.y = Math.random() * 2 * Math.PI;
    rotation.z = Math.random() * 2 * Math.PI;

    quaternion.setFromEuler( rotation );

    scale.x = scale.y = scale.z = Math.random() * 1;

    matrix.compose( position, quaternion, scale );
```
物体的角度姿态属性设置：欧拉角 -> 四元数


## 本地矩阵`.matrix`、世界矩阵`.matrixWorld`

模型本地矩阵属性.matrix的属性值是一个4x4矩阵Matrix4

由position位置转为matrix矩阵数据进行赋值
1. position随机生成坐标
2. 更新mesh得matrix
3. setMatrixAt(i, matrix)
```js

    dummy.position.set( offset - 300 * x + 200 * Math.random(), 0, offset - 300 * y );

    dummy.updateMatrix();

    mesh.setMatrixAt( i, dummy.matrix );
```


改变three.js模型对象的.position、.scale或.rotation(.quaternion)任何一个属性，查看查看mesh.matrix值的变化。

1.仅改变mesh.position,你会发现mesh.matrix的值会从单位矩阵变成一个平移矩阵(沿着xyz轴分别平移2、3、


```js
// 不执行renderer.render(scene, camera);情况下测试
mesh.position.set(2,3,4);
mesh.updateMatrix();//更新矩阵，.matrix会变化
console.log('本地矩阵',mesh.matrix);

```
2.仅改变mesh.scale,你会发现mesh.matrix的值会从单位矩阵变成一个缩放矩阵(沿着xyz轴三个方向分别缩放6、6、6倍);

```js
mesh.scale.set(6,6,6);
mesh.updateMatrix();
console.log('本地矩阵',mesh.matrix);

```
3.同时平移和缩放，查看.matrix变化，你可以看到mesh.matrix是平移矩阵和缩放矩阵的复合矩阵。
```js
mesh.position.set(2,3,4);
mesh.scale.set(6,6,6);
mesh.updateMatrix();
console.log('本地矩阵',mesh.matrix);
```



## 代码示例
通过矩阵进行旋转
矩阵表示物体在三维空间的姿态，在更新后的基础上再次应用
```js
const rotateY = new THREE.Matrix4().makeRotationY( 0.005 );
....

camera.applyMatrix4( rotateY );
camera.updateMatrixWorld();

```
给矩阵应用四元数
tmpM.makeRotationFromQuaternion( tmpQ );