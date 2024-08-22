- `.toneMappingExposure : Number`
色调映射的曝光级别。默认是1,调节整个场景的明暗程度： 白天黑夜的效果
```js
renderer.toneMappingExposure = Math.pow( params.exposure, 5.0 )
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
SVG也有一些十分重要的限制：

没有高级的着色器
不支持纹理
不支持阴影

SVGrenderer可以用做粗的线


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