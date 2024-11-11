这个类用于进行raycasting（光线投射）。 光线投射用于进行鼠标拾取（在三维空间中计算出鼠标移过了什么物体）。

## API
- `.intersectObject ( object : Object3D, recursive : Boolean, optionalTarget : Array )` : Array

- `origin` —— 光线投射的原点向量。
- `direction` —— 为光线提供方向的标准化方向向量。

1. face —— 相交的面  
```js
{
    normal: 相交面的法线，单位向量
    a: 2273
    b: 2234
    c: 2274 相交三角面片的顶点属性
}
// Normal时单位向量，垂直于平面的法线
```


distance —— 射线投射原点和相交部分之间的距离。  
point —— 相交部分的点（世界坐标）  

faceIndex —— 相交的面的索引  
object —— 相交的物体  
uv —— 相交部分的点的UV坐标。  
uv2 —— Second set of U,V coordinates at point of intersection  
instanceId – The index number of the instance where the ray intersects the InstancedMesh   

```js
    // 实例：webgl_decals
	const n = intersects[ 0 ].face.normal.clone();
    n.transformDirection( mesh.matrixWorld );
    // 沿着法线的方向移动10个单位
    n.multiplyScalar( 10 );
    n.add( intersects[ 0 ].point );
    // 生成了一条从交点垂直于平面的法线
```
两个点连成一条线，从向量起点指向终点的运算为  
向量的乘法表示延向量方向移动x距离

1. Obb
> 实现通过点击的方式进行射线拾取
```js
import { OBB } from 'three/addons/math/OBB.js';

function onClick( event ) {
    event.preventDefault();
    mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
    mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;
    raycaster.setFromCamera( mouse, camera );
    const intersectionPoint = new THREE.Vector3();
    const intersections = [];
    for ( let i = 0, il = objects.length; i < il; i ++ ) {
        const object = objects[ i ];
        const obb = object.userData.obb;
        const ray = raycaster.ray;
        if ( obb.intersectRay( ray, intersectionPoint ) !== null ) {
            const distance = ray.origin.distanceTo( intersectionPoint );
            intersections.push( { distance: distance, object: object } );
        }
    }
    if ( intersections.length > 0 ) {
        // determine closest intersection and highlight the respective 3D object
        // 当有多个重叠物体被捕获后，确定里屏幕最近的物体
        intersections.sort( sortIntersections );
        intersections[ 0 ].object.add( hitbox );
    } else {
        const parent = hitbox.parent;
        if ( parent ) parent.remove( hitbox );
    }
}
// 实现切换换肤效果
for ( let i = 0, il = objects.length; i < il; i ++ ) {
    const object = objects[ i ];
    const obb = object.userData.obb;
    for ( let j = i + 1, jl = objects.length; j < jl; j ++ ) {
        const objectToTest = objects[ j ];
        const obbToTest = objectToTest.userData.obb;
        if ( obb.intersectsOBB( obbToTest ) === true ) {
            object.material.color.setHex( 0xff0000 );
            objectToTest.material.color.setHex( 0xff0000 );
        }
    }
}
```
总结：
1. 光线投射的起点 Raycaster.ray.origin, 与捕获的点intersectionPoint，向量之间求距离distanceTo
2. Obb光线拾取原理
 2.1 遍历物体对象，获取每个对象下的obb  
 2.2 如果物体对象obb与光线相交，赋值相交点  
 `obb.intersectRay( ray, intersectionPoint )`

2. 射线的起点就是相机的位置
示例：misc_controls_pointerlock
运动计算、在运动中光线捕获

2.1 物体的捕获
```js
raycaster = new THREE.Raycaster( new THREE.Vector3(), new THREE.Vector3( 0, - 1, 0 ), 0, 10 );

// 设置捕获起点为相机的位置相当于人的眼睛
raycaster.ray.origin.copy( controls.getObject().position );
raycaster.ray.origin.y -= 10;

```
控制能否跳跃的关键字：canJump
核心是对摄像机的控制

1. 初始化当相机小于一定高度可以跳跃
```js
// 
controls.getObject().position.y += ( velocity.y * delta ); // new behavior

if ( controls.getObject().position.y < 10 ) {
    // 速度减为零
    velocity.y = 0;
    controls.getObject().position.y = 10;
    // 
    canJump = true;
}


```


```js
raycaster.ray.origin.copy( controls.getObject().position );
raycaster.ray.origin.y -= 10;
```

实例：webgl_interactive_lines
线捕获时点触发的范围：
 ```js
 raycaster.params.Line.threshold = 3;
 ```


 鼠标交互-圈选示例：misc_boxselection

