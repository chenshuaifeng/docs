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


## 变换控制器 TransformControls
类可提供一种类似于在数字内容创建工具（例如Blender）中对模型进行交互的方式，来在3D空间中变换物体。 和其他控制器不同的是，变换控制器不倾向于对场景摄像机的变换进行改变

`.show.xyz`
控制显示的箭头

`.attach ( object : Object3D ) : TransformControls`
object: 应当变换的3D对象。

设置应当变换的3D对象，并确保控制器UI是可见的。

```js
// 鼠标按下时禁止，鼠标抬起时运行
transformControls.addEventListener( 'mouseDown', () => orbitControls.enabled = false );
			transformControls.addEventListener( 'mouseUp', () => orbitControls.enabled = true );
```

## 轨迹球控制器（TrackballControls）
TrackballControls 与 OrbitControls 相类似。然而，它不能恒定保持摄像机的up向量。 这意味着，如果摄像机绕过“北极”和“南极”，则不会翻转以保持“右侧朝上

实际效果是：不能上下翻转，只能左右翻转


## 第一人称控制器FirstPersonControls
鼠标左右移动时，相机加速移动

- `.lookSpeed : Number`
环视速度。默认为0.005。

- `movementSpeed:number`
移动速度。默认为1。

## 指针锁定控制器PointerLockControls
实例：misc_controls_pointerlock
