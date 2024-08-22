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

### 人物粒子下落效果要素
speed：下落速度
direction： 运动方向
start： 开始的位置

次要要素：
delay: 延迟时间

时间动画中运动

运动分析：物体由初始位置start运动到0处， 当start=0时，物体的方向改变,在运动的**过程中改变顶点的位置**


位移 = 速度 * 时间
```js
if ( data.direction < 0 ) {

	if ( py > 0 ) {

		positions.setXYZ(
			i,
			px + 1.5 * ( 0.50 - Math.random() ) * data.speed * delta,
			py + 3.0 * ( 0.25 - Math.random() ) * data.speed * delta,
			pz + 1.5 * ( 0.50 - Math.random() ) * data.speed * delta
		);

	} else {

		data.verticesDown += 1;
	}
}
```

往上走的时候 点的位置pos = |当前位置 - 初始化位置 |

```js
// rising up
if ( data.direction > 0 ) {

	const ix = initialPositions.getX( i );
	const iy = initialPositions.getY( i );
	const iz = initialPositions.getZ( i );

	const dx = Math.abs( px - ix );
	const dy = Math.abs( py - iy );
	const dz = Math.abs( pz - iz );

	const d = dx + dy + dx;

	if ( d > 1 ) {

		positions.setXYZ(
			i,
			px - ( px - ix ) / dx * data.speed * delta * ( 0.85 - Math.random() ),
			py - ( py - iy ) / dy * data.speed * delta * ( 1 + Math.random() ),
			pz - ( pz - iz ) / dz * data.speed * delta * ( 0.85 - Math.random() )
		);

	} else {

		data.verticesUp += 1;

	}

}
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

