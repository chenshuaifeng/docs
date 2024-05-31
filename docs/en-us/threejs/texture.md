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

