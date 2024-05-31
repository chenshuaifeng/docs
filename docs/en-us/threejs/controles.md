## 轨道控制器OribtControle

- `.maxPolarAngle `: Float
垂直旋转的角度的上限，范围是0到Math.PI，其默认值为Math.PI。

- `.target `: Vector3
控制器的焦点


> 轨道控制器实例化后不起作用

原因是需要重新渲染 `renderer.render(scene, camera)`


调整往上整体调整视角，比如看人的上半身
1. camera.lookAt(0, 1, 0)
2. controles.set(0, 1, 0)