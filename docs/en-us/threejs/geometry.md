
## 自定义缓冲几何体 BufferGeometry
所有几何体得基类
- `.setFromPoints( points )`
从线中获取点设置给buffer得顶点坐标

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
```

## 实例化网格（InstancedMesh）

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