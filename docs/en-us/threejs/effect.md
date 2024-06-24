## EffectComposer 后期处理
用于在three.js中实现后期处理效果。该类管理了产生最终视觉效果的后期处理过程链。 后期处理过程根据它们添加/插入的顺序来执行，最后一个过程会被自动渲染到屏幕上。

```js
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';

composer = new EffectComposer( renderer );
composer.addPass( new RenderPass( scene, camera ) );

const effect1 = new ShaderPass( DotScreenShader );
effect1.uniforms[ 'scale' ].value = 4;
composer.addPass( effect1 );

const effect2 = new ShaderPass( RGBShiftShader );
effect2.uniforms[ 'amount' ].value = 0.0015;
composer.addPass( effect2 );

const effect3 = new OutputPass();
composer.addPass( effect3 );

composer.render();

```

代码步骤
1. new Composer实例，并将渲染器添加到composer中

2. new RenderPasss,renderPass渲染场景scene和camera, 并将renderPass实例添加到composer中

3. 添加后期处理通道

4. 使用composer.render进行场景渲染


### ShaderPass

着色器通道，功能类似于高级函数作用就，对自定义着色器进行包装

```js
const effect1 = new ShaderPass( DotScreenShader );
effect1.uniforms[ 'scale' ].value = 4;
composer.addPass( effect1 );
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

## AnaglyphEffect
浅浮雕效果:
![https://img.xakwy.com/threejs/docs/AnaglyphEffect.png](AnaglyphEffect)

使用方法与通道pass有稍微区别
```js
    // 1.创建实例
    effect = new AnaglyphEffect( renderer );
    effect.setSize( width, height );

    // 2. 渲染
    effect.render( scene, camera );
```