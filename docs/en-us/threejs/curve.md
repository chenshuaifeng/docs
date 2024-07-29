## curve曲线
总结：
1. 求点、生成Curve
2. 求点、生成shape
3. 使用Curve和Shape结合生成geometry


- `.getPointAt` ( u : Float, optionalTarget : Vector ) : Vector
u - 根据弧长在曲线上的位置。必须在范围[0，1]内。获取[0, 1]方法：%
optionalTarget — (可选) 如果需要, (可选) 如果需要, 结果将复制到此向量中，否则将创建一个新向量。
插值计算的API，通常u为`time`,这个API常用于时间动画


## Curv的使用
使用曲线构造几何体的原理是：`BufferGeometry`:

从曲线中获取顶点坐标的方法：
1. 插值法:getPointAt([0, 1], Vector), 获取到[0,1]区间的向量Vector
2. 分段取点法： getPoints(50),获取的是一个由50个向量组成的点得数组,返回多点
3. 分段去点法2：`.getPoint ( t : Float, optionalTarget : Vector ) : Vector` t - 曲线上的位置。必须在[0,1]范围内 返回

通过bufferGeometry的`setFromPoints`方法，给几何体确定顶点属性

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
通过插值方式画弧度: 已知曲线，已经曲线上的点数

- `.getPoint ( t : Float, optionalTarget : Vector ) : Vector`
t - 曲线上的位置。必须在[0,1]范围内
optionalTarget — (可选) 如果需要, 结果将复制到此向量中，否则将创建一个新向量。
```js
	for ( let i = 0; i < ARC_SEGMENTS; i ++ ) {
        // 通过分段方式求插值  
        const t = i / ( ARC_SEGMENTS - 1 );
        spline.getPoint( t, point );
        position.setXYZ( i, point.x, point.y, point.z );

    }
```

使用`Line`辅助曲线点的位置的确定，帮助完成曲线绘制

```js
	const closedSpline = new THREE.CatmullRomCurve3( [
        new THREE.Vector3( - 60, - 100, 60 ),
        new THREE.Vector3( - 60, 20, 
        60 ),
        new THREE.Vector3( - 60, 120, 60 ),
        new THREE.Vector3( 60, 20, - 60 ),
        new THREE.Vector3( 60, - 100, - 60 )
    ] );
    closedSpline.curveType = 'catmullrom';
    closedSpline.closed = true;

    const points = closedSpline.getPoints( 50 );
    const bufferGeometry = new THREE.BufferGeometry();

    bufferGeometry.setFromPoints( points );
    const material = new THREE.LineBasicMaterial( { color: 0xff0000 } );
    const splineObject = new THREE.Line( bufferGeometry, material );
    scene.add( splineObject );
 ```
> 总结：使用曲线Curve的步骤

1. 勾画曲线
2. 生成平面Shape
3. 按照曲线由Shape拉伸为管道

## 虚线
通过点的方式求虚线
1. 通过点物体绘制虚线
2. 通过line绘制虚线更好看,间隔时短横线

- `.computeLineDistances () : this`
计算LineDashedMaterial所需的距离的值的数组。 对于几何体中的每一个顶点，这个方法计算出了当前点到线的起始点的累积长度。

```js
const line = new THREE.Line( geometrySpline, new THREE.LineDashedMaterial( { color: 0xffffff, dashSize: 1, gapSize: 0.5 } ) );
				line.computeLineDistances();
```

**形状（Shape）**

使用路径以及可选的孔洞来定义一个**二维形状平面**。 它可以和ExtrudeGeometry、ShapeGeometry一起使用，获取点，或者获取三角面。
shape有一些比较重要的方法：

- `.moveTo ( x : Float, y : Float ) : this`
将.currentPoint移动到x, y。

- `.lineTo ( x : Float, y : Float ) : this`
在当前路径上，从.currentPoint连接一条直线到x,y。

- `.setFromPoints ( vector2s : Array ) : this`
points -- Vector2数组。

- `.bezierCurveTo ( cp1X : Float, cp1Y : Float, cp2X : Float, cp2Y : Float, x : Float, y : Float ) : this`
从.currentPoint创建一条贝塞尔曲线，以(cp1X, cp1Y)和(cp2X, cp2Y)作为控制点，并将.currentPoint更新到x,y。

- `tension – 曲线的张力，默认为0.5。`
可以使得线打结

构建shape平面的方式：核心是**构造点**
1. 圆形法：知道半径R,顶点数(分段数)
```js
const pts1 = [], count = 3;

for ( let i = 0; i < count; i ++ ) {

    const l = 20;

    const a = 2 * i / count * Math.PI;

    pts1.push( new THREE.Vector2( Math.cos( a ) * l, Math.sin( a ) * l ) );

}
```
```js
for ( let i = 0; i < hilbertPoints.length * subdivisions; i ++ ) {

        const t = i / ( hilbertPoints.length * subdivisions );
        spline.getPoint( t, point );

        vertices.push( point.x, point.y, point.z );

        color.setHSL( 0.6, 1.0, Math.max( 0, - point.x / 200 ) + 0.5, THREE.SRGBColorSpace );
        colors1.push( color.r, color.g, color.b );

        color.setHSL( 0.9, 1.0, Math.max( 0, - point.y / 200 ) + 0.5, THREE.SRGBColorSpace );
        colors2.push( color.r, color.g, color.b );

        color.setHSL( i / ( hilbertPoints.length * subdivisions ), 1.0, 0.5, THREE.SRGBColorSpace );
        colors3.push( color.r, color.g, color.b );

    }
```

插值法
**`const t = i / ( ARC_SEGMENTS - 1 );`**

const shape1 = new THREE.Shape( pts1 );
2. lineTo, moveTO
3. setFromPoints
4. 贝塞尔曲线法

点的数量splinePointsLength

案例：webgl_geometry_spline_editor
1. 创建四个端点mesh --> 立方体内随机分布 ---> 全局保存对象 ---> 将mesh的坐标保存到position中

2. 创建CatmullRomCurve3
保存曲线的全局对象 -->通过position生成曲线(没有创建Line) ---> 创建buffergeometry --> 申请200个点的buffer空间 --> 创建Line ---> 添加到场景对象中 ---> loader曲线上四个端点 ---> 用给定的坐标替换postition值，确当端点位置 ---> 通过求插值方式给position赋值 ---> 更新

> tranformControl 改变坐标线条的样式

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


## 矩阵跟随
简单方法：
在动画函中通过插值方式获取的Curve的点，然后将点赋值给Mesh

复杂方法：
使用矩阵跟随

```js
import { Flow } from 'three/addons/modifiers/CurveModifier.js';
```
`CurveModifier`这个模块有很多curve相关的方法
初始化FLow
1. Flow
实例化Flow，传入要跟随mesh
2. `flow.updateCurve( 0, curve );`
更新Curve,传入入索引，及被跟随curve

事件触发：
在动画函数中让物体沿着曲线运动`flow.moveAlongCurve( 0.002 );`

副作用：
animate\raycast\transform
粗略的流程：在动画函数中，捕获物体，并将捕获的物体加入transformControle中，作用是：控制器的position赋值给Mesh；transformControle拖拽结束事件中，获取到点的坐标重新更新线条
```js
	control.addEventListener( 'dragging-changed', function ( event ) {

        if ( ! event.value ) {

            const points = curve.getPoints( 50 );
            line.geometry.setFromPoints( points );
            flow.updateCurve( 0, curve );

        }

    } );
```

## 矩阵跟随多实例


