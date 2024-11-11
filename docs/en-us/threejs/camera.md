## 相机的运动
> 物理含义

相机运动的方向的单位向量 **乘以** 平移距离 **等于** 位移向量:  
 `disV = camara.getWorldDirection().clone().mutiplyScalar(200)`  
相机的初始位置 **加上** 位移向量 **等于** 相机的新位置：  
`camara.position.add(disV)`

## 相机类型

### 正交相机OrthographicCamera
在这种投影模式下，无论物体距离相机距离远或者近，在最终渲染的图片中物体的大小都保持不变。

- `OrthographicCamera( left : Number, right : Number, top : Number, bottom : Number, near : Number, far : Number )`

算法，由小到大，由大到小
```js
velocity.x -= velocity.x * 10.0 * delta;
velocity.z -= velocity.z * 10.0 * delta;
```

### 立方体相机CubeCamera

创建6个渲染到WebGLCubeRenderTarget的摄像机。

```js
// 把CubeCamera作为场景的子对象添加到场景中
CubeCamera = new THREE.CubeCamera( 0.05, 50, cubeRenderTarget );
scene.add( CubeCamera )

// 从场景中获取到纹理赋值给模型的材质
const mirrorSphereMaterial = new THREE.MeshBasicMaterial( { envMap: cubeRenderTarget.texture } );
OOI.sphere.material = mirrorSphereMaterial;

// animate
cubeCamera.update( renderer, scene );
```
`cubeCamera`与`WebGLCubeRenderTarget`一起使用

**总结：动态纹理投射**

### 阵列相机ArrayCamera
ArrayCamera 用于更加高效地使用一组已经预定义的摄像机来渲染一个场景。这将能够更好地提升VR场景的渲染性能。  
一个 ArrayCamera 的实例中总是包含着一组子摄像机，应当为每一个子摄像机定义viewport（视口）这个属性，这一属性决定了由该子摄像机所渲染的视口区域的大小。


只需要创建一个模型物体，创建100个相机矩阵，就等于100个模型物体  
一个正方形平局分配的算法  
已知量：粒子个数AMOUNT=6；边长length = 1（length/2为偏移量）；例子间距为gap=length/(AMOUNT-1)=1/5  

```js
for ( let y = 0; y < AMOUNT; y ++ ) {
    for ( let x = 0; x < AMOUNT; x ++ ) {
        /**
         * x: 6个一循环 0 285 570 865 1141 1426
         * position.x: -0.5, -0.3, -0.16, 0, 0.16 0.33
         * position.y: 0.5*6， 0.33*6 0.16*6 0*6 -0.16*6 -0.33*6
         */
        // 大小是以当前屏幕视口大小/个数，平局分布在屏幕空间中
        const subcamera = new THREE.PerspectiveCamera( 40, ASPECT_RATIO, 0.1, 10 );
        subcamera.viewport = new THREE.Vector4( Math.floor( x * WIDTH ), Math.floor( y * HEIGHT ), Math.ceil( WIDTH ), Math.ceil( HEIGHT ) );
        // 相机的位置,从左到右，从上到下
        // 1. x坐标计算公式：点的索引*间距 - 偏移量 = 索引 * (length / (amount-1)) - 偏移量
        subcamera.position.x = ( x / (AMOUNT-1) ) - 0.5;
        subcamera.position.y = 0.5 - ( y / (AMOUNT-1) );
        subcamera.position.z = 1.5;
        // 相机的位置坐标乘以2扩大倍 = 视野扩大2倍 = 物体缩小二倍
        subcamera.position.multiplyScalar( 2 );
        subcamera.lookAt( 0, 0, 0 );
        subcamera.updateMatrixWorld();
        cameras.push( subcamera );
        // 在遍历循环中获取第几个
        const subcamera = camera.cameras[ AMOUNT * y + x ]
        }
    }
```
- `viewport：Vector4`视口范围
大小是以当前屏幕视口大小


### 电影相机cinematicCamera

