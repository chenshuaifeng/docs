## `GLTFLoader` 

```js
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

loader.load( 'models/gltf/Soldier.glb', function ( gltf ) {
    ...
    // 模型比较大的时候播放动画在回调中进行
    renderer.setAnimationLoop( animate );
} );
```

## CCDIK解算器(CCDIKSolver)
一种基于 CCD Algorithm 的 IK 解算器。

CCDIKSolver 用 CCD 算法解决逆运动学问题。 CCDIKSolver 设计用于与 SkinnedMesh 配合使用，但也可与 MMDLoader 或 GLTFLoader 配合使用。

```js
gltf.scene.traverse( n => {
// 保存物体对象
				if ( n.name === 'head' ) OOI.head = n;
				if ( n.name === 'lowerarm_l' ) OOI.lowerarm_l = n;
				if ( n.name === 'Upperarm_l' ) OOI.Upperarm_l = n;
				if ( n.name === 'hand_l' ) OOI.hand_l = n;
				if ( n.name === 'target_hand_l' ) OOI.target_hand_l = n;

				if ( n.name === 'boule' ) OOI.sphere = n;
				if ( n.name === 'Kira_Shirt_left' ) OOI.kira = n;

			} );

            
```
- `.attach ( object : Object3D ) : this`
将object作为子级来添加到该对象中，同时保持该object的世界变换
```js
const targetPosition = OOI.sphere.position.clone(); // for orbit controls
OOI.hand_l.attach( OOI.sphere );
```

## FontLoader文字加载器
fontLoader与textGeometry一起使用

## ColladaLoader

`mash.material.flatShading = true`

`.dae` 模型加载器
```js
import { ColladaLoader } from 'three/addons/loaders/ColladaLoader.js';
```

## FbxLoader 

```js
	import { FBXLoader } from 'three/addons/loaders/FBXLoader.js';
```

## `PBR`分子式

`PDBLoader`加载PDB模型
文件结构
```js
{
	geometryAtoms: 物体位置、颜色，表示原子
	geometryBonds：线条，表示化学键
	json：位置、标注颜色、标注
}
```

```js
color.r = Colors.get(i)
// 在该向量与传入的向量v之间的线性插值，alpha是沿着线的长度的百分比 —— alpha = 0 时表示的是当前向量，alpha = 1 时表示的是所传入的向量v。
position.lerp(end, 0.5)

// 在向量的两个点中间进行线性插值
// 线段不够长,延长线段
scale.z=start.distanceTo(end)*10

// 改变线段方向
lookAt(end)

```