
## 自定义缓冲几何体 BufferGeometry
所有几何体得基类
- `.setFromPoints ( points : Array ) : this`

通过点队列设置该 BufferGeometry 的 attribute。
```js
const pointsGeometry = new THREE.BufferGeometry().setFromPoints( vertices );
```
- `Vector.fromBufferAttribute ( attribute : BufferAttribute, index : Integer ) : this`
attribute - 来源的attribute。
index - 在attribute中的索引。

**从attribute获取顶点属性设置向量的x值、y值和z值。**
```js
  // 包含坐标的向量的集合
	const vertices = [];
    const positionAttribute = dodecahedronGeometry.getAttribute( 'position' );

    for ( let i = 0; i < positionAttribute.count; i ++ ) {
        // 每个向量代表一个顶点
        const vertex = new THREE.Vector3();
        // 给向量循环遍历赋值
        vertex.fromBufferAttribute( positionAttribute, i );
        vertices.push( vertex );

    }   
```
- `.copyAt ( index1 : Integer, bufferAttribute : BufferAttribute, index2 : Integer ) : this`
将一个矢量从 bufferAttribute[index2] 拷贝到 array[index1] 中。

```js
	const intersect = intersects[ 0 ];
  const face = intersect.face;

  const linePosition = line.geometry.attributes.position;
  const meshPosition = mesh.geometry.attributes.position;

  linePosition.copyAt( 0, meshPosition, face.a );
  linePosition.copyAt( 1, meshPosition, face.b );
  linePosition.copyAt( 2, meshPosition, face.c );
  linePosition.copyAt( 3, meshPosition, face.a );

  mesh.updateMatrix();

  line.geometry.applyMatrix4( mesh.matrix );

  line.visible = true;
```

`.userData : Object`
存储 BufferGeometry 的自定义数据的对象。为保持对象在克隆时完整，该对象不应该包括任何函数的引用。
自定义数据

- `position`属性 

```js
const geometry = new THREE.BufferGeometry();
// 创建一个简单的矩形. 在这里我们左上和右下顶点被复制了两次。
// 因为在两个三角面片里，这两个顶点都需要被用到。
const vertices = new Float32Array( [
	-1.0, -1.0,  1.0,
	 1.0, -1.0,  1.0,
	 1.0,  1.0,  1.0,

	 1.0,  1.0,  1.0,
	-1.0,  1.0,  1.0,
	-1.0, -1.0,  1.0
] );

// itemSize = 3 因为每个顶点都是一个三元组。
geometry.setAttribute( 'position', new THREE.BufferAttribute( vertices, 3 ) );
const material = new THREE.MeshBasicMaterial( { color: 0xff0000 } );
const mesh = new THREE.Mesh( geometry, material );
```

自定义缓冲几何体自身没有这些属性、需要设定的属性：`position`、`normal`、`uv`、`color`、, 
其中`UV`属性用来进行贴图，`normal`属性用来反射环境光
法向向量每个面都有，也可能是每个顶点都有

### 批量生成得三角形
三角形太大太大如何缩小
1. 先给定一个三角形的位置范围position
2. 然后给定一个点得距离范围
3. position加上点的距离范围

三角形的如何求法向量
核心方法是：求出三角形的两边向量，然后两向量叉乘求出法向量
1. 定义三个向量存储三角形得三个顶点
2. 两个向量相减获得新的向量，数学含义是求出三角形两边
3. 两个向量叉乘获得法向量

