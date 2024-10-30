## 基本属性
- `.envMap`： 环境贴图
与环境贴图常在一起使用的是`mapping`属性
```js
const refractionCube = new THREE.CubeTextureLoader().load( urls );
refractionCube.mapping = THREE.CubeRefractionMapping;
```
- `repeat`: Vector2
- `offset`: Vector2

纹理贴图得示例：webgl_materials_texture_rotation

`- .refractionRatio : Float`
示例：webgl_materials_cubemap
折射率，空气的折射率（IOR）（约为1）除以材质的折射率。它与环境映射模式THREE.CubeRefractionMapping 和THREE.EquirectangularRefractionMapping一起使用。   折射率不应超过1。默认值为0.98。 取值【0，1】
与`mapping`一起使用,值越大越像水晶/玻璃透明


- `.reflectivity : Float`
环境贴图对表面的影响程度; 见.combine。默认值为1，有效范围介于0（无反射）和1（完全反射）之间。

塑料质感
```js
const cubeMaterial3 = new THREE.MeshLambertMaterial( { color: 0xff6600, envMap: reflectionCube, combine: THREE.MixOperation, reflectivity: 0.3 } );
```
总结：envMap\refractionRatio\reflectivity\mapping

- `colorSpace`: 纹理的颜色空间
```js
THREE.NoColorSpace = ""
THREE.SRGBColorSpace = "srgb"
THREE.LinearSRGBColorSpace = "srgb-linear"
```
纹理空间发生改变时需要重新更新纹理。`texture.needsUpdates = ture`

- `map`：贴图
漫反射材质、镜面反射材质需要光照才能显示出效果

- `.mapping`： number
图像将如何应用到物体（对象）上。默认值是THREE.UVMapping对象类型， 即UV坐标将被用于纹理映射。

实例：webgl_materials_envmaps
```js
	if ( value ) {
    textureEquirec.mapping = THREE.EquirectangularRefractionMapping;
    textureCube.mapping = THREE.CubeRefractionMapping;
    } else {
    textureEquirec.mapping = THREE.EquirectangularReflectionMapping;
    textureCube.mapping = THREE.CubeReflectionMapping;
    }
    sphereMaterial.needsUpdate = true;
```
- `.mipmaps `: Array
用户所给定的mipmap数组（可选）
示例：webgl_materials_cubemap_mipmaps

- `vertexColors ` : Boolean
是否使用顶点着色。默认值为false。 此引擎支持RGB或者RGBA两种顶点颜色，取决于缓冲 attribute 使用的是三分量（RGB）还是四分量（RGBA）。


> 纹理的三个核心API: offset\repeat\center  
texture.wrapS = texture.wrapT = THREE.RepeatWrapping;  
这个属性当repeat 大于1时，此时纹理图片缩小，一个面平铺好几个。使用这个属性铺满全平面  
或者offset偏移后也能够铺满全平面
```js
// 纹理平铺模式
// 纹理像素
texture.repeat.set( 0.008, 0.008 );
```
**纹理重复基础原理：**
repeat(x,y) 是指在x,y轴展开多少个纹理

**offset偏移原理**
当 repeat=（1，1）, 偏移x偏移0.1时，x轴纹理向左移动0.1个单位，y偏移0.1时，y轴向下偏移0.1个单位
offset是向XY的负方向偏移
## 精灵（Sprite）动画帧FramebufferTexture
精灵是一个总是面朝着摄像机的平面，通常含有使用一个半透明的纹理。

