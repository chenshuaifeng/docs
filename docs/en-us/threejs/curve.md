`Curve`所有曲线的基类  

## API
- `.getPointAt` ( u : Float, optionalTarget : Vector ) : Vector  
u - 根据弧长在曲线上的位置。必须在范围[0，1]内。获取[0, 1]方法：%
optionalTarget — (可选) 如果需要, (可选) 如果需要, 结果将复制到此向量中，否则将创建一个新向量。

```js
time *= 0.001
// 坦克移动插值
const tankTime = time * 0.1
// 小于1的值[0, 1]
// console.log(tankTime % 1)
// 从路径中取出[0, 1]的点的位置
curve.getPointAt(tankTime % 1, tankPosition)
// 坦克朝向
curve.getPointAt((tankTime + 0.01) % 1, tankTarget)
```

- `.getPoints ( divisions : Integer )` : Array
divisions -- 要将曲线划分为的分段数。默认是 5.
返回Vector2组成的数组

```js
	for ( let i = 0; i < ARC_SEGMENTS; i ++ ) {
        // 通过分段方式求插值  
        const t = i / ( ARC_SEGMENTS - 1 );
        spline.getPoint( t, point );
        position.setXYZ( i, point.x, point.y, point.z );

    }
```


## Shape

使用路径以及可选的孔洞来定义一个二维形状平面，**一组由vector2(x,y)组成的向量**。 它可以和`ExtrudeGeometry`、`ShapeGeometry`一起使用，获取点，或者获取三角面  

集成了Curve/Path的方法
- `.moveTo`
- `.bezierCurveTo`
- `.lineTo`
- `.moveTo`

> 用法：使用Shape按照Curve曲线挤压成ExtrudeGeometry
```js
// 知道半径R,顶点数(分段数)
const extrudeSettings1 = {
    steps: 100,
    bevelEnabled: false,
    extrudePath: closedSpline
};
const pts1 = [], count = 3;
for ( let i = 0; i < count; i ++ ) {
    const l = 20;
    const a = 2 * i / count * Math.PI;
    pts1.push( new THREE.Vector2( Math.cos( a ) * l, Math.sin( a ) * l ) );
}
const shape1 = new THREE.Shape( pts1 );
const geometry1 = new THREE.ExtrudeGeometry( shape1, extrudeSettings1 );
```
> 用法2：使用Shape生成曲线

```js
shape.autoClose = true;

const points = shape.getPoints();
const geometryPoints = new THREE.BufferGeometry().setFromPoints( points );
let line = new THREE.Line( geometryPoints, new THREE.LineBasicMaterial( { color: color } ) );

const spacedPoints = shape.getSpacedPoints( 50 );
const geometrySpacedPoints = new THREE.BufferGeometry().setFromPoints( spacedPoints );
line = new THREE.Line( geometrySpacedPoints, new THREE.LineBasicMaterial( { color: color } ) );
```

> 用法三：使用Shape生成文字  

```js
	const message = '   Three.js\nSimple text.';
    const shapes = font.generateShapes( message, 100 );
    const geometry = new THREE.ShapeGeometry( shapes );
    geometry.computeBoundingBox();
    const xMid = - 0.5 * ( geometry.boundingBox.max.x - geometry.boundingBox.min.x );
    geometry.translate( xMid, 0, 0 );
    // make shape ( N.B. edge view not visible )
    const text = new THREE.Mesh( geometry, matLite );
    text.position.z = - 150;
    scene.add( text );
    // make line shape ( N.B. edge view remains visible )
    const holeShapes = [];
    for ( let i = 0; i < shapes.length; i ++ ) {
        const shape = shapes[ i ];
        if ( shape.holes && shape.holes.length > 0 ) {
            for ( let j = 0; j < shape.holes.length; j ++ ) {
                const hole = shape.holes[ j ];
                holeShapes.push( hole );
            }
        }
    }
    shapes.push.apply( shapes, holeShapes );
```
文字对象的`generateShapes`方法


### 总结：
1. 求点、生成Curve
2. 求点、生成shape
3. 使用Curve和Shape结合生成geometry
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



插值法
**`const t = i / ( ARC_SEGMENTS - 1 );`**

const shape1 = new THREE.Shape( pts1 );
2. lineTo, moveTO
3. setFromPoints
4. 贝塞尔曲线法

点的数量splinePointsLength

案例：webgl_geometry_spline_editor
1. 创建四个端点mesh --> 立方体内随机分布 ---> 全局保存对象 ---> 将mesh的坐标保存到position中
**这一步目的是生成曲线上的四个点的坐标**

2. 创建CatmullRomCurve3
保存曲线的全局对象 -->通过position生成曲线(没有创建Line) ---> 创建buffergeometry --> 申请200个点的buffer空间 --> 创建Line ---> 添加到场景对象中 ---> loader曲线上四个端点 ---> 用给定的坐标替换postition值，确当端点位置 ---> 通过求插值方式给position赋值 ---> 更新
**更具点生成3维曲线，使用 插值法求出点，赋值给lineBufferGeometry**

## 在曲线上运动
```js
    // 实例：webgl_geometry_extrude_splines
    function render() {
        // animate camera along spline
        const time = Date.now();
        const looptime = 20 * 1000;
        const t = ( time % looptime ) / looptime;
        tubeGeometry.parameters.path.getPointAt( t, position );
        position.multiplyScalar( params.scale );
        // interpolation
        const segments = tubeGeometry.tangents.length;
        const pickt = t * segments;
        const pick = Math.floor( pickt );
        const pickNext = ( pick + 1 ) % segments;
        binormal.subVectors( tubeGeometry.binormals[ pickNext ], tubeGeometry.binormals[ pick ] );
        binormal.multiplyScalar( pickt - pick ).add( tubeGeometry.binormals[ pick ] );

        tubeGeometry.parameters.path.getTangentAt( t, direction );
        const offset = 15;
        normal.copy( binormal ).cross( direction );
        // we move on a offset on its binormal
        position.add( normal.clone().multiplyScalar( offset ) );
        splineCamera.position.copy( position );
        cameraEye.position.copy( position );
        // using arclength for stablization in look ahead
        tubeGeometry.parameters.path.getPointAt( ( t + 30 / tubeGeometry.parameters.path.getLength() ) % 1, lookAt );
        lookAt.multiplyScalar( params.scale );
        // camera orientation 2 - up orientation via normal
        if ( ! params.lookAhead ) lookAt.copy( position ).add( direction );
        splineCamera.matrix.lookAt( splineCamera.position, lookAt, normal );
        splineCamera.quaternion.setFromRotationMatrix( splineCamera.matrix );
        cameraHelper.update();
        renderer.render( scene, params.animationView === true ? splineCamera : camera );
    }
```

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