```js
	const positions = [];
				const normals = [];
				const colors = [];

				const color = new THREE.Color();

				const n = 800, n2 = n / 2;	// triangles spread in the cube
				const d = 12, d2 = d / 2;	// individual triangle size

				const pA = new THREE.Vector3();
				const pB = new THREE.Vector3();
				const pC = new THREE.Vector3();

				const cb = new THREE.Vector3();
				const ab = new THREE.Vector3();

        // 三角形得个数
				for ( let i = 0; i < triangles; i ++ ) {

					// positions

					const x = Math.random() * n - n2;  // [-400, 400]
					const y = Math.random() * n - n2;
					const z = Math.random() * n - n2;
          // 第一个点
					const ax = x + Math.random() * d - d2;  // [-400, 400] + [-6, 6]
					const ay = y + Math.random() * d - d2;
					const az = z + Math.random() * d - d2;
          // 第二个点
					const bx = x + Math.random() * d - d2;
					const by = y + Math.random() * d - d2;
					const bz = z + Math.random() * d - d2;
          // 第三个点
					const cx = x + Math.random() * d - d2;
					const cy = y + Math.random() * d - d2;
					const cz = z + Math.random() * d - d2;

					positions.push( ax, ay, az );
					positions.push( bx, by, bz );
					positions.push( cx, cy, cz );

					// flat face normals

					pA.set( ax, ay, az );
					pB.set( bx, by, bz );
					pC.set( cx, cy, cz );

					cb.subVectors( pC, pB );
					ab.subVectors( pA, pB );
					cb.cross( ab );

					cb.normalize();

					const nx = cb.x;
					const ny = cb.y;
					const nz = cb.z;
          // 给每个顶点求法线
					normals.push( nx, ny, nz );
					normals.push( nx, ny, nz );
					normals.push( nx, ny, nz );

					// colors

					const vx = ( x / n ) + 0.5;
					const vy = ( y / n ) + 0.5;
					const vz = ( z / n ) + 0.5;

					color.setRGB( vx, vy, vz );

					const alpha = Math.random();

					colors.push( color.r, color.g, color.b, alpha );
					colors.push( color.r, color.g, color.b, alpha );
					colors.push( color.r, color.g, color.b, alpha );

				}
```

### 批量点的运动
核心是点有速度，在动画函数中对位移增加
连线得方法：每个点得连接数
1. 求出线得个数
2. 分配缓冲空间
3. 在动画函数中为线顶点赋值
3.1 循环i和j两个点
3.2 求出两个点得距离，向量得模长
当小于最小距离，连接数加一

线得点的数量算法：`count*(count-1)`

```js
function animate() {

    let vertexpos = 0;
    let colorpos = 0;
    let numConnected = 0;

    for ( let i = 0; i < particleCount; i ++ )
      particlesData[ i ].numConnections = 0;

    for ( let i = 0; i < particleCount; i ++ ) {

      // get the particle
      const particleData = particlesData[ i ];

      // 每帧位移增加
      particlePositions[ i * 3 ] += particleData.velocity.x;
      particlePositions[ i * 3 + 1 ] += particleData.velocity.y;
      particlePositions[ i * 3 + 2 ] += particleData.velocity.z;

      if ( particlePositions[ i * 3 + 1 ] < - rHalf || particlePositions[ i * 3 + 1 ] > rHalf )
        particleData.velocity.y = - particleData.velocity.y;

      if ( particlePositions[ i * 3 ] < - rHalf || particlePositions[ i * 3 ] > rHalf )
        particleData.velocity.x = - particleData.velocity.x;

      if ( particlePositions[ i * 3 + 2 ] < - rHalf || particlePositions[ i * 3 + 2 ] > rHalf )
        particleData.velocity.z = - particleData.velocity.z;

      if ( effectController.limitConnections && particleData.numConnections >= effectController.maxConnections )
        continue;

      // Check collision
      for ( let j = i + 1; j < particleCount; j ++ ) {

        const particleDataB = particlesData[ j ];
        if ( effectController.limitConnections && particleDataB.numConnections >= effectController.maxConnections )
          continue;

        const dx = particlePositions[ i * 3 ] - particlePositions[ j * 3 ];
        const dy = particlePositions[ i * 3 + 1 ] - particlePositions[ j * 3 + 1 ];
        const dz = particlePositions[ i * 3 + 2 ] - particlePositions[ j * 3 + 2 ];
        const dist = Math.sqrt( dx * dx + dy * dy + dz * dz );

        if ( dist < effectController.minDistance ) {

          particleData.numConnections ++;
          particleDataB.numConnections ++;

          const alpha = 1.0 - dist / effectController.minDistance;

          positions[ vertexpos ++ ] = particlePositions[ i * 3 ];
          positions[ vertexpos ++ ] = particlePositions[ i * 3 + 1 ];
          positions[ vertexpos ++ ] = particlePositions[ i * 3 + 2 ];

          positions[ vertexpos ++ ] = particlePositions[ j * 3 ];
          positions[ vertexpos ++ ] = particlePositions[ j * 3 + 1 ];
          positions[ vertexpos ++ ] = particlePositions[ j * 3 + 2 ];

          colors[ colorpos ++ ] = alpha;
          colors[ colorpos ++ ] = alpha;
          colors[ colorpos ++ ] = alpha;

          colors[ colorpos ++ ] = alpha;
          colors[ colorpos ++ ] = alpha;
          colors[ colorpos ++ ] = alpha;

          numConnected ++;

        }

      }

    }


    linesMesh.geometry.setDrawRange( 0, numConnected * 2 );
    linesMesh.geometry.attributes.position.needsUpdate = true;
    linesMesh.geometry.attributes.color.needsUpdate = true;

    pointCloud.geometry.attributes.position.needsUpdate = true;

    render();

    stats.update();

  }

```
自定义BufferGeometry的颜色动起来，满足的条件：
1. 申请缓冲区大小，顶点属性为空
2. colorAttribute属性的setUsage为动态`THREE.DynamicDrawUsage`
3. 在动画渲染函数中遍历顶点并赋值更、新颜色color
4. offset偏移颜色变化的速率
```js
	function updateColors( colorAttribute ) {

      const l = colorAttribute.count;

      for ( let i = 0; i < l; i ++ ) {
					// 色调饱和度、亮度
        const h = ( ( offset + i ) % l ) / l;

        color.setHSL( h, 1, 0.5 );
        colorAttribute.setX( i, color.r );
        colorAttribute.setY( i, color.g );
        colorAttribute.setZ( i, color.b );

      }

      colorAttribute.needsUpdate = true;

      offset += 25;

    }
```

