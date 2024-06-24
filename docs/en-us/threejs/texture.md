## 基本属性

- `colorSpace`: 纹理的颜色空间
```js
THREE.NoColorSpace = ""
THREE.SRGBColorSpace = "srgb"
THREE.LinearSRGBColorSpace = "srgb-linear"
```
纹理空间发生改变时需要重新更新纹理。`texture.needsUpdates = ture`

- `map`：贴图
漫反射材质、镜面反射材质需要光照才能显示出效果

- `vertexColors ` : Boolean
是否使用顶点着色。默认值为false。 此引擎支持RGB或者RGBA两种顶点颜色，取决于缓冲 attribute 使用的是三分量（RGB）还是四分量（RGBA）。

```js
// 纹理平铺模式
texture.wrapS = texture.wrapT = THREE.RepeatWrapping;
// 纹理像素
texture.repeat.set( 0.008, 0.008 );
```
## 代码示例
### 星空图
![angular-question3](../../static/images/texture_starts.png ':size=800')

```js
// 初始化场景星星的效果
const initSceneStar=()=>{
    // 自定义缓冲几何体创建星星
    const geometry = new THREE.BufferGeometry()
    // 星星位置的坐标
    const vertices = []
    // 创建纹理
    const textureLoader =new THREE.TextureLoader()
    const spritel = textureLoader.load(IMAGE STAR1const)
    sprite2 = textureLoader.load(IMAGE STAR2)
    // 声明点的参数
    parameters = [
    [[0.6，100，0.75],sprite1，50],[[0,0,1],sprite2,20]
 ]
}
```
星星是一个点，由一个顶点组成，点材质。

> 颜色随时间变化的算法-蓝白之间的交替，实质是材质color随时间变化的函数

```js
// 渲染星星的运动，在动画函数中调用
const renderStarMove=()=>{
    // 星星颜色交替变化
    // shl颜色范围0~1
    const time = Date.now()*0.00005
    for(let i=0;i< parameters.length;i++){
    const color = parameters[i][0]
    const h=((360*(color[0]+time))% 360)/ 360
    materials[i].color.setHsL(color[0],color[1],parseFloa(h.toFixed(2)))
    }
}
```

> 星星的运动轨迹

运动需要考虑几个变量：时间、速度、位移、初始位置
核心是通过对物体mesh的setZ()来改变物体的远近，z随时间变化的函数

```js
// 星星的运动
zprogress += 1
if(zprogress >= zAxisNumber + depth /2){
    zprogress = particles init position
}else {
    particles first.forEach(item =>{
        item.position.setZ(zprogress)
    })
}
```


> 一个随机分布的算法
```js
    const x = Math.random() * n - n2;
    const y = Math.random() * n - n2;
    const z = Math.random() * n - n2;

    const ax = x + Math.random() * d - d2;
    const ay = y + Math.random() * d - d2;
    const az = z + Math.random() * d - d2;

    const bx = x + Math.random() * d - d2;
    const by = y + Math.random() * d - d2;
    const bz = z + Math.random() * d - d2;

    const cx = x + Math.random() * d - d2;
    const cy = y + Math.random() * d - d2;
    const cz = z + Math.random() * d - d2;

    positions.push( ax, ay, az );
    positions.push( bx, by, bz );
    positions.push( cx, cy, cz );
```
- `blending`混合
```js
THREE.NoBlending
THREE.NormalBlending
THREE.AdditiveBlending // 贴图效果的混合
THREE.SubtractiveBlending
THREE.MultiplyBlending
THREE.CustomBlending
```

控制纹理平铺时的状态

```js
texture.anisotropy = 16;
texture.repeat.set(4, 4);
```

### 镜面反射效果
![angular-question3](../../static/images/reflector.png ':size=800')

```js
let reflectorGeometry = new THREE.PlaneBufferGeometry(100, 16)
let reflectorplane = new Reflector(reflectorGeometry,{
    textureWidth: window.innerWidth,
    textureHeight: window.innerHeight,
    color:0x332222
})
reflectorPlane.rotation.x=-Math.PI /2;
scene.add(reflectorPlane);
```

```js
  let lightPillarMaterial = new THREE.MeshBasicMaterial({
      color: 0xffffff,
      map: lightPillarTexture,
      alphaMap: lightPillarTexture,
      transparent: true,
      blending: THREE.AdditiveBlending,
      side: THREE.DoubleSide,
      depthWrite: false,
    });
```

## 自定义BufferGeometry中的texture
1. uniform
2. glsl


## 自定义ShaderMaterial的time属性
1. 在GLSL中定义time
```js
void main( void ) {

				vec2 position = - 1.0 + 2.0 * vUv;

				vec4 noise = texture2D( texture1, vUv );
				vec2 T1 = vUv + vec2( 1.5, - 1.5 ) * time * 0.02;
				vec2 T2 = vUv + vec2( - 0.5, 2.0 ) * time * 0.01;

				T1.x += noise.x * 2.0;
				T1.y += noise.y * 2.0;
				T2.x -= noise.y * 0.2;
				T2.y += noise.z * 0.2;

				float p = texture2D( texture1, T1 * 2.0 ).a;

				vec4 color = texture2D( texture2, T2 * 2.0 );
				vec4 temp = color * ( vec4( p, p, p, p ) * 2.0 ) + ( color * color - 0.1 );

				if( temp.r > 1.0 ) { temp.bg += clamp( temp.r - 2.0, 0.0, 100.0 ); }
				if( temp.g > 1.0 ) { temp.rb += temp.g - 1.0; }
				if( temp.b > 1.0 ) { temp.rg += temp.b - 1.0; }

				gl_FragColor = temp;

				float depth = gl_FragCoord.z / gl_FragCoord.w;
				const float LOG2 = 1.442695;
				float fogFactor = exp2( - fogDensity * fogDensity * depth * depth * LOG2 );
				fogFactor = 1.0 - clamp( fogFactor, 0.0, 1.0 );

				gl_FragColor = mix( gl_FragColor, vec4( fogColor, gl_FragColor.w ), fogFactor );

			}

```
2. uniform 中初始化time

3. animate中出入time值

```js
uniforms[ 'time' ].value += 0.2 * delta;
```