动画帧纹理是将屏幕中的区域进行裁剪投影
开启顶点着色
```js
	texture = new THREE.FramebufferTexture( textureSize, textureSize );

    const spriteMaterial = new THREE.SpriteMaterial( { map: texture } );
    sprite = new THREE.Sprite( spriteMaterial );
    sprite.scale.set( textureSize, textureSize, 1 );
    sceneOrtho.add( sprite );

    // 屏幕位置算法
    const halfWidth = window.innerWidth / 2;
    const halfHeight = window.innerHeight / 2;

    const halfImageWidth = textureSize / 2;
    const halfImageHeight = textureSize / 2;

    sprite.position.set(  halfWidth - halfImageWidth, halfHeight - halfImageHeight, 1 );

    // 动画
    function updateColors( colorAttribute ) {

        const l = colorAttribute.count;

        for ( let i = 0; i < l; i ++ ) {
            // 从【0， 1】得递增函数
            const h = ( ( offset + i ) % l ) / l;

            color.setHSL( h, 1, 0.5 );
            colorAttribute.setX( i, color.r );
            colorAttribute.setY( i, color.g );
            colorAttribute.setZ( i, color.b );

        }

        colorAttribute.needsUpdate = true;

        offset -= 25;
    }

    // 渲染
    // 1. 清除画布
    // 2. 渲染普通场景
    // 3. 设置渲染区域,将需要渲染得区域拷贝到纹理中
    // 4. 清除深度缓冲区并渲染
    renderer.clear();
    renderer.render( scene, camera );

    // calculate start position for copying data

    vector.x = ( window.innerWidth * dpr / 2 ) - ( textureSize / 2 );
    vector.y = ( window.innerHeight * dpr / 2 ) - ( textureSize / 2 );

    renderer.copyFramebufferToTexture( texture, vector );

    renderer.clearDepth();
    renderer.render( sceneOrtho, cameraOrtho );
 ```

 纹理映射模式
 图形如何应用到物体对象上
 1. 默认值通过UV坐标映射到物体上

 THREE.UVMapping
THREE.CubeReflectionMapping
THREE.CubeRefractionMapping
THREE.EquirectangularReflectionMapping
THREE.EquirectangularRefractionMapping
THREE.CubeUVReflectionMapping

 **环境贴图投影**
运用于物体紧贴地面的场景
 ```js
import { GroundProjectedEnv } from 'three/addons/objects/GroundProjectedEnv.js';

 env = new GroundProjectedEnv( envMap );
env.scale.setScalar( 100 );
scene.add( env );

const shadow = new THREE.TextureLoader().load( 'models/gltf/ferrari_ao.png' );
const mesh = new THREE.Mesh(
    new THREE.PlaneGeometry( 0.655 * 4, 1.3 * 4 ),
    new THREE.MeshBasicMaterial( {
        map: shadow, blending: THREE.MultiplyBlending, toneMapped: false, transparent: true
    } )
);
mesh.rotation.x = - Math.PI / 2;
mesh.renderOrder = 2;
carModel.add( mesh );

function render() {
    renderer.render( scene, camera );
    env.radius = params.radius;
    env.height = params.height;
}
```
总结：使用内置得shader将环境贴图：GroundProjectedEnv， 通过控制env得高度来更新

光源贴图：
webgl_materials_lightmap


glTF目前仅支持切线空间法线贴图
```js
new GLTFLoader().load( 'models/gltf/Nefertiti/Nefertiti.glb', function ( gltf ) {
    gltf.scene.traverse( function ( child ) {
        if ( child.isMesh ) {
            console.log(child)
            // glTF currently supports only tangent-space normal maps.
            // this model has been modified to demonstrate the use of an object-space normal map.
            child.material.normalMapType = THREE.ObjectSpaceNormalMap;
            // attribute normals are not required with an object-space normal map. remove them.
            child.geometry.deleteAttribute( 'normal' );
            child.material.side = THREE.DoubleSide;
            child.scale.multiplyScalar( 0.5 );
            // 移动物体位置
            new THREE.Box3().setFromObject( child ).getCenter( child.position ).multiplyScalar( - 1 );
            scene.add( child );
        }
    } );
    render();
} );
```

材质实例：webgl_materials_physical_clearcoat
有法线贴图图片

材质实例：webgl_materials_physical_reflectivity
钻石材质

## 代码示例
### 星空图
![angular-question3](../../static/images/texture_starts.png ':size=800')

```js
// 初始化场景星星的效果
const initSceneStar=()=>{
    // 自定义缓冲几何体创建星星
    const geometry = new THREE.BufferGeometry()
    // 星星位置的坐标
    const vertices = []
    // 创建纹理
    const textureLoader =new THREE.TextureLoader()
    const spritel = textureLoader.load(IMAGE STAR1const)
    sprite2 = textureLoader.load(IMAGE STAR2)
    // 声明点的参数
    parameters = [
    [[0.6，100，0.75],sprite1，50],[[0,0,1],sprite2,20]
 ]
}
```
星星是一个点，由一个顶点组成，点材质。

