
### Instance与animation结合
初始化mixer混合glb.scene
动画函数中通过mixer的`.setMorphAt`给多实例中每个实例设置morph
设置动画时间`.setTime`

> instance是在初始化给定position还是在animate中给定position

如果mesh动画需要threejs去设置，在动画中给定position,如果mesh不需要动画或者设置morhp在初始化函数中设置



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

- `.morphTargetInfluences` : Array  
一个包含有权重（值一般在0-1范围内）的数组，指定应用了多少变形。 默认情况下是未定义的，但是会被updateMorphTargets重置为一个空数组。
value
morph的值 = [0, 1]

.morphTargetDictionary : Object
索引
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

## 实现流光渐变
1. 先求顶点


## 代码技巧
一个将物体移动到平面四角得方法
```js
	function updateSpritePosition() {

				const halfWidth = window.innerWidth / 2;
				const halfHeight = window.innerHeight / 2;

				const halfImageWidth = textureSize / 2;
				const halfImageHeight = textureSize / 2;

				sprite.position.set( halfWidth - halfImageWidth, halfHeight - halfImageHeight, 1 );

			}
```
## InstanceMesh
一种具有实例化渲染支持的特殊版本的Mesh。你可以使用 InstancedMesh 来渲染大量具有相同几何体与材质、但具有不同世界变换的物体。 使用 InstancedMesh 将帮助你减少 draw call 的数量，从而提升你应用程序的整体渲染性能。

- `.instanceMatrix : InstancedBufferAttribute`
表示所有实例的本地变换。 如果你要通过 .setMatrixAt() 来修改实例数据，你必须将它的 needsUpdate 标识为 true 。**bufferAtrribute对象**
```js
    // 类型是bufferAttribute
	mesh.instanceMatrix.setUsage( THREE.DynamicDrawUsage );
```
- `.setColorAt ( index : Integer, color : Color ) : undefined`  
为实例应用颜色
```js
mesh.setColorAt( instanceId, color.setHex( Math.random() * 0xffffff ) );
mesh.instanceColor.needsUpdate = true;
```

- `.setMatrixAt `( index : Integer, matrix : Matrix4 ) : undefined   
index: 实例的索引。值必须在 [0, count] 区间。  
matrix: 一个4x4矩阵，表示单个实例本地变换。  
设置给定的本地变换矩阵到已定义的实例。 请确保在更新所有矩阵后将 .instanceMatrix.needsUpdate 设置为true。
```js
	const offset = ( amount - 1 ) / 2;

    for ( let x = 0; x < amount; x ++ ) {

        for ( let y = 0; y < amount; y ++ ) {

            for ( let z = 0; z < amount; z ++ ) {

                dummy.position.set( offset - x, offset - y, offset - z );
                dummy.rotation.y = ( Math.sin( x / 4 + time ) + Math.sin( y / 4 + time ) + Math.sin( z / 4 + time ) );
                dummy.rotation.z = dummy.rotation.y * 2;

                dummy.updateMatrix();
                // 将dummy的位置、坐标、缩放信息应用给mesh
                mesh.setMatrixAt( i ++, dummy.matrix );
            }

        }

    }
    mesh.instanceMatrix.needsUpdate = true;
```
多实例物体应用位置、旋转、缩放的方法：`.setMatrixAt`
1. Object3D应用
2. Matrix矩阵应用


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
### InstanceMesh+Curve


## 销毁mesh
mesh的销毁过程
1. Gemoetry销毁
2. Material销毁
3. Material.map销毁
4. 从场景中移除mesh

```js
const mesh = meshes[ i ];
mesh.material.dispose();
mesh.geometry.dispose();

scene.remove( mesh );
```

## 知识技能
1. 一个向左旋转一般，然后向右旋转一半的算法
```js
// 实例：webgl_instancing_performance
dummy.rotation.y = ( Math.sin( x / 4 + time ) + Math.sin( y / 4 + time ) + Math.sin( z / 4 + time ) );
```

2. 根据子对象找到父对象,然后移除自己

```js
  if ( intersections.length > 0 ) {
        // determine closest intersection and highlight the respective 3D object
        // 当有多个重叠物体被捕获后，确定里屏幕最近的物体
        intersections.sort( sortIntersections );
        intersections[ 0 ].object.add( hitbox );
    } else {
        const parent = hitbox.parent;
        if ( parent ) parent.remove( hitbox );
    }
```