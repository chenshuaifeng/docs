## 平面Plan

一个无线延申的二维平面

构造器（Constructor）
Plane( normal : Vector3, constant : Float )
normal - (可选参数) 定义单位长度的平面法向量Vector3。默认值为 (1, 0, 0)。
constant - (可选参数) 从原点到平面的有符号距离。 默认值为 0.
裁剪的方向与向量方向一致


构造函数有两个参数，第一个参数是一个法线向量，与平面垂直。第二参数是平面到远点的距离

裁剪材质Materail的属性：
```js
	// ***** Clipping planes: *****

    const localPlane = new THREE.Plane( new THREE.Vector3( 0, - 1, 0 ), 0.8 );
    const globalPlane = new THREE.Plane( new THREE.Vector3( - 1, 0, 0 ), 0.1 );

    // Geometry

    const material = new THREE.MeshPhongMaterial( {
        color: 0x80ee10,
        shininess: 100,
        side: THREE.DoubleSide,

        // ***** Clipping setup (material): *****
        clippingPlanes: [ localPlane ],
        clipShadows: true,

        alphaToCoverage: true,

    } );
```
clip与渲染器renderer相关

- intersection检测相交
`.localClippingEnabled : Boolean`
定义渲染器是否考虑对象级剪切平面。 默认为false.

截取上面和右面

`.intersectLine ( line : Line3, target : Vector3 ) : Vector3`
line - 检测是否相交的三维几何线段 Line3。
target — 结果将会写入该向量中。

返回给定线段和平面的交点。如果不相交则返回null。如果线与平面共面，则返回该线段的起始点。

`.intersectsBox ( box : Box3 ) : Boolean`
box - 检查是否相交的包围盒 Box3。

确定该平面是否与给定3d包围盒Box3相交。

`.intersectsLine ( line : Line3 ) : Boolean`
line - 检查是否相交的三维线段 Line3。

测试线段是否与平面相交。

`.intersectsSphere ( sphere : Sphere ) : Boolean`
sphere - 检查是否相交的球体 Sphere。

确定该平面是否与给定球体 Sphere 相交。

## 经纬度转球面坐标
```js
// 经度 [-90, 90]
let lat = Math.random() * 180 - 90;
// 纬度 [-180, 180]
let lon = Math.random() * 360 - 180;

const lon2xyz = (R, longitude, latitude) => {
  let lon = (longitude * Math.PI) / 180; // 转弧度值
  const lat = (latitude * Math.PI) / 180; // 转弧度值
  lon = -lon; // js坐标系z坐标轴对应经度-90度，而不是90度

  // 经纬度坐标转球面坐标计算公式
  const x = R * Math.cos(lat) * Math.cos(lon);
  const y = R * Math.sin(lat);
  const z = R * Math.cos(lat) * Math.sin(lon);
  // 返回球面坐标
  return new THREE.Vector3(x, y, z);
};
```