> 颜色随时间变化的算法-蓝白之间的交替，实质是材质color随时间变化的函数

```js
// 渲染星星的运动，在动画函数中调用
const renderStarMove=()=>{
    // 星星颜色交替变化
    // shl颜色范围0~1
    const time = Date.now()*0.00005
    for(let i=0;i< parameters.length;i++){
    const color = parameters[i][0]
    const h=((360*(color[0]+time))% 360)/ 360
    materials[i].color.setHsL(color[0],color[1],parseFloa(h.toFixed(2)))
    }
}
```

> 星星的运动轨迹

运动需要考虑几个变量：时间、速度、位移、初始位置
核心是通过对物体mesh的setZ()来改变物体的远近，z随时间变化的函数

```js
// 星星的运动
zprogress += 1
if(zprogress >= zAxisNumber + depth /2){
    zprogress = particles init position
}else {
    particles first.forEach(item =>{
        item.position.setZ(zprogress)
    })
}
```


> 一个随机分布的算法
```js
    const x = Math.random() * n - n2;
    const y = Math.random() * n - n2;
    const z = Math.random() * n - n2;

    const ax = x + Math.random() * d - d2;
    const ay = y + Math.random() * d - d2;
    const az = z + Math.random() * d - d2;

    const bx = x + Math.random() * d - d2;
    const by = y + Math.random() * d - d2;
    const bz = z + Math.random() * d - d2;

    const cx = x + Math.random() * d - d2;
    const cy = y + Math.random() * d - d2;
    const cz = z + Math.random() * d - d2;

    positions.push( ax, ay, az );
    positions.push( bx, by, bz );
    positions.push( cx, cy, cz );
```
- `blending`混合
```js
THREE.NoBlending
THREE.NormalBlending
THREE.AdditiveBlending // 贴图效果的混合
THREE.SubtractiveBlending
THREE.MultiplyBlending
THREE.CustomBlending
```

控制纹理平铺时的状态

```js
texture.anisotropy = 16;
texture.repeat.set(4, 4);
```

### 镜面反射效果
![angular-question3](../../static/images/reflector.png ':size=800')

```js
let reflectorGeometry = new THREE.PlaneBufferGeometry(100, 16)
let reflectorplane = new Reflector(reflectorGeometry,{
    textureWidth: window.innerWidth,
    textureHeight: window.innerHeight,
    color:0x332222
})
reflectorPlane.rotation.x=-Math.PI /2;
scene.add(reflectorPlane);
```

```js
  let lightPillarMaterial = new THREE.MeshBasicMaterial({
      color: 0xffffff,
      map: lightPillarTexture,
      alphaMap: lightPillarTexture,
      transparent: true,
      blending: THREE.AdditiveBlending,
      side: THREE.DoubleSide,
      depthWrite: false,
    });
```

## 自定义BufferGeometry中的texture
1. uniform
2. glsl


## 自定义ShaderMaterial的time属性
1. 在GLSL中定义time
```js
void main( void ) {

				vec2 position = - 1.0 + 2.0 * vUv;

				vec4 noise = texture2D( texture1, vUv );
				vec2 T1 = vUv + vec2( 1.5, - 1.5 ) * time * 0.02;
				vec2 T2 = vUv + vec2( - 0.5, 2.0 ) * time * 0.01;

				T1.x += noise.x * 2.0;
				T1.y += noise.y * 2.0;
				T2.x -= noise.y * 0.2;
				T2.y += noise.z * 0.2;

				float p = texture2D( texture1, T1 * 2.0 ).a;

				vec4 color = texture2D( texture2, T2 * 2.0 );
				vec4 temp = color * ( vec4( p, p, p, p ) * 2.0 ) + ( color * color - 0.1 );

				if( temp.r > 1.0 ) { temp.bg += clamp( temp.r - 2.0, 0.0, 100.0 ); }
				if( temp.g > 1.0 ) { temp.rb += temp.g - 1.0; }
				if( temp.b > 1.0 ) { temp.rg += temp.b - 1.0; }

				gl_FragColor = temp;

				float depth = gl_FragCoord.z / gl_FragCoord.w;
				const float LOG2 = 1.442695;
				float fogFactor = exp2( - fogDensity * fogDensity * depth * depth * LOG2 );
				fogFactor = 1.0 - clamp( fogFactor, 0.0, 1.0 );

				gl_FragColor = mix( gl_FragColor, vec4( fogColor, gl_FragColor.w ), fogFactor );

			}

```
2. uniform 中初始化time

