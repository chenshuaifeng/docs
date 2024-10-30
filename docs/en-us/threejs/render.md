- `.toneMappingExposure : Number`
色调映射的曝光级别。默认是1,调节整个场景的明暗程度： 白天黑夜的效果
夜幕降临的效果用：toneMappingExposure,环境贴图必须是hrd文件   .exr文件
实例：webgl_materials_cubemap_dynamic

```js
renderer.toneMappingExposure = Math.pow( params.exposure, 5.0 )
	new THREE.TextureLoader().load( 'textures/equirectangular.png', function ( texture ) {
        texture.mapping = THREE.EquirectangularReflectionMapping;
        texture.encoding = THREE.sRGBEncoding;
        pngCubeRenderTarget = pmremGenerator.fromEquirectangular( texture );
        pngBackground = texture;
    } );
    const pmremGenerator = new THREE.PMREMGenerator( renderer );
    pmremGenerator.compileEquirectangularShader();
```
- `.getContext`
返回当前WebGL环境

- `.autoClear`

- `.autoClearColor `
renderer是否清除颜色缓存。 默认是true  
实例：webgl_postprocessing_crossfade  
- `.setClearColor()`
设置场景背景颜色及其透明度 

- `.autoClearStencil : Boolean`
清除模板缓存


- `.anisotropy`
```js
texture.anisotropy = renderer.capabilities.getMaxAnisotropy();
```

```js
// 实例：webgl_buffergeometry_glbufferattribute
const gl = renderer.getContext();
const pos = gl.createBuffer();
gl.bindBuffer( gl.ARRAY_BUFFER, pos );
gl.bufferData( gl.ARRAY_BUFFER, new Float32Array( positions ), gl.STATIC_DRAW );

const pos2 = gl.createBuffer();
gl.bindBuffer( gl.ARRAY_BUFFER, pos2 );
gl.bufferData( gl.ARRAY_BUFFER, new Float32Array( positions2 ), gl.STATIC_DRAW );

const rgb = gl.createBuffer();
gl.bindBuffer( gl.ARRAY_BUFFER, rgb );
gl.bufferData( gl.ARRAY_BUFFER, new Float32Array( colors ), gl.STATIC_DRAW );

const posAttr1 = new THREE.GLBufferAttribute( pos, gl.FLOAT, 3, 4, particles );
const posAttr2 = new THREE.GLBufferAttribute( pos2, gl.FLOAT, 3, 4, particles );
geometry.setAttribute( 'position', posAttr1 );

```
1. 返回webGL上下文
2. 绑定缓冲区
3. 为当前缓冲区绑定数据
4. 为bufferGeometry生成attribute
最终生成attribute,次attribute与buffer绑定


- `.copyFramebufferToTexture ( position : Vector2, texture : FramebufferTexture, level : Number )` : undefined
将当前WebGLFramebuffer中的像素复制到2D纹理中。   
可访问WebGLRenderingContext.copyTexImage2D.  

**纹理帧+渲染器重复渲染**


```js
function animate() {

    requestAnimationFrame( animate );

    const colorAttribute = line.geometry.getAttribute( 'color' );
    updateColors( colorAttribute );

    // scene rendering

    renderer.clear();
    renderer.render( scene, camera );

    // calculate start position for copying data

    vector.x = ( window.innerWidth * dpr / 2 ) - ( textureSize / 2 );
    vector.y = ( window.innerHeight * dpr / 2 ) - ( textureSize / 2 );

    renderer.copyFramebufferToTexture( vector, texture );

    renderer.clearDepth();
    renderer.render( sceneOrtho, cameraOrtho );

}
```
### 分屏渲染
```js
function render() {
    updateSize();
    for ( let ii = 0; ii < views.length; ++ ii ) {

        const view = views[ ii ];
        const camera = view.camera;

        view.updateCamera( camera, scene, mouseX, mouseY );

        const left = Math.floor( windowWidth * view.left );
        const bottom = Math.floor( windowHeight * view.bottom );
        const width = Math.floor( windowWidth * view.width );
        const height = Math.floor( windowHeight * view.height );

        renderer.setViewport( left, bottom, width, height );
        renderer.setScissor( left, bottom, width, height );
        renderer.setScissorTest( true );
        renderer.setClearColor( view.background );

        camera.aspect = width / height;
        camera.updateProjectionMatrix();

        renderer.render( scene, camera );

    }

}
```

## WebGLCubeRenderTarget