给模型添加坐标，获取模型矩阵后，然后赋值给InstancesMesh
1. 求出模型坐标
2. 更新模型矩阵
3. 给InstanceMesh应用Matrix


```js
for ( let x = 0, i = 0; x < 32; x ++ ) {

						for ( let y = 0; y < 32; y ++ ) {

							dummy.position.set( offset - 300 * x + 200 * Math.random(), 0, offset - 300 * y );

							dummy.updateMatrix();

							mesh.setMatrixAt( i, dummy.matrix );

							mesh.setColorAt( i, new THREE.Color( `hsl(${Math.random() * 360}, 50%, 66%)` ) );

							i ++;
						}
					}
```

### lineGeometry
使用bufferGeomery生成直线
方法一： 通过attributes的属性直接赋值
lineGeometry.attributes.position.setXYZ(startV)
lineGeometry.attributes.position.setXYZ(endV)
lineGeometry.attributes.position.needUpdate = true

方法二：通过bufferGeometry的setFromPoints赋值
当位置向量先不知道时可以先创建，然后在赋值
```js
const geometry = new THREE.BufferGeometry();
    geometry.setFromPoints( [ new THREE.Vector3(), new THREE.Vector3() ] );

    line = new THREE.Line( geometry, new THREE.LineBasicMaterial() );

  const positions = line.geometry.attributes.position;
  positions.setXYZ( 0, p.x, p.y, p.z );
  positions.setXYZ( 1, n.x, n.y, n.z );
  positions.needsUpdate = true;
```


## 数据结构
调用getAttribute('xxx')获取的是一个顶点属性的对象，点的数据存储在arry属性中；
包括count顶点数等其他数据
可以调用getXXX传入索引值获取某个顶点坐标
可以调用setXXX入索引值设置某个顶点坐标

### BufferAttribute动态创建缓冲区

- `setUsage`、`setDrawRange`
```js
THREE.StaticReadUsage
THREE.DynamicReadUsage
THREE.StreamReadUsage
```

