## 鼠标上下移动只改变相机视角
通过鼠标左右移动，旋转玩家角色模型player，相机跟着player同步旋转。
```js
player.add(camera);//相机作为player子对象

document.addEventListener('mousemove', (event) => {
    if(leftButtonBool){
        player.rotation.y -= event.movementX / 600;
    } 
});

```
鼠标上下移动后，只改变相机视角，但是不改变玩家角色模型的姿态角度,换句话说，就是玩家角色模型始终站在地面上不会倾斜。

## 有问题：通过player改变相机上下俯仰视角
`event.movementY`的值改变`player.rotation.x`的值，这样虽然可以通过player控制子对象相机视角上下俯仰，但是玩家角色模型也必须跟着旋转，这样会改变人与地面位置关系，你可以思考下，该怎么解决？

```js
document.addEventListener('mousemove', (event) => {
    if(leftButtonBool){
        // 左右旋转
        player.rotation.y -= event.movementX / 600;
        // 玩家角色绕x轴旋转  视角上下俯仰
        player.rotation.x -= event.movementY / 600;
    } 
});

```
## 鼠标上下移动只改变相机视角，不改变player角度
可以在相机camera和玩家角色模型player之间，嵌入一个子节点cameraGroup，作为相机的父对象，作为玩家角色模型player的子对象。
```js
// 层级关系：player  <—— camera
player.add(camera);//相机作为player子对象

```
```js
// 层级关系：player <—— cameraGroup <—— camera
const cameraGroup = new THREE.Group();
cameraGroup.add(camera);
player.add(cameraGroup);

```
通过`camera`的父对象`cameraGroup`控制相机姿态角度变化。
```js
document.addEventListener('mousemove', (event) => {
    if(leftButtonBool){
        // 左右旋转
        player.rotation.y -= event.movementX / 600;
        // 鼠标上下滑动，让相机视线上下转动
        // 相机父对象cameraGroup绕着x轴旋转,camera跟着转动
        cameraGroup.rotation.x -= event.movementY / 600;
    } 
});

```
## 限制视线上下浮动范围

你可以根据需要，约束上下浮动角度范围，比如我设置上下俯仰范围-15度~15度，共30度。

思路很简单，一旦判断`.rotation.x`小于-15度，就设置为-15度，大于15度，就设置为15度

```js
// 上下俯仰角度范围
const angleMin = THREE.MathUtils.degToRad(-15);//角度转弧度
const angleMax = THREE.MathUtils.degToRad(15);
document.addEventListener('mousemove', (event) => {
    if(leftButtonBool){
        // 左右旋转
        player.rotation.y -= event.movementX / 600;
        // 鼠标上下滑动，让相机视线上下转动
        // 相机父对象cameraGroup绕着x轴旋转,camera跟着转动
        cameraGroup.rotation.x -= event.movementY / 600;
        // 一旦判断.rotation.x小于-15，就设置为-15，大于15，就设置为15
        if (cameraGroup.rotation.x < angleMin) {
            cameraGroup.rotation.x = angleMin;
        }
        if (cameraGroup.rotation.x > angleMax) {
            cameraGroup.rotation.x = angleMax
        };
    } 
});

```

##  玩家角色左右运动(叉乘)
## 叉乘计算左右方向
两个向量a、b叉乘有一个特点，叉乘结果是一个同时垂直于a和b的向量。这就是说只要知道玩家角色模型人正前方方向和高度方向向量，就可以计算出来人的左右方向。

小技巧：叉乘获得垂直于向量up和front的向量,左右方向与叉乘顺序有关,左右方向，可以用右手螺旋定责判断，但是比较麻烦，如果你懒得思考，干脆不用右手螺旋定则判断，代码测试测试下最简单，先随便写一个叉乘顺序，如果不对，就把up和front叉乘顺序换下。

## 玩家角色的正前方
```js
const front = new THREE.Vector3();
player.getWorldDirection(front);

```
玩家角色的高度方向(竖直方向)

```js
const up = new THREE.Vector3(0, 1, 0);//y方向
```
A和D按键对应的方向计算代码。
```js
if (keyStates.A) {//向左运动
    const front = new THREE.Vector3();
    player.getWorldDirection(front);
    const up = new THREE.Vector3(0, 1, 0);//y方向

    const left = up.clone().cross(front);
    v.add(left.multiplyScalar(a * deltaTime));
}
if (keyStates.D) {//向右运动
    const front = new THREE.Vector3();
    player.getWorldDirection(front);
    const up = new THREE.Vector3(0, 1, 0);//y方向
    //叉乘获得垂直于向量up和front的向量 左右与叉乘顺序有关,可以用右手螺旋定则判断，也可以代码测试结合3D场景观察验证
    const right = front.clone().cross(up);
    v.add(right.multiplyScalar(a * deltaTime));
}

```
## 鼠标滑动改变视角(指针锁定模式)
前面改变相机左右和上下视角，用的是鼠标拖动方式，本节课给大家演示一个游戏、元宇宙项目中，常用的不用拖动，鼠标直接滑动改变相机玩家角色或相机视角

