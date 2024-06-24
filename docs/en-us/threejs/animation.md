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
`name`: 动画属性，比如: `.position`、 `.scale`、 `.quaternion`、`..material.color`、`..material.opacity`，注意不同属性对应不同关键帧轨道
`time`:动画时间

2. 创建剪辑Clip

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
控制动画的播放、停止

4. 更新动画

```js
mixer.update(delta)
```

## AnimationAction
通过mixer调用clipAcition方法传入clip返回的，控制动画的方法，包括许多属性和方法

- `.loop : Number`
动画循环次数，循环结束后返回初始状态，如果要当动画循环结束后留在最后一帧需调用下面方法

- `.clampWhenFinished : Boolean`
停在最后一帧

> 如何停掉当前动画，播放下一个动画

1. preAction.fadeOut( duration );
2. curAction.fadeIn( duration ).play();;


> 如何停掉当前的动画，播放插入的动画，插入的动画播放完毕后，在进行当前动画播放

1. 首先执行如上1/2动作，进入第二个动画播放阶段
2. 给Mixer监听动作动画播放完毕fiNish事件
3. 最后在执行1/2动作恢复1动画播放


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

## 控制动画

### 播放暂停|停止
1. 获取动画的状态:paused是否停止状态，默认是false
```js
action.paused
action.play
action.stop

// 多个动画通过遍历播放
actions.forEach( function ( action ) {
    action.play();
} );
```

2. 更新动画状态

- `.loop : Number`

循环模式 
THREE.LoopOnce - 只执行一次
THREE.LoopRepeat - 重复次数为repetitions的值, 且每次循环结束时候将回到起始动作开始下一次循环。
THREE.LoopPingPong - 重复次数为repetitions的值, 且像乒乓球一样在起始点与结束点之间来回循环。



### 单步调试动画1
对动画的更新时间进行控制
```js
// 核心控制更新时间
mixer.update( mixerUpdateDelta );

// 在在两帧动画间隙，确定开启单步调式
if ( singleStepMode ) {
    mixerUpdateDelta = sizeOfNextStep // 0.1;
    sizeOfNextStep = 0;

}

```

> AnimationAction
AnimationActions 用来调度存储在AnimationClips中的动画。
### 单步调式动画1
在播放之前`action.play()`,设置动画的步骤
```js
    const baseActions = {
        idle: { weight: 1 },
        walk: { weight: 0 },
        run: { weight: 0 }
    };
    const additiveActions = {
        sneak_pose: { weight: 0 },
        sad_pose: { weight: 0 },
        agree: { weight: 0 },
        headShake: { weight: 0 }
    };
    setWeight( idleAction, settings[ 'modify idle weight' ] );
    function setWeight( action, weight ) {
        // 核心是通过action的以下setEffectiveWeight改变步骤
        action.enabled = true;
        action.setEffectiveTimeScale( 1 );
        action.setEffectiveWeight( weight );

    }
    // weight权重，多个动画的权重
```

`.fadeOut ( durationInSeconds : Number ) : this`
在传入的时间间隔内，逐渐将此动作的权重（weight）由1降至0。此方法可链式调用。
逐步停掉当前的动画

`.fadeIn ( durationInSeconds : Number ) : this`
在传入的时间间隔内，逐渐将此动作的权重（weight）由0升到1。此方法可链式调用。
动画逐渐开启

开启了下一个动画，在回复前需要重置动画
`.reset () : this`
重置动作。此方法可链式调用。

`.clampWhenFinished : Boolean`
如果 clampWhenFinished 值设为true, 那么动画将在最后一帧之后自动暂停（paused）

如果 clampWhenFinished 值为false, enabled 属性值将在动作的最后一次循环完成之后自动改为false, 那么这个动作以后就不会再执行。
## AnimationUtils
一个提供各种动画辅助方法的对象，内部使用。

## AnimationObjectGroup动画对象
用于成组的物体动画，接受一组mesh
```js
animationGroup.add( mesh );
```

## 动画code

