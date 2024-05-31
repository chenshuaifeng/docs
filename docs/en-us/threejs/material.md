## 基本属性

- `vertexColors ` : Boolean
是否使用顶点着色。默认值为false。 此引擎支持RGB或者RGBA两种顶点颜色，取决于缓冲 attribute 使用的是三分量（RGB）还是四分量（RGBA）。
当使用自定义缓冲几何体bufferGeometry时，给几何体添加材质时会用到



- `sizeAttenuation` : Boolean
指定点的大小是否因相机深度而衰减。（仅限透视摄像头。）默认为true。


随机设置颜色的方法
```js
color.setHex(Math.random() * 0xfffffff)
```

## 物理引擎ammo.js

遇到的问题是引擎引不进，解决办法通过`script`标签引入脚本

```js
// 物理引擎ammo.js的示例
physics_ammo_instancing
```

## 控制器

## 轨道控制器OribteControle
- `target` 
控制旋转的围绕中心