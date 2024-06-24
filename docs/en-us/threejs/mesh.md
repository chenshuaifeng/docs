## 属性
`.getWorldPosition ( target : Vector3 ) : Vector3`
target — 结果将被复制到这个Vector3中。

返回一个表示该物体在世界空间中位置的矢量。

## 多实例物体InstanceMesh(geometry, material, count)

创建由多个物体组成的立方体
已知列数count=10,物体直径=1 通过物体直径间接得出lenth=10。有别于`索引 * （length / (count -1)） - offset`，通过矩阵的方式直接进行一个求值

```js
let index=0; // 单位物体
const offset=(amount -1)/2;/// 4.5
const matrix = new THREE.Matrix4();

for(let i=0;i<amount;i++){
    for(let j=0;j<amount;j++){
        for(let k=0;k<amount;k++){
            matrix.setPosition(offset-i,offset-j,offset-k);
            meshes.setMatrixAt(index,matrix);
            meshes.setColorAt(index,white);
            index=index + 1;
        }
    }
}
```
- `matrix.setPosition`
将位置坐标保存在矩阵中，给当前矩阵设置位置坐标

- `instanceMesh.setMatrixAt(i, matrix)` 
At给给定物体添加矩阵数据，.instanceMatrix : InstancedBufferAttribute，表示所有实例的本地变换。 如果你要通过 .setMatrixAt() 来修改实例数据，你必须将它的 needsUpdate 标识为 true 。

- `setColorAt`
给物体添加颜色，instanceColor : InstancedBufferAttribute，如果通过.setColorAt()修改实例化数据，则必须将它的needsUpdate标志设置为 true。

- `getColorAt `
获取当前mesh的颜色

## Color
- `color.equals(white)`
用来判断两个颜色是否相等

**mesh.useData**向物体中挂载自定义数据


## 骨架（Skeleton）
用于人体骨架mesh，骨架（Skeleton）

## 骨骼Bone
骨骼是Skeleton（骨架）的一部分。骨架是由SkinnedMesh（蒙皮网格）依次来使用的。 骨骼几乎和空白Object3D相同

## SkinnedMesh蒙皮网格物体
具有Skeleton（骨架）和bones（骨骼）的网格，可用于给几何体上的顶点添加动画。

## 点（Points）

### 构造器Construct()
Points( geometry : BufferGeometry, material : Material )
geometry —— （可选）是一个BufferGeometry的实例，默认值是一个新的BufferGeometry。
material —— （可选） 是一个对象，默认值是一个PointsMaterial。

BufferGeometry + Shape|Curve 生成虚线平面 

## `.morph`变形
.morphTargetInfluences : Array
一个包含有权重（值一般在0-1范围内）的数组，指定应用了多少变形。 默认情况下是未定义的，但是会被updateMorphTargets重置为一个空数组。
morph的值 = [0, 1]

.morphTargetDictionary : Object
基于morphTarget.name属性的morphTargets字典。 默认情况下是未定义的，但是会被updateMorphTargets重建。
在mesh中定义的动画字典，key变形名称， value索引（0，1，2）


复制mesh的方法：.clone
```js
lineMesh.clone().rotateY(Math.PI / 2)
lightPillar.add(lightPillar.clone().rotateY(Math.PI / 2));
```

## 批量创建mesh方法一

- `position.set().nomalize()`
```js
const geometry = new THREE.SphereGeometry( 1, 4, 4 );

for ( let i = 0; i < 100; i ++ ) {

    const material = new THREE.MeshPhongMaterial( { color: 0xffffff * Math.random(), flatShading: true } );

    const mesh = new THREE.Mesh( geometry, material );
    mesh.position.set( Math.random() - 0.5, Math.random() - 0.5, Math.random() - 0.5 ).normalize();
    mesh.position.multiplyScalar( Math.random() * 400 );
    mesh.rotation.set( Math.random() * 2, Math.random() * 2, Math.random() * 2 );
    mesh.scale.x = mesh.scale.y = mesh.scale.z = Math.random() * 50;
    object.add( mesh );

}

// 创建出来的物体随机放大
mesh.scale.multiplyScalar (Math.round()) 
// 相同得方法
mesh.scale.setScalar()
```

> 一个算法：由多个立方体构成一个立方体

```js
// 5*5*5的立方体 由 -2 -1 0 1 2五个数组成
// z,y,x， ++i与i++没有区别
// z [-2, 2] - [-0.4 ,0.4]
// y [-2, 2] - [-0.4 ,0.4]
// x [-2, 2] - [-0.4 ,0.4]
// 边长为4 - 边长为0.8
const geometry = new THREE.BoxGeometry( 0.18, 0.18, 0.18 );
for ( let z = - 2; z <= 2; ++ z )
    for ( let y = - 2; y <= 2; ++ y )
        for ( let x = - 2; x <= 2; ++ x ) {
            const mesh = new THREE.Mesh( geometry, clipMaterial );
            // 决定了立方体的间隙
            mesh.position.set( x / 5, y / 5, z / 5 );
            mesh.castShadow = true;
            object.add( mesh );

        }
```