在Three.js中，CinematicCamera并不是一个内置的相机类型，但它是基于Three.js的自定义扩展或插件，用于实现电影级别的视觉效果，如景深、动态模糊、镜头光晕等。虽然Three.js本身提供了PerspectiveCamera和OrthographicCamera等基本的相机类型，但CinematicCamera通过引入额外的渲染技术和后期处理来增强这些基本相机的功能。

关于CinematicCamera在Three.js中的使用，以下是一些关键点和信息：

1. 引入与实现
CinematicCamera通常作为Three.js的插件或扩展引入，因此可能需要从外部源获取并包含到项目中。
它可能依赖于其他库或插件，如后期处理库（如three.js/examples/jsm/postprocessing），用于实现复杂的视觉效果。
2. 特性与功能
景深（Depth of Field）：模拟真实世界相机在不同焦距下的模糊效果，使得画面中的某些部分清晰，而其他部分模糊。
动态模糊（Motion Blur）：在相机或对象移动时产生模糊效果，增强动态场景的视觉冲击力。
镜头光晕（Lens Flares）：模拟光线穿过镜头时产生的光晕效果，常用于增强明亮光源的视觉表现。
其他后期处理效果：可能包括颜色校正、对比度调整、亮度控制等，用于增强整体画面的视觉效果。
3. 使用步骤
创建相机：与创建PerspectiveCamera或OrthographicCamera类似，首先需要创建一个CinematicCamera对象。
初始化：设置相机的初始参数，如焦距、光圈大小、快门速度等。这些参数将影响景深、动态模糊等效果的表现。
设置属性：根据需要调整相机的属性，如对焦距离、模糊强度等，以实现所需的视觉效果。
添加后期处理：将CinematicCamera与后期处理库结合使用，添加所需的视觉效果。这通常涉及创建后期处理通道、添加效果等步骤。
渲染：使用Three.js的渲染器进行渲染，将场景中的对象与相机视图结合，生成最终的图像。
4. 注意事项
性能开销：由于CinematicCamera涉及复杂的渲染技术和后期处理，因此可能会对性能产生一定影响。在使用时需要权衡视觉效果与性能之间的关系。
兼容性：不同的浏览器和设备可能对某些视觉效果的支持程度不同。因此，在使用CinematicCamera时需要确保目标平台具备足够的兼容性。
调试与优化：在开发过程中，需要仔细调试和优化CinematicCamera的参数和效果，以确保最终输出的图像质量满足要求。


## 一个相机随鼠标摇摆的算法

1. 获取屏幕坐标
```js
mouse[ 0 ] = ev.clientX / window.innerWidth; // [0, 1]
mouse[ 1 ] = ev.clientY / window.innerHeight;
```

2. 系数
```js
const zoom = THREE.MathUtils.clamp( Math.pow( Math.E, zoompos ), minzoom, maxzoom );
```

3. 相机坐标
```js
objects.normal.camera.position.x = Math.sin( .5 * Math.PI * ( mouse[ 0 ] - .5 ) ) * zoom;
objects.normal.camera.position.y = Math.sin( .25 * Math.PI * ( mouse[ 1 ] - .5 ) ) * zoom;
objects.normal.camera.position.z = Math.cos( .5 * Math.PI * ( mouse[ 0 ] - .5 ) ) * zoom;
objects.normal.camera.lookAt( objects.normal.scene.position );
```
zoom与深度缓冲区例子有关系
Math.sin( .5 * Math.PI * ( mouse[ 0 ] - .5 ) ) 这个表达式描述了一个基于鼠标位置（通常mouse[0]表示鼠标的x坐标）的正弦波（sine wave）函数。具体来说，这个表达式的行为可以通过分析其组成部分来理解：

