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
```js
// if normal and uv attributes are not removed, mergeVertices() can't consolidate indentical vertices with different normal/uv data

    boxGeometry.deleteAttribute( 'normal' );
    boxGeometry.deleteAttribute( 'uv' );
```

## GeometryCompressionUtils
mesh的压缩工具
示例：webgl_buffergeometry_compression

```js
if ( data[ 'QuantizePosEncoding' ] ) {
    GeometryCompressionUtils.compressPositions( mesh );
}
if ( data[ 'NormEncodingMethods' ] !== 'None' ) {
    GeometryCompressionUtils.compressNormals( mesh, data[ 'NormEncodingMethods' ] );
}
if ( data[ 'DefaultUVEncoding' ] ) {
    GeometryCompressionUtils.compressUvs( mesh );
}
```


- `.mergeVertices ( geometry : BufferGeometry, tolerance : Number ) : BufferGeometry`
合并顶点
geometry -- 用于合并顶点的 BufferGeometry 实例。
tolerance -- 要合并的顶点属性之间允许的最大差异。 默认为 1e-4。

返回一个新的 BufferGeometry ，其中包含将所有（在容差范围内的）具有相似属性的顶点合并而成的顶点。

## LoadingManager加载管理器
其功能是处理并跟踪已加载和待处理的数据

- `.onProgress : Function`
此方法加载每一个项，加载完成时进行调用。 有如下参数：
url — 被加载的项的url。
itemsLoaded — 目前已加载项的个数。
itemsTotal — 总共所需要加载项的个数。

**加载模型时用于进度条管理**


## GeometryUtils

- `gosper`
创建点

- `hilbert3D`
以非常快速的方式生成三维坐标。

## LoadingManager加载管理器
LoadingManager是一个全局实例, 当其他加载器没有指定加载管理器时，它将被其他大多数的加载器设为默认的加载管理器。

实例：webgl_materials_envmaps_exr
```js
THREE.DefaultLoadingManager.onLoad = function ( ) {
    pmremGenerator.dispose();
};
```

## 辅助工具

### Box3

```js
// 移动物体位置
new THREE.Box3().setFromObject( child ).getCenter( child.position ).multiplyScalar( - 1 );
```
包围盒可以用来移动物体位置


## Tessellation 曲面细分
示例：webgl_modifier_tessellation
一种类似于变形复原过程，细分为三角面片，对任何模型对象都适用

## Morphtargets变形
示例：webgl_morphtargets
将物体形态变换的过程
