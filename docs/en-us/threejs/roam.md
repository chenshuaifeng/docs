## 键盘WASD按键状态记录

如果你玩过游戏，一般都知道，通过键盘的W、A、S、D按键可以控制玩家角色在3D场景中运动，比如控制一个人前后左右运动，比如控制一辆车前后左右运动。

## 键盘事件
面代码功能是当你按下随便一个键盘按键，就会触发参数2表示的函数执行，并log打印对应按键名字相关信息
```js
// 监听鼠标按下事件
document.addEventListener('keydown', (event) => {
    console.log('event.code',event.code);
})
// 监听鼠标松开事件
document.addEventListener('keyup', (event) => {
    console.log('event.code',event.code);
})


```

## 记录键盘按键WASD状态
```js
// 声明一个对象keyStates用来记录键盘事件状态
const keyStates = {
    // 使用W、A、S、D按键来控制前、后、左、右运动
    // false表示没有按下，true表示按下状态
    W: false,
    A: false,
    S: false,
    D: false,
};
// 当某个键盘按下设置对应属性设置为true
document.addEventListener('keydown', (event) => {
    if (event.code === 'KeyW') keyStates.W = true;
    if (event.code === 'KeyA') keyStates.A = true;
    if (event.code === 'KeyS') keyStates.S = true;
    if (event.code === 'KeyD') keyStates.D = true;
});
// 当某个键盘抬起设置对应属性设置为false
document.addEventListener('keyup', (event) => {
    if (event.code === 'KeyW') keyStates.W = false;
    if (event.code === 'KeyA') keyStates.A = false;
    if (event.code === 'KeyS') keyStates.S = false;
    if (event.code === 'KeyD') keyStates.D = false;
});

```
## 键盘状态
```js
// 循环执行的函数render
function render() {
    requestAnimationFrame(render);
}
render();
// 循环执行的函数中测试W键盘状态值
function render() {
    if(keyStates.W){
        console.log('W键按下');
    }else{
        console.log('W键松开');
    }
    requestAnimationFrame(render);
}
render();

```
## W键控制角色模型运动
```js
// 用三维向量表示玩家角色(人)运动漫游速度
//按下W键对应的人运动速度
const v = new THREE.Vector3(0, 0, 3);
// 渲染循环
const clock = new THREE.Clock();
function render() {
    const deltaTime = clock.getDelta();
    if (keyStates.W) {
        // 在间隔deltaTime时间内，玩家角色位移变化计算(速度*时间)
        const deltaPos = v.clone().multiplyScalar(deltaTime);
        player.position.add(deltaPos);//更新玩家角色的位置
    }
    mixer.update(deltaTime);// 更新播放器相关的时间
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();

```

## 加速度
当你按下W键的时候，玩家角色模型，会运动，松开W键，人会停下来。但是这个运动效果是突然运动和突然停止，没有一个加速或减速的过程，本节课以W键为例，设置玩家加速过程,也就是当你按下W键以后，人的速度从0慢慢提升上来。
```js
const v = new THREE.Vector3(0, 0, 3);
function render() {
    if (keyStates.W) {
        // 在间隔deltaTime时间内，玩家角色位移变化计算(速度*时间)
        const deltaPos = v.clone().multiplyScalar(deltaTime);
        player.position.add(deltaPos);//更新玩家角色的位置
    }
}

```
## 设置加速度
```js
// 用三维向量表示玩家角色(人)运动漫游速度
const v = new THREE.Vector3(0, 0, 0);//初始速度设置为0
const a = 12;//加速度：调节按键加速快慢
// 渲染循环
const clock = new THREE.Clock();
function render() {
    const deltaTime = clock.getDelta();
    if (keyStates.W) {
        //先假设W键对应运动方向为z
        const front = new THREE.Vector3(0,0,1);
        // W键按下时候，速度随着时间增加
        v.add(front.multiplyScalar(a * deltaTime));
        // 在间隔deltaTime时间内，玩家角色位移变化计算(速度*时间)
        const deltaPos = v.clone().multiplyScalar(deltaTime);
        player.position.add(deltaPos);//更新玩家角色的位置
    }
    
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();

```
## 限制最高速度
执行上面代码，当你按下W键的时候，v会一直加速，这时候可以通过v.length()计算速度v的值，当然速度小一个临界值的时候，才增加速度v的大小
```js
const vMax = 5;//限制玩家角色最大速度
...
if (v.length() < vMax) {//限制最高速度
    // W键按下时候，速度随着时间增加
    v.add(front.multiplyScalar(a * deltaTime));
}

```
## 阻尼(玩家角色逐渐减速停止)
怎么设置阻尼，具体说，就是当没有WASD按键加速时候，玩家角色模型，会在阻尼作用下逐渐减速停止，就像地面上滚动的球逐渐停下来。
```js
if (keyStates.W) {
    //先假设W键对应运动方向为z
    const front = new THREE.Vector3(0,0,1);
    // W键按下时候，速度随着时间增加
    v.add(front.multiplyScalar(a * deltaTime));
    // 在间隔deltaTime时间内，玩家角色位移变化计算(速度*时间)
    const deltaPos = v.clone().multiplyScalar(deltaTime);
    player.position.add(deltaPos);//更新玩家角色的位置
}

```
## 设置阻尼减速
其实很简单，可以在渲染循环中，重复执行速度v乘以一个小于1的数值，这样重复多次执行以后，速度就会逼近0。比如v* (1 - 0.04) = v * 0.96,多次循环乘以0.96(v*0.96*0.96*0.96...)，v就会无限逼近于0。
```js
const damping = -0.04;
function render() {
    if (keyStates.W) {
        ...
    }
    // v*(1 + damping) = v* (1 - 0.04) = v * 0.96
    // 多次循环乘以0.96(v*0.96*0.96*0.96...),v就会无限逼近于0。
    // v*(1 + damping) = v + v * damping
    v.addScaledVector(v, damping);//阻尼减速

    requestAnimationFrame(render);
}

```
## 验证阻尼是否生效
把`if (keyStates.W){}`里面玩家角色位置更新的代码，挪到外面，你可以发现，当按键W松开，玩家角色会慢慢停下来，原因很简单，虽然一直在执行速度*时间更新玩家位置，但是在阻尼作用下，速度慢慢逼近0了，位移变化量自然逼近0
```js
// 用三维向量表示玩家角色(人)运动漫游速度
const v = new THREE.Vector3(0, 0, 0);//初始速度设置为0
const a = 12;//WASD按键的加速度：调节按键加速快慢
const damping = -0.04;//阻尼 当没有WASD加速的时候，人、车等玩家角色慢慢减速停下来
// 渲染循环
const clock = new THREE.Clock();
function render() {
    const deltaTime = clock.getDelta();
    if (keyStates.W) {
        //先假设W键对应运动方向为z
        const front = new THREE.Vector3(0, 0, 1);
        if (v.length() < 5) {//限制最高速度
            // W键按下时候，速度随着时间增加
            v.add(front.multiplyScalar(a * deltaTime));
        }
    }

    // 阻尼减速
    v.addScaledVector(v, damping);

    //更新玩家角色的位置  当v是0的时候，位置更新也不会变化
    const deltaPos = v.clone().multiplyScalar(deltaTime);
    player.position.add(deltaPos);

    mixer.update(deltaTime);
    renderer.render(scene, camera);
    requestAnimationFrame(render);
}
render();

```

