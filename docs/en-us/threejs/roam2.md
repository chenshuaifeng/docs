## 按键S退后运动
## S键退后
```js
function render() {
    if (v.length() < vMax) {//限制最高速度
        if (keyStates.W) {
            //先假设W键对应运动方向为z
            const front = new THREE.Vector3(0, 0, 1);
            v.add(front.multiplyScalar(a * deltaTime));
        }
        if (keyStates.S) {
            // 与W按键相反方向
            const front = new THREE.Vector3(0, 0, -1);
            v.add(front.multiplyScalar(a * deltaTime));
        }
    }
    v.addScaledVector(v, damping);//阻尼减速
    //更新玩家角色的位置
    const deltaPos = v.clone().multiplyScalar(deltaTime);
    player.position.add(deltaPos);
}

```
下面是更多具体代码
```js
const keyStates = {
    W: false,
    S: false,
};
document.addEventListener('keydown', (event) => {
    if (event.code === 'KeyW') keyStates.W = true;
    if (event.code === 'KeyA') keyStates.A = true;
});
document.addEventListener('keyup', (event) => {
    if (event.code === 'KeyW') keyStates.W = false;
    if (event.code === 'KeyA') keyStates.A = false;
});

```

```js
const keyStates = {
    W: false,
    S: false,
};
document.addEventListener('keydown', (event) => {
    if (event.code === 'KeyW') keyStates.W = true;
    if (event.code === 'KeyA') keyStates.A = true;
});
document.addEventListener('keyup', (event) => {
    if (event.code === 'KeyW') keyStates.W = false;
    if (event.code === 'KeyA') keyStates.A = false;
});
```
## 相机跟着玩家走(第三人称漫游)
前面案例，通过按键控制玩家角色模型运动的时候，并没有控制相机移动。下面给大家讲解怎么控制相机运动产生漫游的感觉。
```js
const camera = new THREE.PerspectiveCamera();
camera.position.set(8, 10, 14);
camera.lookAt(0, 0, 0);

function render() {
    ...
    //更新玩家角色的位置
    player.position.add(deltaPos);
}
render();
```
## 相机作为玩家角色子对象
相机作为玩家角色子对象，可以实现相机对玩家角色模型的跟随运动，使相机运动，模拟人漫游3D场景的感觉

```js
// 把相机作为玩家角色的子对象，这样相机的位置和姿态就会跟着玩家角色改变
player.add(camera);//相机作为人的子对象，会跟着人运动

```
你可以根据需要放相机相对玩家角色的位置，比如我这里相机与人高度相近(你可以在blender中测量下人的高度)，把相机放在人后脑勺，拉开一定距离，然后相机镜头对准人的后脑勺。

下面尺寸是以相机的父对象玩家角色模型的局部坐标系坐标原点为参照的

camera.position.set(0, 1.6, -5.5);//玩家角色后面一点
camera.lookAt(0, 1.6, 0);//对着人身上某个点  视线大致沿着人的正前方

## 第三人称漫游
这里所谓的第三人称，你可以简单的理解为相机在运动漫游的过程中，你可以看到玩家角色模型，比如你能看到运动的人、车等角色模型，就是上面咱们写的代码，把相机放在玩家角色模型后面一点即可。

## 补充：相机视角参数fov影响相机位置设置
```js
const camera = new THREE.PerspectiveCamera(30,...);
player.add(camera);//相机作为人的子对象
//玩家角色后面一点  对应fov 30度
camera.position.set(0, 1.6, -5.5);
camera.lookAt(0, 1.6, 0);//对着人身上某个点

```
根据透视投影相机规律，fov变大，能够看到的视野范围角度更大。
```js
const camera = new THREE.PerspectiveCamera(70,...);
//玩家角色后面一点  对应fov 70度
camera.position.set(0, 1.6, -2.3);

```

## 鼠标左右拖动改变玩家视角
就是你按住**鼠标左键，左右拖动**，改变玩家角色和相机的视角

## 了解鼠标滑动事件规则
如果你不了解前端HTML5鼠标滑动事件的规则，可以跟着视频学习一遍，如果你非常熟悉，可以直接快进到下一步。