CubeCamera和WebGLRenderTarget一起使用：
1. 将渲染器作为cube相机的子对象添加到相机中
2. 从渲染器获取到场景材质，添加到物体材质的环境贴图中
3. 在动画函数中，当主相机位置发生变化，更新cube相机位置，更新cubeCamera

webglRender会调用renderer(scene, camera)进行场景模型渲染，这个webglCubeRenderTarget不用主要是获取环境纹理贴图给场景中的模型

```js
const cubeRenderTarget = new THREE.WebGLCubeRenderTarget( 1024 );
    mirrorSphereCamera = new THREE.CubeCamera( 0.05, 50, cubeRenderTarget );
    scene.add( mirrorSphereCamera );
    const mirrorSphereMaterial = new THREE.MeshBasicMaterial( { envMap: cubeRenderTarget.texture } );
    OOI.sphere.material = mirrorSphereMaterial;

// 动画
if ( OOI.sphere && mirrorSphereCamera ) {

    OOI.sphere.visible = false;
    OOI.sphere.getWorldPosition( mirrorSphereCamera.position );
    mirrorSphereCamera.update( renderer, scene );
    OOI.sphere.visible = true;

}

// 更新视图请注意,先让物体隐藏，更新后在打开
car.visible = false;
cubeCamera.position.copy( car.position );
cubeCamera.update( renderer, scene );

// Render the scene
car.visible = true;

```

## WebGLRenderTarge
render target是一个缓冲，就是在这个缓冲中，视频卡为正在后台渲染的场景绘制像素。 它用于不同的效果，例如用于在一个图像显示在屏幕上之前先做一些处理
实例：webgl_postprocessing_crossfade
WebGLRenderTarge用来渲染过度专场场景

转场的必要条件：
1. 多场景scene


使用插值的方式让控制器跟随场景中的物体移动,核心更新`orbitControls.tagert`

```js
if ( OOI.sphere && conf.followSphere ) {

				// orbitControls follows the sphere
				OOI.sphere.getWorldPosition( v0 );
				orbitControls.target.lerp( v0, 0.1 );

			}
```

## CSS2DRender渲染器 



```js
    import { CSS2DRenderer, CSS2DObject } from 'three/addons/renderers/CSS2DRenderer.js';
    // 1. 创建dom元素
    const text = document.createElement( 'div' );
    text.className = 'label';
    text.style.color = 'rgb(' + atom[ 3 ][ 0 ] + ',' + atom[ 3 ][ 1 ] + ',' + atom[ 3 ][ 2 ] + ')';
    text.textContent = atom[ 4 ];

    // 2. 创建2D对象将元素添加到Object中
    const label = new CSS2DObject( text );
    label.position.copy( object.position );

    // 3. 调用render渲染,拿到DOM添加样式
    labelRenderer = new CSS2DRenderer();
    labelRenderer.setSize( window.innerWidth, window.innerHeight );
    labelRenderer.domElement.style.position = 'absolute';
    labelRenderer.domElement.style.top = '0px';
    labelRenderer.domElement.style.pointerEvents = 'none';
    document.getElementById( 'container' ).appendChild( labelRenderer.domElement );

```

## SVGRenderer
示例：svg_lines
SVG也有一些十分重要的限制：
没有高级的着色器\不支持纹理\不支持阴影  
SVGrenderer可以用做粗的线  
**分段数与弧度在曲线中的应用---获取顶点**
```js
const vertices = [];
const divisions = 50;
for ( let i = 0; i <= divisions; i ++ ) {
    const v = ( i / divisions ) * ( Math.PI * 2 );
    const x = Math.sin( v );
    const z = Math.cos( v );
    vertices.push( x, 0, z );
}
const geometry = new THREE.BufferGeometry();
geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices, 3 ) );
```


## CSS3D与正交相机
通过相机的setViewOffset的核心方法来改变视图比例
```js
function updateCameraViewOffset( { fullWidth, fullHeight, x, y, width, height } ) {

				if ( ! camera.view ) {

					camera.setViewOffset( fullWidth || window.innerWidth, fullHeight || window.innerHeight, x || 0, y || 0, width || window.innerWidth, height || window.innerHeight );

				} else {

					camera.setViewOffset( fullWidth || camera.view.fullWidth, fullHeight || camera.view.fullHeight, x || camera.view.offsetX, y || camera.view.offsetY, width || camera.view.width, height || camera.view.height );

				}

			} 
```