3. animate中出入time值

```js
uniforms[ 'time' ].value += 0.2 * delta;
```
## DDS文件
DDS文件，全称为DirectDraw Surface，是DirectX纹理压缩（DirectX Texture Compression，简称DXTC）的产物，由NVIDIA公司开发。这种文件格式主要用于存储纹理数据，广泛应用于Microsoft DirectX游戏和应用程序中，特别是在大部分3D游戏引擎中，DDS格式的图片常被用作贴图或制作法线贴图。

DDS文件的特点

DDS文件记录了一张图片的信息，包括颜色、纹理等数据。
它支持多种压缩格式，如DXT1、DXT3、DXT5等，这些压缩格式能够显著减少文件大小，同时保持较好的图像质量。
DDS文件通常用于高性能的图形处理场景，如游戏开发、虚拟现实等。
DDS文件的打开方式

DDS文件可以通过多种方法打开和编辑，以下是一些常见的打开方式：

使用Microsoft DirectX SDK工具：
DDSView：用于查看DDS文件的内容以及其元数据。
TexConv：用于将DDS文件转换为其他纹理格式。
TexMerge：用于合并多个DDS文件到一个文件中。
使用图像编辑软件：
Adobe Photoshop：通过安装Nvidia Plug-ins for Adobe Photoshop插件，可以在Photoshop中打开和编辑DDS文件。
GIMP、Paint.NET、Krita等图像编辑软件也支持打开和编辑DDS文件，但可能需要安装相应的插件或支持库。
使用第三方工具：
DDS Plugin for Windows Explorer：允许在Windows资源管理器中预览DDS文件的缩略图。
DDS Texture Viewer：用于查看DDS文件并提取其纹理数据。
DDS Converter：用于将DDS文件转换为其他纹理格式。
其他工具：
DDS Thumbnail Viewer：一个免费的小工具，可以在Windows平台下查看DDS文件的缩略图，无需打开文件即可预览内容。
3D MAX DDS插件：允许在3D MAX中打开和编辑DDS文件。
DDS文件的应用场景

DDS文件因其高效的压缩算法和广泛的应用支持，在游戏开发、虚拟现实、建筑设计、动画制作等领域有着广泛的应用。特别是在游戏开发中，DDS文件作为纹理贴图的主要格式之一，对于提高游戏画面的质量和性能具有重要作用。

综上所述，DDS文件是一种重要的图形文件格式，具有广泛的应用场景和灵活的打开方式。随着图形技术的不断发展，DDS文件在未来仍将继续发挥重要作用。

## lottie 纹理动画

## 帧缓冲纹理
FramebufferTexture
取一个变化的物体的某一帧,这个类只能与 `WebGLRenderer.copyFramebufferToTexture()` 结合使用。
```js
texture = new THREE.FramebufferTexture( textureSize, textureSize );

const vector = new Vector2();
vector.x = ( window.innerWidth * pixelRatio / 2 ) - ( textureSize / 2 );
vector.y = ( window.innerHeight * pixelRatio / 2 ) - ( textureSize / 2 );

// render the scene
renderer.clear();
renderer.render( scene, camera );

// copy part of the rendered frame into the framebuffer texture
renderer.copyFramebufferToTexture( frameTexture, vector );
```

## 贴图
凹凸贴图 bumpMap
1. 与法线贴图nomalMap冲突，有nomalMap则不生效
2. 黑色白色印象光照的感知深度，黑色的地方更黑


## UVsDebug

贴图的调试工具

这有助于调试几何体中的UV问题，并允许新用户可视化UV的内容。
threejs中缓冲几何体的UV坐标

实例:misc_uv_tests
```js
import { UVsDebug } from 'three/addons/utils/UVsDebug.js';
```