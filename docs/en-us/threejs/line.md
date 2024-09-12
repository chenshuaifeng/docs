## API
- `tension – 曲线的张力，默认为0.5。`
可以使得线打结

## line
vector
```js
let object = new CSS3DObject( bond );
object.position.copy( start );
object.position.lerp( end, 0.5 );
```
线的

实例：webgl_lines_fat ，webgl_lines_fat_raycasting 
实现了`Line`的粗细、颜色高亮、虚实变换、流动

曲线的分段数divisions，类似插值

```js
for ( let i = 0, l = divisions; i < l; i ++ ) {
    const t = i / l;
    spline.getPoint( t, point );
    positions.push( point.x, point.y, point.z );
    color.setHSL( t, 1.0, 0.5 );
    colors.push( color.r, color.g, color.b );
}
```

> 虚线材质：LineDashedMaterial，一种用于绘制虚线样式几何体的材质。

## 设置线宽
```js
import { LineMaterial } from 'three/addons/lines/LineMaterial.js';
import { Wireframe } from 'three/addons/lines/Wireframe.js';
import { WireframeGeometry2 } from 'three/addons/lines/WireframeGeometry2.js';

const geometry = new WireframeGeometry2( geo );
matLine = new LineMaterial( {
    color: 0x4080ff,
    linewidth: 5, // in pixels
    //resolution:  // to be set by renderer, eventually
    dashed: false
} );
wireframe = new Wireframe( geometry, matLine );
wireframe.computeLineDistances();
wireframe.scale.set( 1, 1, 1 );
scene.add( wireframe );
```

由于OpenGL限制，Threejs内置的材质无法设置线宽，需要采用自定义着色器方式设置