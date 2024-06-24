## 对象Obejct

### 光的折射
类似于瀑布的效果
```js
	import { Refractor } from 'three/addons/objects/Refractor.js';
	import { WaterRefractionShader } from 'three/addons/shaders/WaterRefractionShader.js';


    refractor = new Refractor( refractorGeometry, {
        color: 0xcbcbcb,
        textureWidth: 1024,
        textureHeight: 1024,
        shader: WaterRefractionShader
    } );

    refractor.position.set( 0, 50, 0 );

    scene.add( refractor );
```


## 缓冲几何体Geometries

### DecalGeometry贴画
```js
    import { DecalGeometry } from 'three/addons/geometries/DecalGeometry.js';
```