
### 聚光灯SportLight

- `angle`: Float
光线照射范围的角度，用弧度表示。不应超过 Math.PI/2。默认值为 Math.PI/3。
圆的大小

- `penumbera:Float` 
光圆边缘模糊程度，值越大越模糊



## 光线技术

###  光照探针技术Lightprobe
1. 创建实例
```js
    lightProbe = new THREE.LightProbe();
	scene.add( lightProbe );
```

2. 实例化探针生成器并获取纹理
```js
	import { LightProbeGenerator } from 'three/addons/lights/LightProbeGenerator.js';

    const texture = LightProbeGenerator.fromCubeTexture( cubeTexture )
```

3. 将纹理拷贝到关照探针中
```js
lightProbe.copy(texture)
```

### PMREMGenerator 环境光
THREEJS内置的环境光纹理shdow
PMREM 是一种用于物理渲染（特别是基于物理的材质和光照）的优化技术，它允许你以较低的成本在多个不同的粗糙度级别上模拟光照
```JS
// 创建PMREM实例
const pmremGenerator = new THREE.PMREMGenerator( renderer );

// 获取纹理
scene.environment = pmremGenerator.fromScene( new RoomEnvironment( renderer ), 0.04 ).texture;


scene.en
vironment = pmrem;
```
## RectAreaLight平面光光源
1. 不支持阴影。
2. 只支持 MeshStandardMaterial 和 MeshPhysicalMaterial 两种材质。
3. 你必须在你的场景中加入 RectAreaLightUniformsLib，并调用 init()。
