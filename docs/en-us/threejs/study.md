1. 俯仰角elevation、方位角azimuth
elevation 
- 球形物体高度控制 
- 范围0~90。 0度时与x轴平行，90度时垂直
- 转为弧度制THREE.MathUtils.degToRad( 90 - parameters.elevation )

2. 使用鼠标按下、移动事件旋转rotation
```js
pointerXOnPointerDown = event.clientX - windowHalfX;
targetRotationOnPointerDown = targetRotation;

pointerX = event.clientX - windowHalfX;
targetRotation = targetRotationOnPointerDown + ( pointerX - pointerXOnPointerDown ) * 0.02;
```

3. 更新局部举证`updateMatrix`
当给mesh指定positin\rotation\scale后，mesh的矩阵之前更新
```js
// 示例：webgl_postprocessing_crossfade
dummy.updateMatrix();
mesh.setMatrixAt( i, dummy.matrix );
```