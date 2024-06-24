## curve曲线

- `.getPointAt` ( u : Float, optionalTarget : Vector ) : Vector
u - 根据弧长在曲线上的位置。必须在范围[0，1]内。获取[0, 1]方法：%
optionalTarget — (可选) 如果需要, (可选) 如果需要, 结果将复制到此向量中，否则将创建一个新向量。
一插值计算的API，通常u为`time`,这个API常用于时间动画


> 获取曲线分段，将弧度转换成顶点数据。 `R*cons`

```js
const geometry = new THREE.BufferGeometry(); //创建一个几何体对象
const R = 100; //圆弧半径
const N = 50; //分段数量
const sp = 2 * Math.PI / N;//两个相邻点间隔弧度
// 批量生成圆弧上的顶点数据
const arr = [];
for (let i = 0; i < N; i++) {
    const angle =  sp * i;//当前点弧度
    // 以坐标原点为中心，在XOY平面上生成圆弧上的顶点数据
    const x = R * Math.cos(angle);
    const y = R * Math.sin(angle);
    arr.push(x, y, 0);
}
//类型数组创建顶点数据
const vertices = new Float32Array(arr);
// 创建属性缓冲区对象
//3个为一组，表示一个顶点的xyz坐标
const attribue = new THREE.BufferAttribute(vertices, 3); 
// 设置几何体attributes属性的位置属性
geometry.attributes.position = attribue;

// 线材质
const material = new THREE.LineBasicMaterial({
    color: 0xff0000 //线条颜色
});
// 创建线模型对象   构造函数：Line、LineLoop、LineSegments
// const line = new THREE.Line(geometry, material); 
const line = new THREE.LineLoop(geometry, material);//线条模型对象

```

>  threejs中SplineCurve和QuadraticBezierCurve有什么区别


在three.js库中，SplineCurve和QuadraticBezierCurve都是用于创建曲线的工具，但它们之间存在明显的区别。以下是两者的主要区别：

**曲线类型与算法：**
QuadraticBezierCurve：这是一个二维的二阶贝塞尔曲线。贝塞尔曲线是由起点、终点和控制点定义的，它们决定了曲线的形状。二阶贝塞尔曲线具有两个控制点，但QuadraticBezierCurve只涉及一个控制点（p2），因为曲线的起点（p1）和终点（p3）也作为参数传入。
SplineCurve：这是一个二维的样条曲线。样条曲线使用特定的算法（如Catmull-Rom算法）从一系列的点创建一条平滑的曲线。在three.js中，SplineCurve主要用于在二维空间中创建曲线。
**参数与输入：**
QuadraticBezierCurve：需要三个参数，都是THREE.Vector2类型的二维向量，分别代表起点（p1）、控制点（p2）和终点（p3）。
SplineCurve：需要一个由THREE.Vector2或THREE.Vector3类型向量构成的数组作为参数，这些向量定义了曲线上的点。
**用途与特性：**
QuadraticBezierCurve：适用于那些需要精确控制曲线形状，尤其是通过单个控制点来影响曲线形状的情况。
SplineCurve：更适用于需要通过一系列点来创建平滑曲线的场景，例如，在三维建模中模拟自然形状或复杂路径。
**实现细节：**
QuadraticBezierCurve：使用贝塞尔曲线的数学公式来计算曲线上的点。这些点随后用于渲染曲线。
SplineCurve：使用样条曲线的算法（如Catmull-Rom算法）来计算通过给定点的平滑曲线。同样，这些计算出的点用于渲染曲线。
可视化与交互：
在使用两者时，都可以通过调整参数或点集来实时观察曲线形状的变化，从而进行可视化和交互设计。
***总结来说，QuadraticBezierCurve和SplineCurve在three.js中都是用于创建曲线的工具，但它们在曲线类型、参数需求、用途和实现细节上有所不同。选择哪种曲线取决于具体的应用场景和需求。***