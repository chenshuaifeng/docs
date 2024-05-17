
## 背景介绍
cesium寻求理想的技术方案。 前后花了2天时间，终于实现了卫星地球加3D模型的顺滑效果，进入到街景的角度看更多细节.

![angular-question3](../../static/images/1.png ':size=800')

## 技术方案
cesium支持两种模型方案，使用3D tiles或者加载glTF模型，前者适用于展示大区域的建筑模型，后者适用于放置单个模型。3D tiles采用了一种称为loD（levels of Detail）分层加载的方法实现模型的快速加载呈现，翻译成通俗易懂的话就是先在视野中加载一个粗糙的模型，然后再慢慢加载整个模型的细节，一点点替换掉这个模型。访问这个示例，可以观察到视野中的模型从模糊到逐渐清晰的过程。
实现步骤
### 准备模型
这次调研采用**3D tiles**方式，即3D瓦片的方式加载模型，使用cesiume官方提供的**objTo3d-tiles**导出瓦片文件，每个瓦片文件包含**model.b3dm**和**tileset.json**两个文件。
.b3dm文件记录了模型信息，tileset.json则记录了模型的地理位置、.b3dm文件的路径、与其他子瓦片的关系，依靠这个tileset.json文件，粗糙的模型和细节模型建立了联结，加载粗糙模型之后cesium会自动遍历tileset.json文件中的children项，加载children中声明的细节模型，并通过refine（值为ADD或REPLACE）参数决定自身替换原模型或叠加在原模型上。

```js
// tileset.js文件结构
  "asset": {
    "version": "0.0",
    "tilesetVersion": "1.0.0-obj23dtiles"
  },
  "geometricError": 500,
  "root": {
    "boundingVolume": {
      "region": [
        1.9812667196223426,
        0.3977279931116791,
        1.98161901607411,
        0.39801077589267914,
        -11.305000305175781,
        134.83670043945312
      ]
    },
    "refine": "ADD",
    "geometricError": 500,
    "children": [
      {
        "boundingVolume": {
          "region": [
            1.9814442514794284,
            0.39777419936273345,
            1.9815212909274045,
            0.3978240929187335,
            -3.1728999614715576,
            98.01309967041016
          ]
        },
        "geometricError": 200,
        "refine": "ADD",
        "content": {
          "url": "Batchedbhyc/tileset.json"
        }
      }
 ]
}}

```

安装完objTo3d-tiles后，可使用以下指令导出瓦片文件
`obj23dtiles -i model.obj --tileset  -p customTilesetOptions.json`

其中customTilesetOptions.json为配置文件，可以指定导出文件的坐标、离地高度、外包体信息

```js
{
    "longitude":      -1.31968,     // 瓦片原点(模型原点 (0,0,0)) 经度的弧度值。
    "latitude":       0.698874,     // 瓦片原点维度的弧度值。
    "transHeight":    0.0,          // 瓦片原点所在高度，单位为米。
    "region":         true,         // 使用 region 作为外包体。
    "box":            false,        // 使用 box 作为外包体。
    "sphere":         false         // 使用 sphere 作为外包体。
}
```

加载模型
```js
<div id="cesiumContainer" class="fullSize"></div>
<script>
Cesium.Ion.defaultAccessToken = 'eyJhbGciO...';
var viewer = new Cesium.Viewer('cesiumContainer');
// 加载1个模型文件
let tile = viewer.scene.primitives.add(
    new Cesium.Cesium3DTileset({
        url: modelInfo.url , //模型地址
        preferLeaves: true,
        skipLevels: 4
    })
);
// 监听模型加载 
tile.readyPromise.then(function (argument) {
    console.log('loaded')
})
</script>
```

存在的问题
使用3D tiles方法加载模型，如何实现loD加载，如何拿到各个地图缩放级别的模是个难点。在这一点上，通过无人机倾斜摄影获取的模型有天然的优势，无人机只要在不同的离地高度上获取摄影数据就可以有不同缩放级别的模型原始数据，再通过专业的软件就可以构造出一个从粗糙到精细的模型树。
然而当前使用的模型时人工自建模型，我尚未找到可靠的将模型3Dtile化的方法，因此无法实现分层加载，这就导致前期模型加载时间拉长，且太过精细的模型缩小尺寸后看起来反而更粗糙。可见3D层的大部分工作量还是在对模型的处理上。
