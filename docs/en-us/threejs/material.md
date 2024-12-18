实例：webgl_lights_physical
RGB通道的应用关系

 
## 基本属性
- .`alphaToCoverage` : Boolean
true // only works when WebGLRenderer's "antialias" is set to "true"
启用alpha to coverage. 只能在开启了MSAA的渲染环境中使用 (当渲染器创建的时候antialias 属性要true才能使用). 默认为 false

- `vertexColors ` : Boolean
是否使用顶点着色。默认值为false。 此引擎支持RGB或者RGBA两种顶点颜色，取决于缓冲 attribute 使用的是三分量（RGB）还是四分量（RGBA）。
当使用自定义缓冲几何体bufferGeometry时，给几何体添加材质时会用到

- `needUpdate`: Boolean
材质属性发生改变，需要调用needUpdate方法

如果材质销毁不用了UPdate
```js
lineSegments.geometry.dispose();
mesh.geometry.dispose();
mesh.material.dispose();
if ( mesh.material.map ) mesh.material.map.dispose();

lineSegments.geometry = new THREE.WireframeGeometry( geometry );
mesh.geometry = geometry;
mesh.material = new THREE.MeshPhongMaterial( { color: 0xffffff, side: THREE.DoubleSide } );
mesh.material.map = data.texture ? texture : null;
```

- `sizeAttenuation` : Boolean
指定点的大小是否因相机深度而衰减。（仅限透视摄像头。）默认为true。

- `.clipIntersection : Boolean`
更改剪裁平面的行为，以便仅剪切其交叉点，而不是它们的并集。默认值为 false。

- `.alphaTest: Float`
与透明度opcity有关，不透明度低于此值，则不会渲染材质， 指rgba中的a或者说opcity的值小于这个这个值不会渲染

- `.colorWrite `: Boolean
是否渲染材质的颜色。 这可以与网格的renderOrder属性结合使用，以创建遮挡其他对象的不可见对象。默认值为true。

随机设置颜色的方法
```js
color.setHex(Math.random() * 0xfffffff)
```

## GLTF材质
删除法线
```js
new GLTFLoader().load( 'models/gltf/Nefertiti/Nefertiti.glb', function ( gltf ) {
	gltf.scene.traverse( function ( child ) {
		if ( child.isMesh ) {
			// glTF currently supports only tangent-space normal maps.
			// this model has been modified to demonstrate the use of an object-space normal map.
			child.material.normalMapType = THREE.ObjectSpaceNormalMap;
			// attribute normals are not required with an object-space normal map. remove them.
			child.geometry.deleteAttribute( 'normal' );
			child.material.side = THREE.DoubleSide;
			child.scale.multiplyScalar( 0.5 );
			// recenter
			new THREE.Box3().setFromObject( child ).getCenter( child.position ).multiplyScalar( - 1 );
			scene.add( child );
		}
	} );
} );
```


### 贴图
基础材质具有的贴图属性：

- `alphaMap: 透明度贴图`
可以使得物体表面从不透明转为透明，黑色与白色通道，黑色为完全透明，需要开启alpha通道

- `aoMap: 环境光遮挡贴图`
环境光遮蔽贴图用于模拟光线在紧密空间内的散射和阻挡效果，增强了局部阴影的深度和细节。它通常是一张灰度图，暗部表示光线难以到达的区域，从而在这些区域产生更深的阴影。

- `.lightMap : Texture`
光照贴图。默认值为null。lightMap需要第二组UV。用来模拟光照，提升亮度

- `specularMap : Texture`
材质使用的高光贴图。默认值为null
- `.reflectivity : Float`
环境贴图对表面的影响程度，反射率，范围[0, 1],从漫反射到镜面反射

Lambert网格材质:
一种非光泽表面的材质，没有镜面高光。

- `.bumpMap : Texture`
凹凸实际上不会影响对象的几何形状，只影响光照。如果定义了法线贴图，则将忽略该贴图,从视觉上显示凹凸效果

- `.displacementMap : Texture`
移位的顶点可以投射阴影，阻挡其他对象

- `.normalMap : Texture`
会影响物体实际表面法线

