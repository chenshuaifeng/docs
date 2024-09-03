LookAt的应用
lookAt是一个在三维运动物体中十分重要得API
1. 让物体看向运动的物体，改变方向
```js
// misc_lookat
for ( let i = 1, l = scene.children.length; i < l; i ++ ) {
    scene.children[ i ].lookAt( sphere.position );
}

```