mouse[0] - .5：这部分是将鼠标的x坐标减去0.5。假设mouse[0]的范围是0到1（例如，它可能表示一个归一化的屏幕宽度，其中0是左侧，1是右侧），那么mouse[0] - .5的范围将是-0.5到0.5。
.5 * Math.PI * ( mouse[ 0 ] - .5 )：这部分将上一步得到的值乘以π/2（即0.5乘以π）。因此，当mouse[0]从0变化到1时，该表达式的结果将从-π/4变化到π/4（即-0.7854变化到0.7854，单位是弧度）。
Math.sin(...)：最后，对上述表达式的结果应用正弦函数。正弦函数在-π/2到π/2的范围内是从-1变化到1的。由于我们的输入值范围是-π/4到π/4，所以输出的正弦值范围将是-√2/2到√2/2（即-0.707到0.707）。
现在，关于这个表达式的曲线形状：

当mouse[0]从0变化到0.5时，Math.sin( .5 * Math.PI * ( mouse[ 0 ] - .5 ) )的值将从-√2/2增加到0（即从-0.707增加到0）。
当mouse[0]从0.5变化到1时，Math.sin( .5 * Math.PI * ( mouse[ 0 ] - .5 ) )的值将从0增加到√2/2（即从0增加到0.707）。
因此，整个曲线将是一个从-√2/2到√2/2的正弦波的一部分，该波在mouse[0] = 0.5时达到其最低点（或最高点，取决于你如何定义“最低”和“最高”）。这个波形的确切形状取决于mouse[0]的实际范围，但上述描述假设了mouse[0]在0到1之间。

如果你将这个表达式用于图形或动画中，并随着鼠标的移动而实时更新，你将看到一个正弦波的一部分在屏幕上移动。


代码示例：来源webgl_effects_anaglyph，场景中的物体随鼠标上下翻转、左右翻转的效果，实质是相机的位置**Position**发生变化
```js
// 1. 获取鼠标坐标 左右[-5.4, 5.4] [-3.4, 3.4]
function onDocumentMouseMove( event ) {

    mouseX = ( event.clientX - window.innerWidth / 2 ) / 100;
    mouseY = ( event.clientY - window.innerHeight / 2 ) / 100;

}
// 2. 在动画函数中给相机赋值,
// 2.1 位置坐标+=
// 2.2 系数表示相机旋转速率
// 2.3 减去相机的坐标是禁止超出边界
// 2.4 y轴是相反轴
camera.position.x += ( mouseX - camera.position.x )* 0.05 ;
camera.position.y += ( - mouseY - camera.position.y )* 0.05;

camera.lookAt( scene.position );
```

 - `.layers : Layers`
图层
物体的层级关系。 物体只有和一个正在使用的Camera至少在同一个层时才可见。当使用Raycaster进行射线检测的时候此项属性可以用于过滤不参与检测的物体.
1. 相机的图层layer.enable 
2. Obeject的图层object.layers.set
3. 图层操作方法。类似于Map

.layers : Layers
Used by Raycaster to selectively ignore 3D objects when performing intersection tests. The following code example ensures that only 3D objects on layer 1 will be honored by the instance of Raycaster.
```js
raycaster.layers.set( 1 );
object.layers.enable( 1 );
	light.layers.enable( 0 );
    light.layers.enable( 1 );
    light.layers.enable( 2 );
```

## 相机沿着曲线运动
核心原理：**插值运算**
  
1. 创建group，将camera作为子对象添加到group中
2. 动画函数中，插值运算
**求插值的算法**: t=[0, 1],循环时间looptime = 20s
```js
	const time = Date.now();
    const looptime = 20 * 1000;
    const t = ( time % looptime ) / looptime;
    
```
3. 在三维运动中必须调整相机得姿态(欧拉角及四元数)
4. 运动过程中得相机朝向  `loockAt`相机看向正前方
可以通过相机Helper辅助开发，查看相机姿态及位置    

5. lookHead的原理: t + 30 / tubeGeometry.parameters.path.getLength()
获取到position`tubeGeometry.parameters.path.getPointAt( t, position );`
lookHead: `tubeGeometry.parameters.path.getPointAt( ( t + 30 / tubeGeometry.parameters.path.getLength() ) % 1, lookAt );`


