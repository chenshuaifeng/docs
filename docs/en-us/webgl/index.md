## wegGL步骤
### 1. 查找 WebGL 返回分配的输入位置
```js
  attribLocations: {
    vertexPosition: gl.getAttribLocation(shaderProgram, "aVertexPosition"),
  },
  uniformLocations: {
    projectionMatrix: gl.getUniformLocation(shaderProgram, "uProjectionMatrix"),
    modelViewMatrix: gl.getUniformLocation(shaderProgram, "uModelViewMatrix"),
  },
```

### 2. 将缓冲区中的数据与着色器中的属性进行关联、如何绑定数据
```js
gl.bindBuffer(gl.ARRAY_BUFFER, buffers.position);
gl.vertexAttribPointer(
      programInfo.attribLocations.vertexPosition,
      numComponents,
      type,
      normalize,
      stride,
      offset,
    );
```

### 3. 给uniform变量指定矩阵,并进行图形绘制
```js
  gl.uniformMatrix4fv(
    programInfo.uniformLocations.projectionMatrix,
    false,
    projectionMatrix,
  );

    gl.drawArrays(gl.TRIANGLE_STRIP, offset, vertexCount);

```
## 相关api

绘制API
- drawElements 绘制索引图形
- drawArrays 绘制普通图形

## 纹理

1. 创建纹理
```js
const texture = gl.createTexture();
```

2. 绑定纹理
```js
  gl.bindTexture(gl.TEXTURE_2D, texture);

```

3. 配置纹理
```js
 const level = 0;
  const internalFormat = gl.RGBA;
  const width = 1;
  const height = 1;
  const border = 0;
  const srcFormat = gl.RGBA;
  const srcType = gl.UNSIGNED_BYTE;
  const pixel = new Uint8Array([0, 0, 255, 255]); // opaque blue
  gl.texImage2D(
    gl.TEXTURE_2D,
    level,
    internalFormat,
    width,
    height,
    border,
    srcFormat,
    srcType,
    pixel,
  );

  const image = new Image();
  image.onload = () => {
    gl.bindTexture(gl.TEXTURE_2D, texture);
    gl.texImage2D(
      gl.TEXTURE_2D,
      level,
      internalFormat,
      srcFormat,
      srcType,
      image,
    );

    // WebGL1 has different requirements for power of 2 images
    // vs. non power of 2 images so check if the image is a
    // power of 2 in both dimensions.
    if (isPowerOf2(image.width) && isPowerOf2(image.height)) {
      // Yes, it's a power of 2. Generate mips.
      gl.generateMipmap(gl.TEXTURE_2D);
    } else {
      // No, it's not a power of 2. Turn off mips and set
      // wrapping to clamp to edge
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
    }
  };
  image.src = url;

  return texture;
  ```

纹理采样器sampler2D

uniform sampler2D uTextrure

1. 需要在每个顶点信息中加入面的朝向**法线**。这个法线是一个垂直于这个顶点所在平面的向量。
2. 需要明确方向光的传播方向，可以使用一个**方向向量**来定义。


```js

    // Apply lighting effect

    highp vec3 ambientLight = vec3(0.6, 0.6, 0.6);
    highp vec3 directionalLightColor = vec3(0.5, 0.5, 0.75);
    highp vec3 directionalVector = vec3(0.85, 0.8, 0.75);
    // 我们先根据立方体位置和朝向，通过顶点法线乘以法线矩阵来转换法线
    highp vec4 transformedNormal = uNormalMatrix * vec4(aVertexNormal, 1.0);

// 法线与方向向量（即，光来自的方向）的点积来计算得出顶点反射方向光的量。如果计算出的这个值小于 0，则我们把值固定设为 0，因为你不会有小于 0 的光
    highp float directional = max(dot(transformedNormal.xyz, directionalVector), 0.0);
    vLighting = ambientLight + (directionalLightColor * directional);
```
逆矩阵（Inverse Matrix）是一个数学概念，特别在线性代数中，对于给定的方阵（即行数和列数相等的矩阵），如果存在另一个方阵，使得这两个矩阵相乘（按照矩阵乘法规则）的结果为单位矩阵（即主对角线上的元素都是1，其余元素都是0的矩阵），那么这两个矩阵互称为对方的逆矩阵。

具体地说，如果有一个方阵 A，存在一个方阵 B，使得 A * B = B * A = I（其中 I 是单位矩阵），那么 B 就是 A 的逆矩阵，通常记作 A^-1。

不是所有的方阵都有逆矩阵。一个方阵有逆矩阵的充分必要条件是它的行列式（determinant）不为零。如果方阵 A 的行列式为零，那么 A 被称为奇异矩阵（singular matrix）或退化矩阵（degenerate matrix），它没有逆矩阵。

逆矩阵在线性代数、计算机科学、物理学和工程学等领域中有广泛的应用。在图形学中，特别是在光照计算和模型变换中，逆矩阵和逆矩阵的转置经常被用来变换法线向量和顶点位置，以正确地模拟光照效果或实现观察空间变换。

在3D图形编程中，逆矩阵和转置通常用于从世界空间到视图空间（或模型空间到模型视图空间）的变换，以便能够正确地在这些空间中计算和解释光照、阴影和其他视觉效果。


