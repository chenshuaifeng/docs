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

### BloomPass
滤布效果

## GlitchPass
电脉冲干扰效果

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

## AfterimagePass
残影
```js
import { AfterimagePass } from 'three/addons/postprocessing/AfterimagePass.js';

```
##  RenderTransitionPass
转场
transition 动画起始时间显示的场景；动画结束时间显示的场景；
```js
import { RenderTransitionPass } from 'three/addons/postprocessing/RenderTransitionPass.js';

renderTransitionPass = new RenderTransitionPass( fxSceneA.scene, fxSceneA.camera, fxSceneB.scene, fxSceneB.camera );
renderTransitionPass.setTexture( textures[ 0 ] );

```