> 碰撞时方向改变 核心是速度方向取反
```js
const render = () => {
  for (let i = 0; i < particleCount; i++) {
    const particleData = particlesData[i]
    particlePositions[i * 3] += particleData.velocity.x
    particlePositions[i * 3 + 1] += particleData.velocity.y
    particlePositions[i * 3 + 2] += particleData.velocity.z

    if (particlePositions[i * 3] < -range / 2 || particlePositions[i * 3] > range / 2) {
      particleData.velocity.x = -particleData.velocity.x
    }
    if (particlePositions[i * 3 + 1] < -range / 2 || particlePositions[i * 3 + 1] > range / 2) {
      particleData.velocity.y = -particleData.velocity.y
    }
    if (particlePositions[i * 3 + 2] < -range / 2 || particlePositions[i * 3 + 2] > range / 2) {
      particleData.velocity.z = -particleData.velocity.z
    }
  }

  points.geometry.attributes.position.needsUpdate = true
  orbitControles.update()

  orbitControles.update()
  renderer.render(scene, camera)
  requestAnimationFrame(render)
}
```
geometry居中方法
```js
xOffset = (Geometry.boundingBox.max.x-geometry.min.x ) / 2
```

## BoxGeometry模拟生成化学键
```js

					for ( let i = 0; i < positions.count; i += 2 ) {

						start.x = positions.getX( i );
						start.y = positions.getY( i );
						start.z = positions.getZ( i );

						end.x = positions.getX( i + 1 );
						end.y = positions.getY( i + 1 );
						end.z = positions.getZ( i + 1 );

						start.multiplyScalar( 75 );
						end.multiplyScalar( 75 );

						const object = new THREE.Mesh( boxGeometry, new THREE.MeshPhongMaterial( { color: 0xffffff } ) );
            // 通过插值的方式从起点到终点，即中点给Object
						object.position.copy( start );
						object.position.lerp( end, 0.5 ); // 返回一个start到end的中点向量
            // 延长start到end，再乘以系数
						object.scale.set( 5, 5, start.distanceTo( end ) );
						object.lookAt( end );
						root.add( object );

					}

          	const material = new THREE.MeshPhongMaterial( {
					color: 0xffffff,
					flatShading: true,
					vertexColors: true,
					shininess: 0
				} );
```
着色方式：
当flatShading设置为true时，材质会使用平面着色。这意味着每个三角形面都将被渲染为具有统一颜色的平面，不考虑三角形内部的平滑颜色过渡。
当flatShading设置为false（默认值）时，材质会使用平滑着色。这会在三角形内部实现颜色的平滑过渡，使得渲染结果看起来更加光滑和连续。
视觉效果：
使用平面着色时，对象的表面可能会呈现出较为“块状”或“面状”的外观，因为每个三角形面都会以统一的颜色渲染。
使用平滑着色时，对象的表面会呈现出更加平滑和自然的过渡效果，因为颜色会在三角形内部进行平滑插值。
应用场景：
平面着色在某些情况下可能是有用的，例如当你想要强调对象的几何形状或结构时。它也可以用于模拟某些材质的外观，如砖块或石头。
平滑着色则更适用于模拟光滑表面的对象，如金属、塑料或玻璃等。
技术细节：
平面着色的实现方式是在每个三角形面上执行一次光照计算，并将结果应用到整个面上。这意味着每个三角形面都会有一个统一的光照结果。
平滑着色则是通过在每个顶点上执行光照计算，并在三角形内部进行颜色插值来实现的。这允许在三角形内部实现颜色的平滑过渡。

### 对内置Geometry的顶点属性的操作

为内置Geometry设置颜色
1. 从Geometry中获取attributes,两种方法获取，属性和方法
  1.1 geometry.getAttribute('color')
  1.2 const colors1 = geometry1.attributes.color;
2. 静态颜色的算法`positions1.getY( i )`

