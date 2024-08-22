通道：一直后期处理技术

一般通用的三个
```js
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
import { OutputPass } from 'three/addons/postprocessing/OutputPass.js';
```

## 颜色空间通道
OutputPass
RGBShiftShader

### FilmPass 
电影效果

### FXAAShader
FXAA设计用于在转换为低动态范围和转换为sRGB颜色空间进行显示后，在引擎后处理结束时应

### BokehPass
一种类似高斯模糊的后期处理效果

### BloomPass
滤布效果

## GTAOPass
灰度做旧效果

## SMAAPass
线框的微弱发光效果，效果并不十分明显

## GlitchPass
电脉冲干扰效果

## UnrealBloomPass
不真实梦幻、加了一种模糊的效果
- threshold阈值 作用范围 【0， 1】由大变小
- strength强度 模糊程度
- radius圆角 模糊的影像范围
renderer.toneMappingExposure也会作用到


```js
    import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';
```

## outlinePass
patternTexture
edgeStrength,
edgeGlow,
edgeThickness,
pulsePeriod,
rotate,
usePatternTexture

## ShadePass
FXAAShader
消除锯齿
```js
effectFXAA.uniforms[ 'resolution' ].value.set( 1 / window.innerWidth, 1 / window.innerHeight );

```

## LuminosityShader
灰度,让物体颜色变灰

## SobelOperatorShader
高斯模糊滤镜


## MaskPass
蒙层通道:MaskPass\ClearMaskPass
关联通道TexturePass\ClearPass
```js
import { TexturePass } from 'three/addons/postprocessing/TexturePass.js';
import { ClearPass } from 'three/addons/postprocessing/ClearPass.js';
import { MaskPass, ClearMaskPass } from 'three/addons/postprocessing/MaskPass.js';
```
不同之处
1. 渲染器renderer：`setClearColor` \ `autoClear`
2. 两个场景scene1，scene2

总结：将纹理作为通道添加给渲染器，让网格物体去涂抹
```js
// 蒙版通道创建
const clearPass = new ClearPass();

const clearMaskPass = new ClearMaskPass();

const maskPass1 = new MaskPass( scene1, camera );
const maskPass2 = new MaskPass( scene2, camera );

// 渲染到模板缓冲区
const renderTarget = new THREE.WebGLRenderTarget( window.innerWidth, window.innerHeight, parameters );

```

## AfterimagePass
残影
```js
import { AfterimagePass } from 'three/addons/postprocessing/AfterimagePass.js';

```
##  RenderTransitionPass
转场效果的后期处理
transition 动画起始时间显示的场景；
动画结束时间显示的场景；
```js
import { RenderTransitionPass } from 'three/addons/postprocessing/RenderTransitionPass.js';

renderTransitionPass = new RenderTransitionPass( fxSceneA.scene, fxSceneA.camera, fxSceneB.scene, fxSceneB.camera );
renderTransitionPass.setTexture( textures[ 0 ] );

```

## 粒子特效
```js
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';
import { BloomPass } from 'three/addons/postprocessing/BloomPass.js';
import { FilmPass } from 'three/addons/postprocessing/FilmPass.js';
import { FocusShader } from 'three/addons/shaders/FocusShader.js';
import { OBJLoader } from 'three/addons/loaders/OBJLoader.js';
```