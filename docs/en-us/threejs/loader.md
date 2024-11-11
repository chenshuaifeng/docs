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

traverse遍历得是Group

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

## OBJLoader
实例：webgl_materials_channels
```js
const loader = new OBJLoader();
loader.load( 'models/obj/ninja/ninjaHead_Low.obj', function ( group ) {
	const geometry = group.children[ 0 ].geometry;
	geometry.attributes.uv2 = geometry.attributes.uv;
	geometry.center();
	mesh = new THREE.Mesh( geometry, materialNormal );
	mesh.scale.multiplyScalar( 25 );
	mesh.userData.matrixWorldPrevious = new THREE.Matrix4(); // for velocity
	scene.add( mesh );
} );
```
1. 开启Geometry第二UV, UV是AttributeGeometry属性
2. 将边界几何体居中

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
示例：css3d_molecules
`PDBLoader`加载PDB模型，文件结构：
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
计算包围盒的质心远点
```js
geometryAtoms.computeBoundingBox();
geometryAtoms.boundingBox.getCenter( offset ).negate();
geometryAtoms.translate( offset.x, offset.y, offset.z );
geometryBonds.translate( offset.x, offset.y, offset.z );
```

在多个点中进行链接
1. 两个点组成一条线
2. 一个点可以与多个线相连

```js
for ( let i = 0; i < positionBonds.count; i += 2 ) {
	start.fromBufferAttribute( positionBonds, i );
	end.fromBufferAttribute( positionBonds, i + 1 );
	start.multiplyScalar( 75 );
	end.multiplyScalar( 75 );
	tmpVec1.subVectors( end, start );
	const bondLength = tmpVec1.length() - 50;
	let bond = document.createElement( 'div' );
	bond.className = 'bond';
	bond.style.height = bondLength + 'px';
	let object = new CSS3DObject( bond );
	object.position.copy( start );
	object.position.lerp( end, 0.5 );
	object.userData.bondLengthShort = bondLength + 'px';
	object.userData.bondLengthFull = ( bondLength + 55 ) + 'px';
	const axis = tmpVec2.set( 0, 1, 0 ).cross( tmpVec1 );
	const radians = Math.acos( tmpVec3.set( 0, 1, 0 ).dot( tmpVec4.copy( tmpVec1 ).normalize() ) );
	const objMatrix = new THREE.Matrix4().makeRotationAxis( axis.normalize(), radians );
	object.matrix.copy( objMatrix );
	object.quaternion.setFromRotationMatrix( object.matrix );
	object.matrixAutoUpdate = false;
	object.updateMatrix();
	root.add( object );
	objects.push( object );
	bond = document.createElement( 'div' );
	bond.className = 'bond';
	bond.style.height = bondLength + 'px';
	const joint = new THREE.Object3D( bond );
	joint.position.copy( start );
	joint.position.lerp( end, 0.5 );
	joint.matrix.copy( objMatrix );
	joint.quaternion.setFromRotationMatrix( joint.matrix );
	joint.matrixAutoUpdate = false;
	joint.updateMatrix();
	object = new CSS3DObject( bond );
	object.rotation.y = Math.PI / 2;
	object.matrixAutoUpdate = false;
	object.updateMatrix();
	object.userData.bondLengthShort = bondLength + 'px';
	object.userData.bondLengthFull = ( bondLength + 55 ) + 'px';
	object.userData.joint = joint;
	joint.add( object );
	root.add( joint );
	objects.push( object );
}
```

## SVGLoader
示例:webgl_geometry_text_stroke
可以将向量点转换成Geometry

```js
const style = SVGLoader.getStrokeStyle( 5, color.getStyle() );

	const strokeText = new THREE.Group();

	for ( let i = 0; i < shapes.length; i ++ ) {

		const shape = shapes[ i ];

		const points = shape.getPoints();

		const geometry = SVGLoader.pointsToStroke( points, style );

		geometry.translate( xMid, 0, 0 );

		const strokeMesh = new THREE.Mesh( geometry, matDark );
		strokeText.add( strokeMesh );

	}

	scene.add( strokeText );
	```

## FontLoader

