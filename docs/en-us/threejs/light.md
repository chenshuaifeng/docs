
### 聚光灯SportLight

- `angle`: Float
光线照射范围的角度，用弧度表示。不应超过 Math.PI/2。默认值为 Math.PI/3。
圆的大小

- `penumbera:Float` 
光圆边缘模糊程度，值越大越模糊

**折射Reflaction与反射Reflection**
Reflaction: 物体呈现透明色气泡
Reflection：物体呈现周围环境

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
**光照探针的原理是从环境中捕获光线，生成环境贴图**

### PMREMGenerator 环境光
THREEJS内置的环境光纹理shdow
PMREM 是一种用于物理渲染（特别是基于物理的材质和光照）的优化技术，它允许你以较低的成本在多个不同的粗糙度级别上模拟光照
```js
// 创建PMREM实例
const pmremGenerator = new THREE.PMREMGenerator( renderer );

// 获取纹理
scene.environment = pmremGenerator.fromScene( new RoomEnvironment( renderer ), 0.04 ).texture;
```
没有光照，光线因子从光线生成器中生成后贴图给物体的envMap

```js  

function createEnvironment() {

    const envScene = new THREE.Scene();
    envScene.background = new THREE.Color( COLOR );
    if ( renderer.outputEncoding === THREE.sRGBEncoding ) envScene.background.convertSRGBToLinear();

    const pmremGenerator = new THREE.PMREMGenerator( renderer );
    radianceMap = pmremGenerator.fromScene( envScene ).texture;
    pmremGenerator.dispose();

    scene.background = envScene.background;

}
```

```js
scene.en
vironment = pmrem;
```
## RectAreaLight平面光光源
1. 不支持阴影。
2. 只支持 MeshStandardMaterial 和 MeshPhysicalMaterial 两种材质。
3. 你必须在你的场景中加入 RectAreaLightUniformsLib，并调用 init()。


实例：webgl_materials_car
```js
scene.background = new THREE.Color( 0x333333 );
scene.environment = new RGBELoader().load( 'textures/equirectangular/venice_sunset_1k.hdr' );
scene.environment.mapping = THREE.EquirectangularReflectionMapping;
scene.fog = new THREE.Fog( 0x333333, 10, 15 );
```

## 模拟点光源及
```js
    const light = new THREE.PointLight( 0xffffff, 1.5, 2000, 0 );
    light.color.setHSL( h, s, l );
    light.position.set( x, y, z );
    scene.add( light );

    const lensflare = new Lensflare();
    lensflare.addElement( new LensflareElement( textureFlare0, 700, 0, light.color ) );
    lensflare.addElement( new LensflareElement( textureFlare3, 60, 0.6 ) );
    lensflare.addElement( new LensflareElement( textureFlare3, 70, 0.7 ) );
    lensflare.addElement( new LensflareElement( textureFlare3, 120, 0.9 ) );
    lensflare.addElement( new LensflareElement( textureFlare3, 70, 1 ) );
    light.add( lensflare );
```

## 模拟太阳光反射，气浪效果
webgl_refraction

```js
import { Refractor } from 'three/addons/objects/Refractor.js';
import { WaterRefractionShader } from 'three/addons/shaders/WaterRefractionShader.js';

const refractorGeometry = new THREE.PlaneGeometry( 90, 90 );
refractor = new Refractor( refractorGeometry, {
    color: 0x999999,
    textureWidth: 1024,
    textureHeight: 1024,
    shader: WaterRefractionShader
} );
refractor.position.set( 0, 50, 0 );
scene.add( refractor );

function animate() {
    requestAnimationFrame( animate );
    const time = clock.getElapsedTime();
    refractor.material.uniforms.time.value = time;
    smallSphere.position.set(
        Math.cos( time ) * 30,
        Math.abs( Math.cos( time * 2 ) ) * 20 + 5,
        Math.sin( time ) * 30
    );
    smallSphere.rotation.y = ( Math.PI / 2 ) - time;
    smallSphere.rotation.z = time * 8;
    renderer.render( scene, camera );
}
```

## 透明的墙
```js
function renderPortal( thisPortalMesh, otherPortalMesh, thisPortalTexture ) {

        // set the portal camera position to be reflected about the portal plane
        thisPortalMesh.worldToLocal( reflectedPosition.copy( camera.position ) );
        reflectedPosition.x *= - 1.0; reflectedPosition.z *= - 1.0;
        otherPortalMesh.localToWorld( reflectedPosition );
        portalCamera.position.copy( reflectedPosition );

        // grab the corners of the other portal
        // - note: the portal is viewed backwards; flip the left/right coordinates
        otherPortalMesh.localToWorld( bottomLeftCorner.set( 50.05, - 50.05, 0.0 ) );
        otherPortalMesh.localToWorld( bottomRightCorner.set( - 50.05, - 50.05, 0.0 ) );
        otherPortalMesh.localToWorld( topLeftCorner.set( 50.05, 50.05, 0.0 ) );
        // set the projection matrix to encompass the portal's frame
        CameraUtils.frameCorners( portalCamera, bottomLeftCorner, bottomRightCorner, topLeftCorner, false );

        // render the portal
        thisPortalTexture.texture.encoding = renderer.outputEncoding;
        renderer.setRenderTarget( thisPortalTexture );
        renderer.state.buffers.depth.setMask( true ); // make sure the depth buffer is writable so it can be properly cleared, see #18897
        if ( renderer.autoClear === false ) renderer.clear();
        thisPortalMesh.visible = false; // hide this portal from its own rendering
        renderer.render( scene, portalCamera );
        thisPortalMesh.visible = true; // re-enable this portal's visibility for general rendering

    }
```

    球体表面的光

```js
    const directionalLight = new THREE.DirectionalLight( 0xffffff, 0 ); // set intensity to 0 to start
    const x = 597;
    const y = 213;
    const theta = ( x + 0.5 ) * Math.PI / 512;
    const phi = ( y + 0.5 ) * Math.PI / 512;

    directionalLight.position.setFromSphericalCoords( 100, - phi, Math.PI / 2 - theta );
```