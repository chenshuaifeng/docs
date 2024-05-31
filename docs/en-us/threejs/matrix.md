## 模型矩阵

在图形学中经常会提到模型矩阵的概念，其实模型矩阵就是咱们上节课介绍的平移矩阵、旋转矩阵、缩放矩阵的统称，或者说模型矩阵是平移、缩放、旋转矩阵相乘得到的复合矩阵。

## 几何变换顺序对结果的影响
假设一个顶点原始坐标(2,0,0)。

先平移2、后缩放10：如果先沿着x轴平移2，变为(4,0,0)，再x轴方向缩放10倍，最终坐标是(40,0,0)。

先缩放10、后平移2：如果先x轴方向缩放10倍，变为(20,0,0)，再沿着x轴平移2，最终坐标是(22,0,0)。

你可以发现上面同样的平移和缩放，顺序不同，变换后的顶点坐标也不相同。

## 矩阵表示

假设一个顶点原始坐标(2,0,0),先沿着x轴平移2，变为(4,0,0)，再x轴方向缩放10倍，最终坐标是(40,0,0)。这个过程可以用上节课介绍的矩阵乘法运算去表示。

![angular-question3](../../static/images/先平移后旋转.jpg ':size=800')
模型矩阵：先计算所有几何变换对应矩阵的乘积，得到一个模型矩阵，再对顶点坐标进行变换

## 单位矩阵
![angular-question3](../../static/images/单位矩阵.jpg.jpg ':size=800')

## Three.js矩阵Matrix4
`Three.js`的一个矩阵相关类Matrix4(4x4矩阵)，并用Matrix4创建平移矩阵、旋转矩阵、缩放矩阵。

创建4x4矩阵Matrix4对象
```js
// 创建一个4x4矩阵对象
const mat4 = new THREE.Matrix4()
```

## 属性`.elements`设置平移矩阵
通过4x4矩阵Matrix4的属性.elements设置矩阵的值，比如设置一个平移矩阵。

.elements属性值是一个数组，数组的元素就是4x4矩阵的16个数字，数字在数组中按照矩阵列的顺序，一列一节输入数组中。

```js
// 平移矩阵，沿着x轴平移50
// 1, 0, 0, x,
// 0, 1, 0, y,
// 0, 0, 1, z,
// 0, 0, 0, 1
const mat4 = new THREE.Matrix4()
mat4.elements=[1,0,0,0, 0,1,0,0, 0,0,1,0, 50, 0, 0, 1];

```
## 顶点坐标进行矩阵变换`Vector3.applyMatrix4()`

`.applyMatrix4()`是三维向量Vector3的一个方法，如果Vector3表示一个顶点xyz坐标，Vector3执行.applyMatrix4()方法意味着通过矩阵对顶点坐标进行矩阵变换，比如平移、旋转、缩放。

```js
// 空间中p点坐标
const p = new THREE.Vector3(50,0,0);
// 矩阵对p点坐标进行平移变换
p.applyMatrix4(mat4);
console.log('查看平移后p点坐标',p);
```
## 快速生成平移、旋转、缩放矩阵
- 平移矩阵.makeTranslation(Tx,Ty,Tz)
- 缩放矩阵.makeScale(Sx,Sy,Sz)
- 绕x轴的旋转矩阵.makeRotationX(angleX)
- 绕y轴的旋转矩阵.makeRotationY(angleY)
- 绕z轴的旋转矩阵.makeRotationZ(angleZ)

```js
const mat4 = new THREE.Matrix4();
// 生成平移矩阵(沿着x轴平移50)
mat4.makeTranslation(50,0,0);
// 结果和.elements=[1,0,0,0,...... 50, 0, 0, 1]一样
console.log('查看矩阵的值',mat4.elements);

```
## 矩阵乘法multiply
关于平移矩阵、旋转矩阵和缩放矩阵乘法的含义，前面给大家讲解过，咱们这里再强调一遍。

比如两个矩阵，一个是平移矩阵T、一个是旋转矩阵R，两个矩阵相乘的结果，就表示旋转和平移的复合变换。

下面代码先把旋转矩阵和平移矩阵相乘，然后再对坐标进行变换，你可以看到结果上面代码相同
```js
const T = new THREE.Matrix4();
T.makeTranslation(50,0,0);//平移矩阵
const R = new THREE.Matrix4();
R.makeRotationZ(Math.PI/2);//旋转矩阵

// p点矩阵变换
// p.applyMatrix4(T);//先平移
// p.applyMatrix4(R);//后旋转

// 旋转矩阵和平移矩阵相乘得到一个复合模型矩阵
const modelMatrix = R.clone().multiply(T);
p.applyMatrix4(modelMatrix);

```
##  矩阵乘法顺序
矩阵乘法除特殊情况外，一般不满足交换律，R.clone().multiply(T)和T.clone().multiply(R)表示的结果不同，也就是R * T和T * R计算结果不同