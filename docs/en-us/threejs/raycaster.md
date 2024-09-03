这个类用于进行raycasting（光线投射）。 光线投射用于进行鼠标拾取（在三维空间中计算出鼠标移过了什么物体）。

## API
- `.intersectObject ( object : Object3D, recursive : Boolean, optionalTarget : Array )` : Array

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