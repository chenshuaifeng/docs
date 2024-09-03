## 性能测试
1. 实例：webgl_buffergeometry_compression
性能测试的工具代码：
```js
import * as BufferGeometryUtils from 'three/addons/utils/BufferGeometryUtils.js';

BufferGeometryUtils.estimateBytesUsed( mesh.geometry )
```
内存占用情况与物体大小没有关系，只与三角面片多少有关系，细分度

2. 实例2：webgl_instancing_performance
模式：
- INSTANCED
```js
new THREE.InstancedMesh
```

- NAIVE
```js
	for ( let i = 0; i < api.count; i ++ ) {

				randomizeMatrix( matrix );

				const mesh = new THREE.Mesh( geometry, material );
				mesh.applyMatrix4( matrix );

				scene.add( mesh );
	}

```
- MERGED
```js
		const geometries = [];
		const matrix = new THREE.Matrix4();

		for ( let i = 0; i < api.count; i ++ ) {

			randomizeMatrix( matrix );

			const instanceGeometry = geometry.clone();
			instanceGeometry.applyMatrix4( matrix );

			geometries.push( instanceGeometry );

		}

		const mergedGeometry = BufferGeometryUtils.mergeBufferGeometries( geometries );
```

性能计算的一个方法
`BufferGeometr.index`
内存占用 =顶点属性+ 法线属性+索引属性
```js
	function getGeometryByteLength( geometry ) {

			let total = 0;

			if ( geometry.index ) total += geometry.index.array.byteLength;

			for ( const name in geometry.attributes ) {

				total += geometry.attributes[ name ].array.byteLength;

			}

			return total;

		}
function formatBytes( bytes, decimals ) {

			if ( bytes === 0 ) return '0 bytes';

			const k = 1024;
			const dm = decimals < 0 ? 0 : decimals;
			const sizes = [ 'bytes', 'KB', 'MB' ];

			const i = Math.floor( Math.log( bytes ) / Math.log( k ) );

			return parseFloat( ( bytes / Math.pow( k, i ) ).toFixed( dm ) ) + ' ' + sizes[ i ];

		}
```

**性能优化方式**
1. 对顶点属性进行压缩
2. 对法向量进行压缩
3. 对UV贴图进行压缩
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
