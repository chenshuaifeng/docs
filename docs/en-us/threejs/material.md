## 基本属性

- `vertexColors ` : Boolean
是否使用顶点着色。默认值为false。 此引擎支持RGB或者RGBA两种顶点颜色，取决于缓冲 attribute 使用的是三分量（RGB）还是四分量（RGBA）。
当使用自定义缓冲几何体bufferGeometry时，给几何体添加材质时会用到



- `sizeAttenuation` : Boolean
指定点的大小是否因相机深度而衰减。（仅限透视摄像头。）默认为true。

- `.clipIntersection : Boolean`
更改剪裁平面的行为，以便仅剪切其交叉点，而不是它们的并集。默认值为 false。

- `.alphaTest: Float`
不透明度低于此值，则不会渲染材质， 指rgba中的a或者说opcity的值小于这个这个值不会渲染


随机设置颜色的方法
```js
color.setHex(Math.random() * 0xfffffff)
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

## 物理引擎ammo.js

遇到的问题是引擎引不进，解决办法通过`script`标签引入脚本

```js
// 物理引擎ammo.js的示例
physics_ammo_instancing
```

## Color
`HSL`：颜色、饱和度、亮度
setHSL(0.01+0.1* (i/count), 1.0, 0.5)

随机颜色：color*Math.random()

`.toArray ( array : Array, offset : Integer ) : Array`
array - 存储颜色的可选数组
offset - 数组的可选偏移量
返回一个格式为[ r, g, b ] 数组

**遍历循环给buffergeometry设置color**
```js
// 传入colors给colors赋值
color.toArray(colors, 3 * i)
```


## ShderMaterail自定义Shader
`UV`框架帮助我们从`Geometry`中获取变量
1. 给BufferGeometry设置setAttribut('size')属性
2. Material中定义GSLS


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