```js
const colors1 = geometry1.attributes.color;
	for ( let i = 0; i < count; i ++ ) {
          // positions1.getY( i ) / radiu
					color.setHSL( ( positions1.getY( i ) / radius + 1 ) / 2, 1.0, 0.5, THREE.SRGBColorSpace );
					colors1.setXYZ( i, color.r, color.g, color.b );

					color.setHSL( 0, ( positions2.getY( i ) / radius + 1 ) / 2, 0.5, THREE.SRGBColorSpace );
					colors2.setXYZ( i, color.r, color.g, color.b );

					color.setRGB( 1, 0.8 - ( positions3.getY( i ) / radius + 1 ) / 2, 0, THREE.SRGBColorSpace );
					colors3.setXYZ( i, color.r, color.g, color.b );

				}

				const material = new THREE.MeshPhongMaterial( {
					color: 0xffffff,
					flatShading: true,
					vertexColors: true,
					shininess: 0
				} );
```


### 利用BuffferGeometry构造pointsGeometry
获取到内置Geometry的顶点属性，通过setFormPonits方法生成pointsGeometry

- `.setFromPoints ( points : Array ) : thi`s
通过点队列设置该 BufferGeometry 的 attribute。
points由向量组成的数组

```js
const pointsMaterial = new THREE.PointsMaterial( {
    color: 0x0080ff,
    map: texture,
    size: 1,
    alphaTest: 0.5
  } );

  const pointsGeometry = new THREE.BufferGeometry().setFromPoints( vertices );

  const points = new THREE.Points( pointsGeometry, pointsMaterial );
```
## 凸包几何体（ConvexGeometry）
由顶点生成面的几何体

```js
import { ConvexGeometry } from 'three/addons/geometries/ConvexGeometry.js';
```

## 多实例网格（InstancedMesh）

```js
 for (let i = 0; i < 50; i++) {
      let matrix = new THREE.Matrix4();
      let size = Math.random() * 10 - 8;
      matrix.makeScale(size, size, size);
      matrix.makeTranslation(
          Math.random() * 1000 - 500,
          Math.random() * 1000 - 500,
          Math.random() * 1000 - 500
      );

      moonInstanced.setMatrixAt(i, matrix);
  }
```


## 代码案例
VR看房代码片段
```js
// 将几何体外面转向内部
cube.geometry.scale(1, 1, -1)

// 对纹理的位置进行移动
texture.rotation = Math.PI;
// 旋转时按照纹理的中心点旋转
texture.center =new THREE.Vector2(0.5，0.5);
```

使用`.set`更新位置，自动会调用update，使用`.position.x `分量单独设置，需要手动调用

## ExtrudeGeometry挤压缓冲几何体
  由一个平面拉成几何体

  定义截面得形状
  ```js
  // pts1 截面顶点坐标
	const pts1 = [], count = 3;

    for ( let i = 0; i < count; i ++ ) {

      const l = 20;

      const a = 2 * i / count * Math.PI;

      pts1.push( new THREE.Vector2( Math.cos( a ) * l, Math.sin( a ) * l ) );

    }
  // 二位平面
    const shape1 = new THREE.Shape( pts1 );
    
  ```

```js
// extrudeSettings 缓冲几何体配置

// 按照路径去拉伸
extrudePath: closedSpline
```
- `steps:int`
与样条曲线有关，曲线上点的数量，决定了绘制曲线的平滑度

- `depth:float`
没有extrudePath是表示平面拉伸得长度

- `bevelEnabled:bool`
对挤出的形状应用是否斜角
- `bevelThickness:int`
斜角的厚度

```js
// 平面五角星算法
const pts2 = [], numPts = 5;

				for ( let i = 0; i < numPts * 2; i ++ ) {

					const l = i % 2 == 1 ? 10 : 20;

					const a = i / numPts * Math.PI;

					pts2.push( new THREE.Vector2( Math.cos( a ) * l, Math.sin( a ) * l ) );

				}
```

## 形状（Shape）
使用路径以及可选的孔洞来定义一个二维形状平面。 它可以和ExtrudeGeometry、ShapeGeometry一起使用，获取点，或者获取三角面。

> 获取shape得另一种方式

1. 通过遍历获取第i个（x, y）

2. i=1时 shape.moveTo, i!=1时 shape.lineTo

3. shape.getPoints()获取点得数据