在管道中的运动
1. 先求次法线
2. 再求position点的位置
3. 通过点乘求法线
4. 更新矩阵
总结：法线 = 次法线 点乘 位置
```js
const segments = tubeGeometry.tangents.length;
const pickt = t * segments; 
// [0,100]和下一个[0, 100]+1
const pick = Math.floor( pickt );
const pickNext = ( pick + 1 ) % segments;
// .binormals : Array一个Vector3次法线数组
binormal.subVectors( tubeGeometry.binormals[ pickNext ], tubeGeometry.binormals[ pick ] );
binormal.multiplyScalar( pickt - pick ).add( tubeGeometry.binormals[ pick ] );

tubeGeometry.parameters.path.getTangentAt( t, direction );
const offset = 15;

normal.copy( binormal ).cross( direction );

// we move on a offset on its binormal
// 沿法线偏移
position.add( normal.clone().multiplyScalar( offset ) );
```
最后一步更新矩阵
```js
splineCamera.matrix.lookAt( splineCamera.position, lookAt, normal );
splineCamera.quaternion.setFromRotationMatrix( splineCamera.matrix );
```
- `.lookAt ( eye : Vector3, target : Vector3, up : Vector3 ) : this`
构造一个旋转矩阵，从eye 指向 target，由向量 up 定向。

```js
function render() {

    // animate camera along spline

    const time = Date.now();
    const looptime = 20 * 1000;
    // [0, 1]按照帧率递增得顺寻
    const t = ( time % looptime ) / looptime; 
    // 求插值运算获取position位置。 
    tubeGeometry.parameters.path.getPointAt( t, position );
    // 扩大倍数
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
    // 给相机position赋值
    splineCamera.position.copy( position );
    cameraEye.position.copy( position );

    // using arclength for stablization in look ahead

    tubeGeometry.parameters.path.getPointAt( ( t + 30 / tubeGeometry.parameters.path.getLength() ) % 1, lookAt );
    lookAt.multiplyScalar( params.scale );

    // camera orientation 2 - up orientation via normal
    // 改变相机运动过程始终看向正前方
    // 通过四元数来改变朝向，相机朝向得复杂设置原因是因为tubeGeometry
    if ( ! params.lookAhead ) lookAt.copy( position ).add( direction );
    splineCamera.matrix.lookAt( splineCamera.position, lookAt, normal );
    splineCamera.quaternion.setFromRotationMatrix( splineCamera.matrix );

    cameraHelper.update();
    // 通过改变相机来改变视角
    renderer.render( scene, params.animationView === true ? splineCamera : camera );

}
```

## 相机小技巧
> 如何将屏幕分成多份，渲染多个场景，切换相机
- `asepect`
- `.setViewport`
- `.autoClear`
- `.render`
- `.lookAt`

总结：通过切换渲染的相机来改变视角，类似控制监视器
相机切换原理
原理是：
1. 渲染函数中调用`this.renderer.render(this.scene, this.camera);`
2. 传入不同得相机
3. 将相机作为子对象添加到物体模型中

```js
cameraHelper.update();
renderer.render( scene, params.animationView === true ? splineCamera : camera );
```

```js
camera = new THREE.PerspectiveCamera( 50, 0.5 * aspect, 1, 10000 );
renderer.autoClear = false;
function render() {
        renderer.clear();
        renderer.setViewport( 0, 0, SCREEN_WIDTH / 2, SCREEN_HEIGHT );
        renderer.render( scene, activeCamera );
        renderer.setViewport( SCREEN_WIDTH / 2, 0, SCREEN_WIDTH / 2, SCREEN_HEIGHT );
        renderer.render( scene, camera );
    }

```

2. 初始化相机角度的代码
示例：webgl_pmrem_test
相机正视物体
```js
function updateCamera() {
    const horizontalFoV = 40;
    const verticalFoV = 2 * Math.atan( Math.tan( horizontalFoV / 2 * Math.PI / 180 ) / camera.aspect ) * 180 / Math.PI;
    camera.fov = verticalFoV;
    camera.updateProjectionMatrix();
}
```