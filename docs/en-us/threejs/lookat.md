LookAt的应用

1. 让物体看向运动的物体，改变方向
```js
// misc_lookat
for ( let i = 1, l = scene.children.length; i < l; i ++ ) {
    scene.children[ i ].lookAt( sphere.position );
}

```