```js
import { FontLoader } from 'three/addons/loaders/FontLoader.js';
import { TextGeometry } from 'three/addons/geometries/TextGeometry.js';

// 让文字居中
textGeo.computeBoundingBox();
const centerOffset = - 0.5 * ( textGeo.boundingBox.max.x - textGeo.boundingBox.min.x );

// 文字平面
const shapes = font.generateShapes( message, 100 );
const geometry = new THREE.ShapeGeometry( shapes );

// 文字线框
const points = shape.getPoints();
const geometry = new THREE.BufferGeometry().setFromPoints( points );

geometry.translate( xMid, 0, 0 );

const lineMesh = new THREE.Line( geometry, matDark );
lineText.add( lineMesh );

```
文字的线框模式添加holeShape
```js
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

## MLTLoader
`.MTL `文件格式是 `.OBJ `的配套文件格式,用于加载材质定义文件

## RGBELoader
纹理贴图加载器
```js
	new RGBELoader()
		// .setDataType( THREE.FloatType )
		.setPath( 'textures/equirectangular/' )
		.load( 'spot1Lux.hdr', function ( texture ) {

			radianceMap = pmremGenerator.fromEquirectangular( texture ).texture;
			pmremGenerator.dispose();

			scene.background = radianceMap;

			const geometry = new THREE.SphereGeometry( 0.4, 32, 32 );

			for ( let x = 0; x <= 10; x ++ ) {

				for ( let y = 0; y <= 2; y ++ ) {

					const material = new THREE.MeshPhysicalMaterial( {
						roughness: x / 10,
						metalness: y < 1 ? 1 : 0,
						color: y < 2 ? 0xffffff : 0x000000,
						envMap: radianceMap,
						envMapIntensity: 1
					} );

					const mesh = new THREE.Mesh( geometry, material );
					mesh.position.x = x - 5;
					mesh.position.y = 1 - y;
					scene.add( mesh );

				}

			}

			render();

		} );

	const pmremGenerator = new THREE.PMREMGenerator( renderer );
	pmremGenerator.compileEquirectangularShader();
```

## OBJLoader
`.OBJ`格式的模型内不是由多个mesh共同组成

将OBJ模型的顶点坐标拿出，转换成其他网格对象
```js
	function createMesh( positions, scene, scale, x, y, z, color ) {

		const geometry = new THREE.BufferGeometry();
		geometry.setAttribute( 'position', positions.clone() );
		geometry.setAttribute( 'initialPosition', positions.clone() );

		geometry.attributes.position.setUsage( THREE.DynamicDrawUsage );

		const clones = [

			[ 6000, 0, - 4000 ],
			[ 5000, 0, 0 ],
			[ 1000, 0, 5000 ],
			[ 1000, 0, - 5000 ],
			[ 4000, 0, 2000 ],
			[ - 4000, 0, 1000 ],
			[ - 5000, 0, - 5000 ],

			[ 0, 0, 0 ]

		];

		for ( let i = 0; i < clones.length; i ++ ) {

			const c = ( i < clones.length - 1 ) ? 0x252525 : color;

			mesh = new THREE.Points( geometry, new THREE.PointsMaterial( { size: 30, color: c } ) );
			mesh.scale.x = mesh.scale.y = mesh.scale.z = scale;

			mesh.position.x = x + clones[ i ][ 0 ];
			mesh.position.y = y + clones[ i ][ 1 ];
			mesh.position.z = z + clones[ i ][ 2 ];

			parent.add( mesh );

			clonemeshes.push( { mesh: mesh, speed: 0.5 + Math.random() } );

		}

		meshes.push( {
			mesh: mesh, verticesDown: 0, verticesUp: 0, direction: 0, speed: 15, delay: Math.floor( 200 + 200 * Math.random() ),
			start: Math.floor( 100 + 200 * Math.random() ),
		} );

	}
```
## 3DMLoader
```js
import { Rhino3dmLoader } from 'three/addons/loaders/3DMLoader.js';

const loader = new Rhino3dmLoader();
loader.setLibraryPath( 'https://cdn.jsdelivr.net/npm/rhino3dm@7.15.0/' );
loader.load( 'models/3dm/Rhino_Logo.3dm', function ( object ) {
scene.add( object );
} );

```
## TDSLoader
加载`.3ds`文件
```js
loader.load( 'models/3ds/portalgun/portalgun.3ds', function ( object ) {
	object.traverse( function ( child ) {
		if ( child.isMesh ) {
			child.material.specular.setScalar( 0.1 );
			child.material.normalMap = normal;
		}
	} );
	scene.add( object );
} );
```


## BufferGeometryLoader

**实例webgl_materials_wireframe**
导入JSON格式的Geometry模型对象
内部结构Center轴上方向向量

通过自定Shader的方式控制BufferGeometry的厚度
```js
	const material2 = new THREE.ShaderMaterial( {

	uniforms: { 'thickness': { value: API.thickness } },
	vertexShader: document.getElementById( 'vertexShader' ).textContent,
	fragmentShader: document.getElementById( 'fragmentShader' ).textContent,
	side: THREE.DoubleSide,
	alphaToCoverage: true // only works when WebGLRenderer's "antialias" is set to "true"

} );
material2.extensions.derivatives = true;