MeshPhoneMatrial高光材质
在没有灯光下，兰伯特材质是黑得

- `.shininess : Float`
.specular高亮的程度，越高的值越闪亮。默认值为 30

- `specular: Color`
材质的高光颜色
与 shininess一起使用

- `.needUpdate`：Boolean
贴图属性发生了变化需要手动更新
```js
const groundGeometry = new THREE.PlaneGeometry( 20, 20, 10, 10 );
	const groundMaterial = new THREE.MeshBasicMaterial( { color: 0xcccccc } );
	const ground = new THREE.Mesh( groundGeometry, groundMaterial );
	ground.rotation.x = Math.PI * - 0.5;
	scene.add( ground );

	const textureLoader = new THREE.TextureLoader();
	textureLoader.load( 'textures/floors/FloorsCheckerboard_S_Diffuse.jpg', function ( map ) {

		map.wrapS = THREE.RepeatWrapping;
		map.wrapT = THREE.RepeatWrapping;
		map.anisotropy = 16;
		map.repeat.set( 4, 4 );
		groundMaterial.map = map;
		groundMaterial.needsUpdate = true;

	} );
	```
## 材质类型
### PBER材质
**MeshPhysicalMaterial**物理网格材质
Clearcoat: 有些类似于车漆，碳纤，被水打湿的表面的材质需要在面上再增加一个透明的，具有一定反光特性的面。而且这个面说不定有一定的起伏与粗糙度。Clearcoat可以在不需要重新创建一个透明的面的情况下做到类似的效果。 
范围从0.0到1.0m 

- `.clearcoatRoughness : Float`  
clear coat层的粗糙度，由0.0到1.0。 默认为0.0
- `.sheen : Float`
光泽层的强度,范围是0.0到1.0。默认为0.0。

```js
const bodyMaterial = new THREE.MeshPhysicalMaterial( {
	color: 0xff0000, 
	metalness: 1.0, 
	roughness: 0.5,
	clearcoat: 1.0, 
	clearcoatRoughness: 0.03, 
	sheen: 0.5
} );
```

玻璃材质实例：webgl_postprocessing_advanced
辉光材质实例：webgl_loader_gltf_sheen

- `.transmission : Float`
透光率（或者说透光性），范围从0.0到1.0。默认值是0.0。  
很薄的透明或者半透明的塑料、玻璃材质即便在几乎完全透明的情况下仍旧会保留反射的光线，透光性属性用于这种类型的材质。  
当透光率不为0的时候, opacity透明度应设置为1.
```js
const glassMaterial = new THREE.MeshPhysicalMaterial( {
	color: 0xffffff,
	 metalness: 0.25,
	  roughness: 0, 
	  transmission: 1.0
} );
```

磨砂玻璃材质
```js
const params = {
    color: 0xffffff,
    //类似透明度
    transmission: 0.9,
    opacity: 1,
    //金属度
    metalness: 0,
    //粗糙
    roughness: 0,
    //折射率
    ior: 1.52,
    //厚度 透过看物体的模糊程度
    thickness: 0.8,
    //镜面强度
    specularIntensity: 1,
    //镜面颜色
    specularColor: new Color("#ffffff"),
    //光强度
    lightIntensity: 1
};
{
    "color": 0xffffff,
    "transmission": 0.9063505503810331,
    "opacity": 0.9713801862828112,
    "metalness": 0,
    "roughness": 0.45114309906858596,
    "ior": 1.52,
    "thickness": 0.8,
    "specularIntensity": 1
}
```

## Color
`HSL`：颜色、饱和度、亮度
`setHSL(0.01+0.1* (i/count), 1.0, 0.5)`
`mesh.setColorAt( i, color.setScalar( 0.1 + 0.9 * Math.random() ) );`

随机颜色：color*Math.random()

`.toArray ( array : Array, offset : Integer ) : Array`
array - 存储颜色的可选数组, 这个可以是一个数组，也可以是一个attributes
offset - 数组的可选偏移量
返回一个格式为[ r, g, b ] 数组

