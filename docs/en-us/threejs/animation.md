## 创建动画步骤

1. 创建关键帧轨道(KeyframeTrack)

```js
BooleanKeyframeTrack
ColorKeyframeTrack
NumberKeyframeTrack
QuaternionKeyframeTrack
StringKeyframeTrack
VectorKeyframeTrack

const positionKF = new THREE.VectorKeyframeTrack(
    name : String, times : Array, values : Array, interpolation : Constant
)
```

2. 创建短视频Clip
```js
AnimationClip( name : String, duration : Number, tracks : Array )
const clip = new THREE.AnimationClip('position', 4, [positionKF])
```

3.混淆动画AnimationMixer
```js
mixer = new THREE.AnimationMixer(mesh)
const clipAction = mixer.clipAction(cilp)
clipAction.play()
```

4. 更新动画

```js
mixer.update(delta)
```

## 模型动画

加载的是模型场景
AnimationMixer(gltf.scene)

分段加载AnimationClip


一个模拟动画的算法
```js
time *= 0.001
// 坦克移动插值
const tankTime = time * 0.1
// 小于1的值[0, 1]
// console.log(tankTime % 1)
// 从路径中取出[0, 1]的点的位置
curve.getPointAt(tankTime % 1, tankPosition)
// 坦克朝向
curve.getPointAt((tankTime + 0.01) % 1, tankTarget)
```