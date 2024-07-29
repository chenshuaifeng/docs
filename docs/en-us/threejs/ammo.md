Ammo物理引擎

构造一个物理世界的步骤：
1. 物理世界Physics
2. 图形世界Graphics
3. 在几何世界中绑定物理世界btTransform，
	3.1 设置初始位置、姿态
	3.2 设置碰撞几何结构
	3.3 创建刚体
	3.4 将刚体添加到物理世界中
4. 动画中更新物理世界
	4.1 同步图形世界
	4.2 物体mesh
	4.3 刚体body


异步加载机制
Ammo().then(() => {

})

```js
function initPhysics() {

			// Physics configuration
            // 默认配置
			collisionConfiguration = new Ammo.btDefaultCollisionConfiguration();
            // 
			dispatcher = new Ammo.btCollisionDispatcher( collisionConfiguration );
			broadphase = new Ammo.btDbvtBroadphase();
			solver = new Ammo.btSequentialImpulseConstraintSolver();
			physicsWorld = new Ammo.btDiscreteDynamicsWorld( dispatcher, broadphase, solver, collisionConfiguration );
            
            // 加速度
			physicsWorld.setGravity( new Ammo.btVector3( 0, - gravityConstant, 0 ) );

			transformAux1 = new Ammo.btTransform();
			tempBtVec3_1 = new Ammo.btVector3( 0, 0, 0 );

		}
```

## 物理引擎ammo.js

遇到的问题是引擎引不进，解决办法通过`script`标签引入脚本

```js
// 物理引擎ammo.js的示例
physics_ammo_instancing
```