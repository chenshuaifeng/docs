## API
- `.copyAt ( index1 : Integer, bufferAttribute : BufferAttribute, index2 : Integer ) : this`
将一个矢量从 bufferAttribute[index2] 拷贝到 array[index1] 中。(将外面的attribute拷贝到目标的自身的atrribute中)

```js
  // 实例：webgl_interactive_buffergeometry
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
注意点：当bufferAttribute获取到其他物体的属性时，如果其他物体的位置、角度、大小发生了变化，bufferAttribute应该应用到其他物体的状态属性。通过矩阵的方式应用

- `.normalized : Boolean`
指明缓存中数据在转化为GLSL着色器代码中数据时是否需要被归一化。详见构造函数中的说明。

```js
// 实例：webgl_buffergeometry_uint

const positionAttribute = new THREE.Float32BufferAttribute( positions, 3 );
const normalAttribute = new THREE.Int16BufferAttribute( normals, 3 );
const colorAttribute = new THREE.Uint8BufferAttribute( colors, 3 );

normalAttribute.normalized = true; // this will map the buffer values to 0.0f - +1.0f in the shader
colorAttribute.normalized = true;
```

- `.count`: number  
count属性物体的点的数量