mesh2 = new THREE.Mesh( geometry, material2 );
mesh2.position.set( 40, 0, 0 );

scene.add( mesh2 );
```
JSON模式得模型
```js
const loader = new THREE.ObjectLoader();
const object = await loader.loadAsync( 'models/json/lightmap/lightmap.json' );
scene.add( object );
```

## SVG
svg_sandbox
```js
const loader = new THREE.BufferGeometryLoader();
				loader.load( 'models/json/QRCode_buffergeometry.json', function ( geometry ) {

					mesh = new THREE.Mesh( geometry, new THREE.MeshLambertMaterial( { vertexColors: true } ) );
					mesh.scale.x = mesh.scale.y = mesh.scale.z = 2;
					scene.add( mesh );

				} );
```
```js
	const fileLoader = new THREE.FileLoader();
				fileLoader.load( 'models/svg/hexagon.svg', function ( svg ) {

					const node = document.createElementNS( 'http://www.w3.org/2000/svg', 'g' );
					const parser = new DOMParser();
					const doc = parser.parseFromString( svg, 'image/svg+xml' );

					node.appendChild( doc.documentElement );

					const object = new SVGObject( node );
					object.position.x = 500;
					scene.add( object );

				} );

```


使用向量创建三角形的方法
1. 四个向量
2. 三个向量三角形的三个顶点

v表示三角形的分布范围
v0、v1、v2表示三角形的边长
```js

	const geometry = new THREE.BufferGeometry();
	const material = new THREE.MeshBasicMaterial( { vertexColors: true, side: THREE.DoubleSide } );

	const v = new THREE.Vector3();
	const v0 = new THREE.Vector3();
	const v1 = new THREE.Vector3();
	const v2 = new THREE.Vector3();
	const color = new THREE.Color();

	const vertices = [];
	const colors = [];

	for ( let i = 0; i < 100; i ++ ) {

		v.set(
			Math.random() * 1000 - 500,
			Math.random() * 1000 - 500,
			Math.random() * 1000 - 500
		);

		v0.set(
			Math.random() * 100 - 50,
			Math.random() * 100 - 50,
			Math.random() * 100 - 50
		);

		v1.set(
			Math.random() * 100 - 50,
			Math.random() * 100 - 50,
			Math.random() * 100 - 50
		);

		v2.set(
			Math.random() * 100 - 50,
			Math.random() * 100 - 50,
			Math.random() * 100 - 50
		);

		v0.add( v );
		v1.add( v );
		v2.add( v );

		color.setHex( Math.random() * 0xffffff );

		// create a single triangle

		vertices.push( v0.x, v0.y, v0.z );
		vertices.push( v1.x, v1.y, v1.z );
		vertices.push( v2.x, v2.y, v2.z );

		colors.push( color.r, color.g, color.b );
		colors.push( color.r, color.g, color.b );
		colors.push( color.r, color.g, color.b );

	}

	geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices, 3 ) );
	geometry.setAttribute( 'color', new THREE.Float32BufferAttribute( colors, 3 ) );

	group = new THREE.Mesh( geometry, material );
	group.scale.set( 2, 2, 2 );
	scene.add( group );
	```



代码技巧
`vectors[ i % 3 ].toArray( centers, i * 3 );`



## Maneger
实例：webgl_loader_3mf
示例：webgl_materials_envmaps_exr
```js
const manager = new THREE.LoadingManager();
manager.onLoad = function () {
	const aabb = new THREE.Box3().setFromObject( object );
	const center = aabb.getCenter( new THREE.Vector3() );
	object.position.x += ( object.position.x - center.x );
	object.position.y += ( object.position.y - center.y );
	object.position.z += ( object.position.z - center.z );
	controls.reset();
	scene.add( object );
	render();
};

loader = new ThreeMFLoader( manager );
loadAsset( params.asset );
function loadAsset( asset ) {
	loader.load( 'models/3mf/' + asset + '.3mf', function ( group ) {
		if ( object ) {
			object.traverse( function ( child ) {
				if ( child.material ) child.material.dispose();
				if ( child.material && child.material.map ) child.material.map.dispose();
				if ( child.geometry ) child.geometry.dispose();
			} );
			scene.remove( object );
		}
		object = group;
	} );
}
```
1. 在管理的加载函数load完成后进行渲染

示例：webgl_loader_obj