你可以把原来鼠标左键拖动改变视角的代码if (leftButtonBool) {}，if条件去掉，只剩下鼠标滑动改变视角，你会发现并不是很好控制，有个问题，鼠标离开网页后，无法在旋转玩家视角，那么怎么解决？请看下面关于指针锁定模式的介绍。

```js
let leftButtonBool = false;//记录鼠标左键状态
document.addEventListener('mousedown', (event) => {
    leftButtonBool = true;
});
document.addEventListener('mouseup', () => {
    leftButtonBool = false;
});

document.addEventListener('mousemove', (event) => {
    if (leftButtonBool) {//根据左键按下拖动才起作用
        player.rotation.y -= event.movementX / 600;
        cameraGroup.rotation.x -= event.movementY / 600;

    }
});
```
请求指针锁定`requestPointerLock`
鼠标滑动时候，会受到浏览器网页页面窗口范围限制，不能无限制移动，这时候可以通过执行requestPointerLock()请求指针锁定。
```js
// 当鼠标左键按下后进入指针锁定模式(鼠标无限滑动)
addEventListener( 'mousedown', () => {
    document.body.requestPointerLock();//body页面指针锁定
});

```
你执行document.body.requestPointerLock();以后，意味着document.pointerLockElement属性，会拥有一个值document.body

```js
if(document.pointerLockElement == document.body){
    // 指针锁定模式下，才能执行的代码
}

```
## 在指针锁定模式下，改变玩家人姿态角度
进入指针模式后，才能根据鼠标位置控制人旋转

通过document.pointerLockElement判断web页面是否进入指针锁定模式。

鼠标点击页面进入指针锁定模式的时候，点击位置默认鼠标的坐标为原点，左右方向是x坐标.movementX(单位像素)，上下方面是y坐标.movementY。

```js
// 人和相机初始姿态正前方：沿着z轴正半轴方向
//鼠标左右移动，人绕y轴旋转
addEventListener('mousemove', (event) => {
    // 进入指针模式后，才能根据鼠标位置控制人旋转
    if (document.pointerLockElement == document.body) {
        // 鼠标左右滑动，让人左右转向(绕y轴旋转)，相机会父对象人绕左右转向
        //加减法根据左右方向对应关系设置，缩放倍数根据，相应敏感度设置
        person.rotation.y -= event.movementX / 500;
    }
});
```
## 快捷键切换第一、第三人称
注意一点透视投影相机fov视野的角度 (opens new window)值会影响，相机与人距离的设置。
```js
const camera = new THREE.PerspectiveCamera(30,...);
//玩家角色后面一点  对应fov 30度
camera.position.set(0, 1.6, -5.5);
```
根据透视投影相机规律，fov变大，能够看到的视野范围角度更大。
```js
const camera = new THREE.PerspectiveCamera(70,...);
//玩家角色后面一点  对应fov 70度
camera.position.set(0, 1.6, -2.3);

```
## 第一人称
第一人称，简单点说，就是看不到玩家角色的模型，相当于把相机放在人的前面
```js
// camera.position.set(0, 1.6, -2.3);//第三人称
// camera.lookAt(0, 1.6, 0);
camera.position.set(0, 1.6, 1);//第一人称
camera.lookAt(0, 1.6, 2);//目标观察点注意在相机位置前面一点
```
如果lookAt后面执行第一人称代码，不重新执行camera.lookAt,视线方向还是原来的。
```js
// z距离人远近具体值，可以根据模型尺寸去测试调节
camera.position.set(0, 1.6, -2.3);//第三人称
camera.lookAt(0, 1.6, 0);
camera.position.set(0, 1.6, 1);//第一人称
```

## 第一、第三人称切换
```js
let viewBool = true;//true表示第三人称，false表示第一人称
document.addEventListener('keydown', (event) => {
    if (event.code === 'KeyV') {
        if (viewBool) {
            // 切换到第一人称
            camera.position.z = 1;//相机在人前面一点 看不到人模型即可
        } else {
            // 切换到第三人称
            camera.position.z = -2.3;//相机在人后面一点
        }
        viewBool = !viewBool;
    }
});

```