## BufferGeometryUtils
一个包含 BufferGeometry 实例的实用方法的类

使用
```js
import * as BufferGeometryUtils from 'three/addons/utils/BufferGeometryUtils.js';
```
- `.mergeVertices ( geometry : BufferGeometry, tolerance : Number ) : BufferGeometry`
用于合并重复顶点
geometry -- 用于合并顶点的 BufferGeometry 实例。
tolerance -- 要合并的顶点属性之间允许的最大差异。 默认为 1e-4。

返回一个新的 BufferGeometry ，其中包含将所有（在容差范围内的）具有相似属性的顶点合并而成的顶点

## LoadingManager加载管理器
其功能是处理并跟踪已加载和待处理的数据

- `.onProgress : Function`
此方法加载每一个项，加载完成时进行调用。 有如下参数：
url — 被加载的项的url。
itemsLoaded — 目前已加载项的个数。
itemsTotal — 总共所需要加载项的个数。

**加载模型时用于进度条管理**
