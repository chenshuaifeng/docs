后期效果就是自定义Shader的实现

## EffectComposer 后期处理
用于在three.js中实现后期处理效果。该类管理了产生最终视觉效果的后期处理过程链。 后期处理过程根据它们添加/插入的顺序来执行，最后一个过程会被自动渲染到屏幕上。

示例：webgl_materials_normalmap

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


##  webgl_effects_stereo
雨滴放大镜效果：

```js
import { StereoEffect } from 'three/addons/effects/StereoEffect.js';

// 1. 给场景添加背景纹理
scene.background = new THREE.CubeTextureLoader()
	.setPath( 'textures/cube/Park3Med/' )
	.load( [ 'px.jpg', 'nx.jpg', 'py.jpg', 'ny.jpg', 'pz.jpg', 'nz.jpg' ] );

// 2. 创建球体，并给球体添加相同的纹理贴图
envMap: textureCube
// 3. 折射率
 refractionRatio: 0.95 
textureCube.mapping = THREE.CubeRefractionMapping;

   // 1.创建实例
    effect = new StereoEffect( renderer );
    effect.setSize( width, height );

    // 2. 渲染
    effect.render( scene, camera );

```

## 分屏渲染技术

1. 依赖类导入及初始化
2. 创建分屏
```js
	fxaaPass = new ShaderPass( FXAAShader );

				const outputPass = new OutputPass();

				composer1 = new EffectComposer( renderer );
				composer1.addPass( renderPass );
				composer1.addPass( outputPass );

				//

				const pixelRatio = renderer.getPixelRatio();

				fxaaPass.material.uniforms[ 'resolution' ].value.x = 1 / ( container.offsetWidth * pixelRatio );
				fxaaPass.material.uniforms[ 'resolution' ].value.y = 1 / ( container.offsetHeight * pixelRatio );

				composer2 = new EffectComposer( renderer );
				composer2.addPass( renderPass );
				composer2.addPass( outputPass );
```
3. 分屏渲染
```js
// 剪裁测试（Scissor Test）的功能，
renderer.setScissorTest( true );

renderer.setScissor( 0, 0, halfWidth - 1, container.offsetHeight );
composer1.render();

renderer.setScissor( halfWidth, 0, halfWidth, container.offsetHeight );
composer2.render();

renderer.setScissorTest( false );
```
```js
function render() {

	camera.position.x += ( mouseX - camera.position.x ) * .05;
	camera.position.y += ( - ( mouseY - 200 ) - camera.position.y ) * .05;

	camera.lookAt( scene.position );

	renderer.clear();
	renderer.setScissorTest( true );

	renderer.setScissor( 0, 0, SCREEN_WIDTH / 2 - 2, SCREEN_HEIGHT );
	renderer.render( scene, camera );

	renderer.setScissor( SCREEN_WIDTH / 2, 0, SCREEN_WIDTH / 2 - 2, SCREEN_HEIGHT );
	renderer.render( scene2, camera );

	renderer.setScissorTest( false );

	}
```

## 天空
方案一：天空=SphereGeometry + CanvasTexture, canvas方案
```js
const canvas = document.createElement( 'canvas' );
canvas.width = 1;
canvas.height = 32;

const context = canvas.getContext( '2d' );
const gradient = context.createLinearGradient( 0, 0, 0, 32 );
gradient.addColorStop( 0.0, '#014a84' );
gradient.addColorStop( 0.5, '#0561a0' );
gradient.addColorStop( 1.0, '#437ab6' );
context.fillStyle = gradient;
context.fillRect( 0, 0, 1, 32 );

const skyMap = new THREE.CanvasTexture( canvas );
skyMap.colorSpace = THREE.SRGBColorSpace;

const sky = new THREE.Mesh(
	new THREE.SphereGeometry( 10 ),
	new THREE.MeshBasicMaterial( { map: skyMap, side: THREE.BackSide } )
);
scene.add( sky );
```

方案二： 自定义shader
```js
const vertexShader = document.getElementById( 'vertexShader' ).textContent;
	const fragmentShader = document.getElementById( 'fragmentShader' ).textContent;
	const uniforms = {
		'topColor': { value: new THREE.Color( 0x0077ff ) },
		'bottomColor': { value: new THREE.Color( 0xffffff ) },
		'offset': { value: 33 },
		'exponent': { value: 0.6 }
	};
	uniforms[ 'topColor' ].value.copy( hemiLight.color );

	scene.fog.color.copy( uniforms[ 'bottomColor' ].value );

	const skyGeo = new THREE.SphereGeometry( 4000, 32, 15 );
	const skyMat = new THREE.ShaderMaterial( {
		uniforms: uniforms,
		vertexShader: vertexShader,
		fragmentShader: fragmentShader,
		side: THREE.BackSide
	} );

	const sky = new THREE.Mesh( skyGeo, skyMat );
	scene.add( sky );
	```
方案三： 内置shader
特点：有太阳、明暗变化
```js
const sky = new Sky();
sky.scale.setScalar( 10000 );
scene.add( sky );

const skyUniforms = sky.material.uniforms;

skyUniforms[ 'turbidity' ].value = 10;
skyUniforms[ 'rayleigh' ].value = 2;
skyUniforms[ 'mieCoefficient' ].value = 0.005;
skyUniforms[ 'mieDirectionalG' ].value = 0.8;

```
