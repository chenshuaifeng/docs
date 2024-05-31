## 模型本地矩阵、世界矩阵

## 本地矩阵`.matrix`、世界矩阵`.matrixWorld`

模型本地矩阵属性.matrix的属性值是一个4x4矩阵Matrix4

```js
console.log('mesh.matrix',mesh.matrix);
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