> shape得作用

与ExtrudeGeometry生成三维几何体
与ShapeGeometry生成平面几何体

> Shape另外一种方式利用bufferGeometry

从shape中获取点得点的数据
shape.getPoints()

1. 可以与BufferGeometry.setFromPoints()生成geometry,
2. 与ShapeGemeotry生成平面
3. 与ExtrudeGeometry生成挤压几何体
4. 与line生成线条


```js
// 获取得点是均匀分布
const spacedPoints = shape.getSpacedPoints( 50 );
```
笑脸
```jd
const smileyShape = new THREE.Shape()
					.moveTo( 80, 40 )
					.absarc( 40, 40, 40, 0, Math.PI * 2, false );

				const smileyEye1Path = new THREE.Path()
					.moveTo( 35, 20 )
					.absellipse( 25, 20, 10, 10, 0, Math.PI * 2, true );

				const smileyEye2Path = new THREE.Path()
					.moveTo( 65, 20 )
					.absarc( 55, 20, 10, 0, Math.PI * 2, true );

				const smileyMouthPath = new THREE.Path()
					.moveTo( 20, 40 )
					.quadraticCurveTo( 40, 60, 60, 40 )
					.bezierCurveTo( 70, 45, 70, 50, 60, 60 )
					.quadraticCurveTo( 40, 80, 20, 60 )
					.quadraticCurveTo( 5, 50, 20, 40 );

				smileyShape.holes.push( smileyEye1Path );
				smileyShape.holes.push( smileyEye2Path );
				smileyShape.holes.push( smileyMouthPath );
```


### 构造函数Constructor
Shape( points : Array )
points -- (optional) 一个Vector2数组。


## 文本缓冲几何体（TextGeometry）
- paramer1 文本


## 形状缓冲几何体（ShapeGeometry）
从一个或多个路径形状中创建一个单面多边形几何体。


一个包裹着的球的算法
```js
	const group = new THREE.Group();
  // 产生一个半径为[1/30 , 1]的圆
  for ( let i = 1; i <= 30; i += 2 ) {

    const geometry = new THREE.SphereGeometry( i / 30, 48, 24 );

    const material = new THREE.MeshPhongMaterial( {

      color: new THREE.Color().setHSL( Math.random(), 0.5, 0.5, THREE.SRGBColorSpace ),
      side: THREE.DoubleSide,
      clippingPlanes: clipPlanes,
      clipIntersection: params.clipIntersection,
      alphaToCoverage: true,

    } );

    group.add( new THREE.Mesh( geometry, material ) );

  }
  ```

  ```js
  radius = Math.sqrt(
    Math.pow((i-w2) / w2 , 2.0) + Math.pow((j-d2) / 2 , 2.0) 
  )
  ```

  ## Subdivision平滑的曲线
  将Geometry包装为更为平滑的几何体
  ```js
  import { LoopSubdivision } from 'three-subdivide';

	const smoothGeometry = LoopSubdivision.modify( normalGeometry, params.iterations, params );

  meshSmooth.geometry = smoothGeometry;
  ```

  ## 三角面片的线
  组成三角形的线的顶点有四个
  
  ```js
  pointer = new THREE.Vector2();

  geometry = new THREE.BufferGeometry();
  geometry.setAttribute( 'position', new THREE.BufferAttribute( new Float32Array( 4 * 3 ), 3 ) );

  material = new THREE.LineBasicMaterial( { color: 0xffffff, transparent: true } );

  line = new THREE.Line( geometry, material );

  const intersect = intersects[ 0 ];
  const face = intersect.face;

  const linePosition = line.geometry.attributes.position;
  const meshPosition = mesh.geometry.attributes.position;
  linePosition.copyAt( 0, meshPosition, face.a );
  linePosition.copyAt( 1, meshPosition, face.b );
  linePosition.copyAt( 2, meshPosition, face.c );
  linePosition.copyAt( 3, meshPosition, face.a );

  mesh.updateMatrix();

  line.geometry.applyMatrix4( mesh.matrix );

  ```
  **先确定点的位置然后更新点的位置**

