## BufferAttribute
- `.copyAt ( index1 : Integer, bufferAttribute : BufferAttribute, index2 : Integer ) : this`
将一个矢量从 bufferAttribute[index2] 拷贝到 array[index1] 中。(将外面的点的坐标拷贝到内部)

```js
  const intersect = intersects[ 0 ];
  const face = intersect.face;

  const linePosition = line.geometry.attributes.position;
  const meshPosition = mesh.geometry.attributes.position;

  linePosition.copyAt( 0, meshPosition, face.a );
  linePosition.copyAt( 1, meshPosition, face.b );
  linePosition.copyAt( 2, meshPosition, face.c );
  linePosition.copyAt( 3, meshPosition, face.a );

    // 更新最新的位置信息
  mesh.updateMatrix();
  line.geometry.applyMatrix4( mesh.matrix );

  line.visible = true;
```

## 示例代码

1. webgl_lines_sphere
点、线、球
```js
function createGeometry() {
				const geometry = new THREE.BufferGeometry();
				const vertices = [];

				const vertex = new THREE.Vector3();

				for ( let i = 0; i < 1500; i ++ ) {
					vertex.x = Math.random() * 2 - 1;
					vertex.y = Math.random() * 2 - 1;
					vertex.z = Math.random() * 2 - 1;
          // 单位向量
					vertex.normalize();
          // 向量按照倍数
					vertex.multiplyScalar( r );

					vertices.push( vertex.x, vertex.y, vertex.z );

					vertex.multiplyScalar( Math.random() * 0.09 + 1 );

					vertices.push( vertex.x, vertex.y, vertex.z );

				}

				geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices, 3 ) );

				return geometry;

			}
```
思路：
1. 构造Geometry：
  点数量
  线的两个端点position: [0, 1]vertices
  单位向量
  按照球形分布
  1） 求随机数（-1， 1）
  2） 重整化向量normalize
  3） 求出一个点坐标
  4） 求另一个坐标
  5） 输出vertices, Geometry是两个顶点