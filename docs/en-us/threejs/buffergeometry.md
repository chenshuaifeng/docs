BufferGeometry
是面片、线或点几何体的有效表述。包括`顶点位置`，`面片索引`、`法相量`、`颜色值`、`UV 坐标`和自定义缓存属性值。使用 BufferGeometry 可以有效减少向 GPU 传输上述数据所需的开销。

## API

- `.morphAttributes`: Array
变形属性的数组

- `.computeVertexNormals () : undefined`
通过面片法向量的平均值计算每个顶点的法向量。
计算顶点法向量是为顶点着色
```js
	new THREE.BufferGeometryLoader()
				.setPath( 'models/json/' )
				.load( 'suzanne_buffergeometry.json', function ( geometry ) {
					material = new THREE.MeshNormalMaterial();
					geometry.computeVertexNormals();
        })
```
导入bufferGeometry的json文件时，需要计算顶点法向量

- `.setDrawRange ( start : Integer, count : Integer ) : undefined`
设置缓存的数目
```js
// 实例：webgl_buffergeometry_drawrange
particles.setDrawRange( 0, particleCount );
particles.setAttribute( 'position', new THREE.BufferAttribute( particlePositions, 3 ).setUsage( THREE.DynamicDrawUsage ) );
```
BufferGeometry.setDrawRange设置范围
BufferAttribute设置动态

- `.applyMatrix4 ( matrix : Matrix4 ) : this`
用给定矩阵转换几何体的顶点坐标。

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



  ## 技能点
1. 在n的范围内，生成长度为d的三角形
```js
  // 实例：webgl_interactive_buffergeometry
  const x = Math.random() * n - n2;
  const y = Math.random() * n - n2;
  const z = Math.random() * n - n2;

  const ax = x + Math.random() * d - d2;
  const ay = y + Math.random() * d - d2;
  const az = z + Math.random() * d - d2;

```

2. 循环遍历的以一个三角面片为基础所以i `i += 9`
法向量的计算，一个点的x\y\z分量normal的x/y/z
```js
// 三角形的三个点在一个平面上，所以法向量相同
// 通过向量的减法和叉乘求法向量
cb.subVectors( pC, pB );
ab.subVectors( pA, pB );
cb.cross( ab );

const nx = cb.x;
const ny = cb.y;
const nz = cb.z;

normals[ i ] = nx;
normals[ i + 1 ] = ny;
normals[ i + 2 ] = nz;

normals[ i + 3 ] = nx;
normals[ i + 4 ] = ny;
normals[ i + 5 ] = nz;

normals[ i + 6 ] = nx;
normals[ i + 7 ] = ny;
normals[ i + 8 ] = nz;
```
顶点属性的位置由循环体i来确定，不一定需要`i += 9`

3. 通过attributeColor属性的需要采用顶点着色
```js
new THREE.MeshPhongMaterial( {
					color: 0xaaaaaa, specular: 0xffffff, shininess: 250,
					side: THREE.DoubleSide, vertexColors: true
				} );
```
4. 在物理世界中距离=速度 * 时间，在threejs中单位时间是固定的也是帧率
粒子在threejs中的运动计算
```js
  // 粒子的位置 = 当前的位置 + 速度 
	particlePositions[ i * 3 ] += particleData.velocity.x;
  particlePositions[ i * 3 + 1 ] += particleData.velocity.y;
  particlePositions[ i * 3 + 2 ] += particleData.velocity.z;

  // 粒子反方向速度取反
  if ( particlePositions[ i * 3 + 1 ] < - rHalf || particlePositions[ i * 3 + 1 ] > rHalf )
      particleData.velocity.y = - particleData.velocity.y;

    if ( particlePositions[ i * 3 ] < - rHalf || particlePositions[ i * 3 ] > rHalf )
      particleData.velocity.x = - particleData.velocity.x;

    if ( particlePositions[ i * 3 + 2 ] < - rHalf || particlePositions[ i * 3 + 2 ] > rHalf )
      particleData.velocity.z = - particleData.velocity.z;
    
  ```
  5. 生成一个用线组成球体的bufferGeometry
  ```js
    function createGeometry() {
      const geometry = new THREE.BufferGeometry();
      const vertices = [];
      const vertex = new THREE.Vector3();
      for ( let i = 0; i < 1500; i ++ ) {
        vertex.x = Math.random() * 2 - 1;
        vertex.y = Math.random() * 2 - 1;
        vertex.z = Math.random() * 2 - 1;
        vertex.normalize();
        vertex.multiplyScalar( r );
        // 生成点1
        vertices.push( vertex.x, vertex.y, vertex.z );
        vertex.multiplyScalar( Math.random() * 0.09 + 1 );
        // 生成点2
        vertices.push( vertex.x, vertex.y, vertex.z );
      }
      geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices, 3 ) );
      return geometry;
    }
```

> 可以先进性缓冲区的申请，然后在动画函数中进行顶点属性的赋值  

```js
    // 实例：webgl_decals

    // 生成了一条从交点垂直于平面的法线
const geometry = new THREE.BufferGeometry();
geometry.setFromPoints( [ new THREE.Vector3(), new THREE.Vector3() ] );
line = new THREE.Line( geometry, new THREE.LineBasicMaterial() );

// 动画函数中
const positions = line.geometry.attributes.position;
const p = intersects[ 0 ].point;
const n = intersects[ 0 ].face.normal.clone();
n.transformDirection( mesh.matrixWorld );
n.multiplyScalar( 10 );
n.add( intersects[ 0 ].point );
positions.setXYZ( 0, p.x, p.y, p.z );
positions.setXYZ( 1, n.x, n.y, n.z );

// 顶点属性设置后需要手动更新
positions.needsUpdate = true;
```
**通过向量的加法运算使得向量垂直于物体表面**

1. 使用modifier修改器对几何体顶点进行修改