**遍历循环给buffergeometry设置color**
```js
for ( let i = 0, l = positionAttribute.count; i < l; i ++ ) {
	// `HSL`：颜色、饱和度、亮度
	color.setHSL( 0.01 + 0.1 * ( i / l ), 1.0, 0.5 );
	color.toArray( colors, i * 3 );
	sizes[ i ] = PARTICLE_SIZE * 0.5;
}
```

- `color.equals(white)`
用来判断两个颜色是否相等

- `getStyle`
Threejs中的Color转Css中的Color


## 点Point材质
- `.size : Number`
设置点的大小。默认值为1.0。

```js
new THREE.PointsMaterial( {
	color: 0xFFFFFF,
	size: 3,
	blending: THREE.AdditiveBlending,
	transparent: true,
	sizeAttenuation: false
} );
```

## ShderMaterail自定义Shader
`UV`框架帮助我们从`Geometry`中获取变量
1. 给BufferGeometry设置setAttribut('size')属性
2. Material中定义GSLS

自定义extensions扩展属性，对材质进行扩展
uniform中的值发生边化

## wireframeMaterial线框材质
给mesh表面添加线框
```js
	let mesh = new THREE.Mesh( geometry1, material );
	mesh.add( wireframe );

```

```js
const material = new THREE.ShaderMaterial( {

	uniforms: {

		time: { value: 1.0 },
		resolution: { value: new THREE.Vector2() }

	},

	vertexShader: document.getElementById( 'vertexShader' ).textContent,

	fragmentShader: document.getElementById( 'fragmentShader' ).textContent

} );
```

```js
// 光线投射
// 动画更新自定义点，获取点的索引（给每个顶点设置大小）
// 让上一个点恢复原状，捕获的点扩大1.25
attributes.size.array[ INTERSECTED ] = PARTICLE_SIZE;

INTERSECTED = intersects[ 0 ].index;

attributes.size.array[ INTERSECTED ] = PARTICLE_SIZE * 1.25;
attributes.size.needsUpdate = true;
```

## 实例

### WaterShader
两种水的Shader: 太阳光照的水、清水

1. 清水，flowMap控制清水的流向
```js
	water = new Water( waterGeometry, {
		scale: 2,
		textureWidth: 1024,
		textureHeight: 1024,
		flowMap: flowMap
	} );
```

## THREEJS设置线宽无效问题

在编写Three.js的时候，设置线模型Line对应线材质LineBasicMaterial的线宽属性lineWidth，是无效的。官方解释

> Due to limitations of the OpenGL Core Profile with the WebGL renderer on most platforms linewidth will always be 1 regardless of the set value.

即由于opengl核心库文件的限制，webgl渲染器在大部分平台上linewidth一直是1，无视你设置的值；

这里通过Line2方式解决

1. 导入相关包
```js
import * as THREE from 'three'
import OrbitControls from 'three-orbitcontrols'
import { Line2 } from 'three/examples/jsm/lines/Line2'
import { LineGeometry } from 'three/examples/jsm/lines/LineGeometry'
import { LineMaterial } from 'three/examples/jsm/lines/LineMaterial'
```
2. 创建线条模型

```js
var geometry = new LineGeometry()
var pointArr = [-100, 0, 0,-100, 100, 0]
geometry.setPositions(pointArr)
var material = new LineMaterial({
        color: "red",
        linewidth: 15
      })
material.resolution.set(window.innerWidth, window.innerHeight)
var line = new Line2(geometry, material)
line.computeLineDistances()
scene.add(line)
```

3. 实例
```js
function render() {
	let material = mesh.material;
	switch ( params.material ) {
		case 'standard': material = materialStandard; break;
		case 'depthBasic': material = materialDepthBasic; break;
		case 'depthRGBA': material = materialDepthRGBA; break;
		case 'normal': material = materialNormal; break;
		case 'velocity': material = materialVelocity; break;
	}
}

// 手动更新纹理贴图
sphereMaterial.envMap = textureCube;
sphereMaterial.needsUpdate = true;
```

> 材质渲染过程中可以通过赋值的方式直接进行替换更新,不需要手动更新材质。更换纹理贴图需要手动更新

