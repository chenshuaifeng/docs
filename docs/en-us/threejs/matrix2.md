## API
- `.Position(x, yz )`
给当前矩阵设置位置
- `.compose ( position : Vector3, quaternion : Quaternion, scale : Vector3 ) : this`
设置将该对象位置 position，四元数quaternion 和 缩放scale 组合变换的矩阵。将位置、旋转、缩放属性赋值给矩阵
```js
    const position = new THREE.Vector3();
    const rotation = new THREE.Euler();
    const quaternion = new THREE.Quaternion();
    const scale = new THREE.Vector3();
            
    position.x = Math.random() * 40 - 20;
    position.y = Math.random() * 40 - 20;
    position.z = Math.random() * 40 - 20;

    rotation.x = Math.random() * 2 * Math.PI;
    rotation.y = Math.random() * 2 * Math.PI;
    rotation.z = Math.random() * 2 * Math.PI;

    quaternion.setFromEuler( rotation );

    scale.x = scale.y = scale.z = Math.random() * 1;

    matrix.compose( position, quaternion, scale );
```
物体的角度姿态属性设置：欧拉角 -> 四元数


## 本地矩阵`.matrix`、世界矩阵`.matrixWorld`

模型本地矩阵属性.matrix的属性值是一个4x4矩阵Matrix4

由position位置转为matrix矩阵数据进行赋值
1. position随机生成坐标
2. 更新mesh得matrix
3. setMatrixAt(i, matrix)
```js

    dummy.position.set( offset - 300 * x + 200 * Math.random(), 0, offset - 300 * y );

    dummy.updateMatrix();

    mesh.setMatrixAt( i, dummy.matrix );
```


改变three.js模型对象的.position、.scale或.rotation(.quaternion)任何一个属性，查看查看mesh.matrix值的变化。

1.仅改变mesh.position,你会发现mesh.matrix的值会从单位矩阵变成一个平移矩阵(沿着xyz轴分别平移2、3、


```js
// 不执行renderer.render(scene, camera);情况下测试
mesh.position.set(2,3,4);
mesh.updateMatrix();//更新矩阵，.matrix会变化
console.log('本地矩阵',mesh.matrix);

```
2.仅改变mesh.scale,你会发现mesh.matrix的值会从单位矩阵变成一个缩放矩阵(沿着xyz轴三个方向分别缩放6、6、6倍);

```js
mesh.scale.set(6,6,6);
mesh.updateMatrix();
console.log('本地矩阵',mesh.matrix);

```
3.同时平移和缩放，查看.matrix变化，你可以看到mesh.matrix是平移矩阵和缩放矩阵的复合矩阵。
```js
mesh.position.set(2,3,4);
mesh.scale.set(6,6,6);
mesh.updateMatrix();
console.log('本地矩阵',mesh.matrix);
```

示例:css3d_molecules
蛋白质示例中更新线的矩阵信息
1. 矩阵的绕轴旋转角度物体的矩阵
```js
const axis = tmpVec2.set( 0, 1, 0 ).cross( tmpVec1 );
const radians = Math.acos( tmpVec3.set( 0, 1, 0 ).dot( tmpVec4.copy( tmpVec1 ).normalize() ) );

const objMatrix = new THREE.Matrix4().makeRotationAxis( axis.normalize(), radians );
object.matrix.copy( objMatrix );
object.quaternion.setFromRotationMatrix( object.matrix );

object.matrixAutoUpdate = false;
object.updateMatrix();
```

> 应用矩阵:给geometry应用矩阵和给mesh应用矩阵有什么不同?
一般先给geometry使用

示例:webgl_interactive_buffergeometry
```js
mesh.updateMatrix();
line.geometry.applyMatrix4( mesh.matrix );	
```

## 代码示例
通过矩阵进行旋转
矩阵表示物体在三维空间的姿态，在更新后的基础上再次应用
```js
const rotateY = new THREE.Matrix4().makeRotationY( 0.005 );
....

camera.applyMatrix4( rotateY );
camera.updateMatrixWorld();

```
给矩阵应用四元数
tmpM.makeRotationFromQuaternion( tmpQ );


算法给正方形内部增加1
```js
let curr = 0;
			const countPerRow = 4;
			const countPerSlice = countPerRow * countPerRow;
			const sliceCount = 4;
			const totalCount = sliceCount * countPerSlice;
			const margins = 8;

			const perElementPaddedSize = ( INITIAL_CLOUD_SIZE - margins ) / countPerRow;
			const perElementSize = Math.floor( ( INITIAL_CLOUD_SIZE - 1 ) / countPerRow );

			function animate() {

				requestAnimationFrame( animate );

				const time = performance.now();
				if ( time - prevTime > 1500.0 && curr < totalCount ) {

					const position = new THREE.Vector3(
						Math.floor( curr % countPerRow ) * perElementSize + margins * 0.5,
						( Math.floor( ( ( curr % countPerSlice ) / countPerRow ) ) ) * perElementSize + margins * 0.5,
						Math.floor( curr / countPerSlice ) * perElementSize + margins * 0.5
					).floor();

					const maxDimension = perElementPaddedSize - 1;
					const box = new THREE.Box3( new THREE.Vector3( 0, 0, 0 ), new THREE.Vector3( maxDimension, maxDimension, maxDimension ) );
					const scaleFactor = ( Math.random() + 0.5 ) * 0.5;
					const source = generateCloudTexture( perElementPaddedSize, scaleFactor );

					renderer.copyTextureToTexture3D( box, position, source, cloudTexture );

					prevTime = time;

					curr ++;

				}

				mesh.material.uniforms.cameraPos.value.copy( camera.position );
				// mesh.rotation.y = - performance.now() / 7500;

				mesh.material.uniforms.frame.value ++;

				renderer.render( scene, camera );

			}
            function generateCloudTexture( size, scaleFactor = 1.0 ) {

				const data = new Uint8Array( size * size * size );
				const scale = scaleFactor * 10.0 / size;

				let i = 0;
				const perlin = new ImprovedNoise();
				const vector = new THREE.Vector3();

				for ( let z = 0; z < size; z ++ ) {

					for ( let y = 0; y < size; y ++ ) {

						for ( let x = 0; x < size; x ++ ) {

							const dist = vector.set( x, y, z ).subScalar( size / 2 ).divideScalar( size ).length();
							const fadingFactor = ( 1.0 - dist ) * ( 1.0 - dist );
							data[ i ] = ( 128 + 128 * perlin.noise( x * scale / 1.5, y * scale, z * scale / 1.5 ) ) * fadingFactor;

							i ++;

						}

					}

				}

				return new THREE.Data3DTexture( data, size, size, size );

			}
            ```