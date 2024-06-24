Ammo物理引擎

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