```js
// 鼠标滑动期间，会不停地多次触发鼠滑动事件，直到不再滑动
document.addEventListener('mousemove', (event) => {
    console.log('触发1次');
});
```
鼠标持续滑动时候，会多次触发滑动事件。event.movementX表示本次触发事件相对上次，鼠标左右方向滑动的距离，单位是像素，往右滑动是正，往左滑动是负
```js
document.addEventListener('mousemove', (event) => {
    console.log('鼠标每次x方向移动距离', event.movementX);
});

```
## 鼠标控制玩家转向
通过鼠标**左右滑动距离**控制玩家角色模型player旋转
```js
document.addEventListener('mousemove', (event) => {
    // 注意rotation.y += 与 -= 区别，左右旋转时候方向相反
    //event.movementX缩小一定倍数改变旋转控制的灵敏度
    player.rotation.y -= event.movementX / 600;
});

```
## 鼠标左键拖动时候，旋转玩家角色
鼠标左键拖动定义：鼠标左键按下，不松开，左右滑动

1. 记录鼠标左键状态 
```js
let leftButtonBool = false;//记录鼠标左键状态
document.addEventListener('mousedown', () => {
    leftButtonBool = true;
});
document.addEventListener('mouseup', () => {
    leftButtonBool = false;
});

```
2. 判断鼠标左键状态，决定是否旋转玩家角色
```js
document.addEventListener('mousemove', (event) => {
    //鼠标左键按下时候，才旋转玩家角色
    if(leftButtonBool){
        player.rotation.y -= event.movementX / 600;
    } 
});

```
## 获取玩家(相机)正前方方向
实际开发，玩家角色的视角或者说相机的视角，会随着鼠标左右移动变化的，不过前面几节课，为了降低学习难度，代码给的是固定方向
```js
function render() {
    if (keyStates.W) {
        //先假设W键对应运动方向为z
        const front = new THREE.Vector3(0, 0, 1);
        // 改变玩家速度
        v.add(front.multiplyScalar(a * deltaTime));
    }
}

```
`.getWorldDirection()`
`Object3D`类有一个获取模型局部z轴方向相关的方法`.getWorldDirection()`。

`obj.getWorldDirection()`表示的获取obj对象自身z轴正方向在世界坐标空间中的方向

模型没有任何旋转情况，`.getWorldDirection()`获取的结果(0,0,1)

```js
const mesh = new THREE.Mesh();
const dir = new THREE.Vector3();
mesh.getWorldDirection(dir);
console.log('dir', dir);
```
模型绕y旋转90度情况，.getWorldDirection()获取的结果(1,0,0)
```js
const mesh = new THREE.Mesh();
mesh.rotateY(Math.PI / 2);
const dir = new THREE.Vector3();
mesh.getWorldDirection(dir);
// 模型没有任何选择打印结果(1,0,0)
console.log('dir', dir);
```
## .getWorldDirection()获取玩家角色正前方
注意：threejs加载的玩家角色gltf模型，自身.rotation没有任何旋转的情况下，注意玩家角色正前方方向最好和z轴方向一致,这样就可以直接用.getWorldDirection()获取的结果表示人的正前方。
```js
// 按下W键，实时计算当前玩家角色的正前方向
if (keyStates.W) {
    const front = new THREE.Vector3();
    //获取玩家角色(相机)正前方
    player.getWorldDirection(front);
}
```
## S键运动方向
注意S键运动方向与W的正前方相反，这时候很简单，可以计算方向的时候，把front取反，或者最简单加速度设置一个负号`front.multiplyScalar(- a * deltaTime)`

```js
function render() {
    if (v.length() < vMax) {//限制最高速度
        if (keyStates.W) {
            const front = new THREE.Vector3();
            player.getWorldDirection(front);//获取玩家角色(相机)正前方
            v.add(front.multiplyScalar(a * deltaTime));
        }
        if (keyStates.S) {
            const front = new THREE.Vector3();
            player.getWorldDirection(front);
            // - a：与W按键反向相反
            v.add(front.multiplyScalar(- a * deltaTime));
        }
    }
}    

```