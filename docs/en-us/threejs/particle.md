particle粒子

在使用bufferGeometry计算生成时不要忘了`geometry.computeBoundingBox();`

## 代码示例
鼠标移动过去粒子逐渐变小
```js

for ( let i = 0; i < 40; i ++ ) {

					const sphere = new THREE.Mesh( sphereGeometry, sphereMaterial );
					scene.add( sphere );
					spheres.push( sphere );

				}

                
const intersections = raycaster.intersectObjects( pointclouds, false );
    intersection = ( intersections.length ) > 0 ? intersections[ 0 ] : null;

    if ( toggle > 0.02 && intersection !== null ) {

        spheres[ spheresIndex ].position.copy( intersection.point );
        spheres[ spheresIndex ].scale.set( 1, 1, 1 );
        spheresIndex = ( spheresIndex + 1 ) % spheres.length;

        toggle = 0;

    }

    for ( let i = 0; i < spheres.length; i ++ ) {

        const sphere = spheres[ i ];
        sphere.scale.multiplyScalar( 0.98 );
        sphere.scale.clampScalar( 0.01, 1 );

    }
```