### 水波纹
```js
// 水波扩散效果
gsap.to(circleMesh.scale, {
      duration: 1 + Math.random() * 0.5,
      x: 2,
      y: 2,
      z: 2,
      repeat: -1,
      delay: Math.random() * 0.5,
      yoyo: true,
      ease: "power2.inOut",
    });

// 位置设置
lightPillar.quaternion.setFromUnitVectors(
      new THREE.Vector3(0, 1, 0),
      position.clone().normalize()
    );
```

```js
    // 随时间time变化的的曲线运动图
  gsap.to(time, {
    value: 1,
    duration: 10,
    repeat: -1, // 
    ease: "linear",
    onUpdate: () => {
        // time.value大小表示旋转速率 [0, 1]，repeat重复执行
      moon.position.x = 150 * Math.cos(time.value * Math.PI * 2);
      moon.position.z = 150 * Math.sin(time.value * Math.PI * 2);
      // 自身旋转Math.PI*2 * 快慢系数
      moon.rotation.y = time.value * Math.PI * 8;
    },
  });
```


## 相机运动算法
```js
// 相机位置运动 相对运动
theta += 0.1;

camera.position.x = radius * Math.sin( THREE.MathUtils.degToRad( theta ) );
camera.position.y = radius * Math.sin( THREE.MathUtils.degToRad( theta ) );
camera.position.z = radius * Math.cos( THREE.MathUtils.degToRad( theta ) );
camera.lookAt( scene.position );

```

```js
now *= 0.001; // 帧率等于0.016
```


一个大小随时间变化的算法, 三角函数在外面乘以系数是幅度，在里面乘以系数是频率
```js
function animate() {

				const currentTime = Date.now();
				const time = ( currentTime - startTime ) / 1000;

				object.position.y = 0.8;
				object.rotation.x = time * 0.5;
				object.rotation.y = time * 0.2;
				console.log(Math.cos( time ) * 0.125) // [-0.125, 0.125] 逐渐递增递减或递减
				object.scale.setScalar( Math.cos( time ) * 0.125 + 0.875 );

				stats.begin();
				renderer.render( scene, camera );
				stats.end();

			}
```
> time *= 0.0001 让物体沿着曲线运动，采用插值计算
在requestAnimationFrame中*=是一个递增的值，递增步幅帧率，递增频率为帧率,

从spliCurve中获取的是二维向量
```js
    time *= 0.001
	// console.log(time)
	// 坦克移动插值
	const tankTime = time * 0.1 // 0.1 转动速率
	// 小于1的值
	curve.getPointAt(tankTime % 1, tankPosition)
	// 设置坦克位置
	tank.position.set(tankPosition.x, 0, tankPosition.y)
	// 坦克朝向
	curve.getPointAt((tankTime + 0.01) % 1, tankTarget)
	// 设置坦克朝向  坦克的朝向始终和运动的方向一致。 比如人在骑车拐弯时眼睛看向的方向是正前方 约等于路的方向
	tank.lookAt(tankTarget.x, 0, tankTarget.y)

	// 车轱辘的滚动
	wheelMeshes.forEach((obj) => {
		obj.rotation.x = time * 3
	})

	// 目标对象的上下浮动
	targetBob.position.y = Math.sin(time * 2) * 2
```

## tween

根据点的位置做动画Positions:[[x,y,z], [x,y,z], [x,y,z], [x,y,z]]
```js
	function transition() {
                // 当前组的索引，一共有四组动画, offset等于每组动画点的起始索引
				const offset = current * particlesTotal * 3;
				const duration = 2000;
                // i点的索引  j+=3点等于点在positions中的索引 
				for ( let i = 0, j = offset; i < particlesTotal; i ++, j += 3 ) {

					const object = objects[ i ];

					new TWEEN.Tween( object.position )
						.to( {
							x: positions[ j ],
							y: positions[ j + 1 ],
							z: positions[ j + 2 ]
						}, Math.random() * duration + duration )
						.easing( TWEEN.Easing.Exponential.InOut )
						.start();

				}

				new TWEEN.Tween( this )
					.to( {}, duration * 3 )
					.onComplete( transition )
					.start();

				current = ( current + 1 ) % 4;

			}
```
