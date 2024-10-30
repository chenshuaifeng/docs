## 变量

**Attributes**
1. Attributes 可以被 JavaScript 代码操作，
2. 也可以在 vertex shader 中被作为变量访问。
在vertex中定义，同样在vettex中使用，定义的时BufferGeometry中的属性
3. Attributes 通常被用于存储颜色、纹理坐标以及其他需要在  ***JavaScript 代码和 vertex shader 之间互相传递的数据***。

**Varying**
1. vertex shader 中定义变量
新创建的变量，用于存储bufferGeometry的属性
2. 用于从 vertex shader 向 fragment shader 传递数据。
3. 通常传递法向量\颜色color等在 vertex shader 中计算生成的数据会使用 varying。

**Uniforms**
1. Uniform 通常是由 JavaScript 代码设置
2. 并且在 vertex shader 和 fragment shader 中都能够访问。
3. 使用 uniform 设定在一帧的所有绘制中相同的数据，例如光源颜色、亮度、全局变换以及透视数据等等。
4. 使用Uniforms定义的遍历：uModelViewMatrix、uProjectionMatrix

## 着色器

**VertexShader**
顶点着色器
可访问变量：
1. 矩阵：modelViewMatrix\projectionMatrix
2. BufferAttribute: position等

输出变量
1. gl_Position
2. gl_PointSize

**FragementShader**
片元着色器

定义变量
1. sampler2D 纹理采样器, 在Uniform中获取的Texture

可访问变量
1. gl_PointCoord

输出变量
1. gl_FragColor

片元着色器内置变量  
[参考资料](https://www.jb51.net/article/193388.htm)  
***gl_PointSize***：在点渲染模式中，控制方形点区域渲染像素大小（注意这里是像素大小，而不是three.js单位，因此在移动相机是，所看到该点在屏幕中的大小不变）  
***gl_Position***：控制顶点选完的位置  
***gl_FragColor***：片元的RGB颜色值  
***gl_FragCoord***：片元的坐标，同样是以像素为单位  

gl_FragCoord内置变量是vec2类型，它表示WebGL在canvas画布上渲染的所有片元或者说像素的坐标，坐标原点是canvas画布的左上角，x轴水平向右，y竖直向下，gl_FragCoord坐标的单位是像素，gl_FragCoord的值是vec2(x,y),通过gl_FragCoord.x、gl_FragCoord.y方式可以分别访问片元坐标的纵横坐标
```js
fragmentShader: `
    void main() {
        if(gl_FragCoord.x < 600.0) {
            gl_FragColor = vec4(1.0,0.0,0.0,1.0);
        } else {
            gl_FragColor = vec4(1.0,1.0,0.0,1.0);
        }
    }
`
```
***gl_PointCoord***：在点渲染模式中，对应方形像素坐标
gl_PointCoord内置变量也是vec2类型，同样表示像素的坐标，但是与gl_FragCoord不同的是，gl_FragCoord是按照整个canvas算的x值从[0,宽度]，y值是从[0,高度]。而gl_PointCoord是在点渲染模式中生效的，而它的范围是对应小正方形面，同样是左上角[0,0]到右下角[1,1]。

官方提供的两个着色器
```js
import { RGBShiftShader } from 'three/addons/shaders/RGBShiftShader.js';
import { DotScreenShader } from 'three/addons/shaders/DotScreenShader.js';

```

纹理Shader  webgl_depth_texture

## Shader实例
1. webgl_test_memory2
2. webgl_buffergeometry_custom_attributes_particles
3. webgl_buffergeometry_instancing
4. webgl_buffergeometry_instancing_billboards
5. webgl_buffergeometry_rawshader
6. webgl_buffergeometry_selective_draw
7. webgl_depth_texture: 深度材质
8. webgl2_buffergeometry_attributes_none
9. webgl2_buffergeometry_attributes_integer
10. ShaderMaterial——webgl_materials_channels
11. webgl_materials_curvature
12. webgl_materials_wireframe
13. webgl_postprocessing_crossfade


示例： webgl_buffergeometry_custom_attributes_particles
```js
<script type="x-shader/x-vertex" id="vertexshader">
    attribute float size;

    varying vec3 vColor;
    void main() {

        vColor = color;

        vec4 mvPosition = modelViewMatrix * vec4( position, 1.0 );

        gl_PointSize = size * ( 300.0 / -mvPosition.z );

        gl_Position = projectionMatrix * mvPosition;

    }

</script>

<script type="x-shader/x-fragment" id="fragmentshader">

    uniform sampler2D pointTexture;

    varying vec3 vColor;

    void main() {

        gl_FragColor = vec4( vColor, 1.0 );

        gl_FragColor = gl_FragColor * texture2D( pointTexture, gl_PointCoord );

    }

</script>
```

RawShaderMaterial
实例：webgl2_materials_texture3d_partialupdate