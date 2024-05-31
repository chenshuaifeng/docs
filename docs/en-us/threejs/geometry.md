## 缓冲几何体

### 自定义缓冲几何体 BufferGeometry

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




