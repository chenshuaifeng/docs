WebGL 空间中的点和多边形的个体转化由基本的转换矩阵（例如平移，缩放和旋转）处理

## 模型矩阵ModelMatrix

- 作用：确定物体在世界空间中的位置、方向和大小。
- 定义：模型矩阵（Model Matrix）是用于将物体的顶点坐标从局部坐标系（Local Space）变换到世界坐标系（World Space）的矩阵。在3D图形学中，每个物体都有自己的局部坐标系，该坐标系以物体的某个点（通常是中心点）为原点，物体的顶点坐标都是相对于这个原点来定义的。然而，在渲染场景中，我们需要将所有物体的坐标统一到同一个全局坐标系中，即世界坐标系，以便进行后续的视图变换和投影变换。模型矩阵正是实现这一变换的关键。 
- 作用：确定物体在世界空间中的位置、方向和大小。

在图形学中经常会提到模型矩阵的概念，其实模型矩阵就是平移矩阵、旋转矩阵、缩放矩阵的统称，或者说模型矩阵是平移、缩放、旋转矩阵相乘得到的复合矩阵。
在Three.js中，模型矩阵与顶点坐标的乘法是物体从模型空间变换到世界空间的关键步骤。它负责将物体的顶点从模型空间（或称为对象空间）变换到世界空间。

## 视图矩阵 ViewMatrix

- 定义：视图矩阵是一个4x4的矩阵，它定义了如何将世界坐标系中的点转换到观察者（或摄像机）坐标系中。这个转换过程取决于观察者的位置、观察方向和上向量（即观察者头顶的方向）。视图矩阵允许我们模拟摄像机的位置、朝向和倾斜，从而影响我们在场景中看到的物体。
- 作用：反映观察者的位置和朝向，以及如何将世界空间中的物体映射到观察者的视野中。

视图矩阵是一种变换矩阵，它模拟了真实世界中的摄像机，将世界场景中的物体从世界坐标系转换到摄像机坐标系下，这一转换过程使得开发者可以根据摄像机的位置、朝向和目标点来调整观察场景的角度和视野，从而改变场景中物体在屏幕上的呈现方式。视图矩阵负责移动场景中的对象以模拟相机位置的变化，改变观察者当前能够看到的内容。

### 模型视图矩阵 MoelViewMatrix
也叫MvMatrix，

- 定义：模型矩阵与视图矩阵的乘积，即模型视图矩阵=模型矩阵×视图矩阵。
- 作用：将物体从模型空间直接转换到观察者空间，无需经过中间的世界空间转换。这使得在渲染过程中可以更方便地处理物体的位置和朝向。
```js
void main() {
    vColor = color;
    vec4 mvPosition = modelViewMatrix * vec4( position, 1.0 );
    gl_PointSize = size * ( 300.0 / -mvPosition.z );
    gl_Position = projectionMatrix * mvPosition;
}
```

## 投影矩阵ProjectionMatrix
- 定义：投影矩阵主要用于模拟摄像机将3D场景投影到2D屏幕上的过程。

投影矩阵用于将世界空间坐标转换为剪裁空间坐标。常用的投影矩阵（透视矩阵）用于模拟充当 3D 虚拟世界中观看者的替身的典型相机的效果



**gl_Position = ProjectionMatrix * ModelViewMatrix * VertexPosition**, 投影矩阵 * 模型视图矩阵 * 顶点坐标 生成webGL中物体顶点坐标

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


- threejs中矩阵的应用
```js
function setObjectWorldMatrix( object, matrix ) {

    // set the orientation of an object based on a world matrix

    const parent = object.parent;
    scene.updateMatrixWorld();
    object.matrix.copy( parent.matrixWorld ).invert();
    object.applyMatrix4( matrix );

}
```
在 three.js 中，Object3D（通常是场景中的任何对象，如网格、组等）具有一些用于变换和位置属性的方法。当你看到像 object.matrix.copy(parent.matrixWorld).invert(); 和 object.applyMatrix4(matrix); 这样的代码时，以下是对这些操作的解释：

object.matrix.copy(parent.matrixWorld);
这行代码将 parent 的世界矩阵（matrixWorld）复制到 object 的本地矩阵（matrix）中。
本地矩阵（matrix）定义了对象在其父级坐标系中的位置和旋转。
世界矩阵（matrixWorld）则定义了对象在全局坐标系（即场景坐标系）中的位置和旋转。
通常情况下，你不需要直接操作 matrix 或 matrixWorld，除非你有特定的需求或了解它们如何影响渲染。
.invert();
这是一个矩阵方法，用于计算并返回当前矩阵的逆矩阵。
逆矩阵有一个特性，即当它与原矩阵相乘时，结果是一个单位矩阵（对角线为1，其余为0的矩阵）。
在三维图形中，逆矩阵经常用于将变换从一个空间“反转”回另一个空间。例如，如果你有一个从世界空间到对象空间的变换，你可以使用其逆矩阵来从对象空间转换回世界空间。
object.applyMatrix4(matrix);
这行代码将 matrix 应用到 object 上。
applyMatrix4 方法会将给定的4x4矩阵（在本例中是 matrix）与对象的当前变换相乘，从而更新对象的位置、旋转和缩放。
这通常用于将复杂的变换（如从一个动画系统或物理模拟中获得的变换）应用到对象上。
现在，让我们结合这些代码片段来看它们可能的作用：

javascript
object.matrix.copy(parent.matrixWorld).invert();  
object.applyMatrix4(matrix);
首先，object 的本地矩阵被设置为 parent 的世界矩阵的逆矩阵。这通常意味着你正在尝试从 parent 的世界空间转换回 object 的某种“原始”或“局部”空间。
然后，你将另一个矩阵 matrix 应用到 object 上。由于 object 的当前矩阵（在 applyMatrix4 调用之前）是 parent.matrixWorld 的逆矩阵，因此这个 matrix 实际上是在与 parent.matrixWorld 的逆矩阵相乘。这可能用于将 object 从其“原始”或“局部”空间转换到一个新的空间，该空间与 matrix 相关联。
注意：直接操作矩阵可能会导致意外的结果，除非你确切知道自己在做什么。在大多数情况下，使用 position、rotation 和 scale 属性来变换对象会更简单、更直观。
