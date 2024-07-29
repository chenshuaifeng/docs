## 阴影shadow

### spotLight聚光灯
`focus`: shadow.focus 控制阴影的遮挡范围 [0，1] 

## 使用canvas创建阴影
```js
				const canvas = document.createElement( 'canvas' );
				canvas.width = 128;
				canvas.height = 128;

				const context = canvas.getContext( '2d' );
				const gradient = context.createRadialGradient( canvas.width / 2, canvas.height / 2, 0, canvas.width / 2, canvas.height / 2, canvas.width / 2 );
				gradient.addColorStop( 0.1, 'rgba(210,210,210,1)' );
				gradient.addColorStop( 1, 'rgba(255,255,255,1)' );

				context.fillStyle = gradient;
				context.fillRect( 0, 0, canvas.width, canvas.height );

				const shadowTexture = new THREE.CanvasTexture( canvas );

				const shadowMaterial = new THREE.MeshBasicMaterial( { map: shadowTexture } );
				const shadowGeo = new THREE.PlaneGeometry( 300, 300, 1, 1 );

				let shadowMesh;

				shadowMesh = new THREE.Mesh( shadowGeo, shadowMaterial );
				shadowMesh.position.y = - 250;
				shadowMesh.rotation.x = - Math.PI / 2;
				scene.add( shadowMesh );

				shadowMesh = new THREE.Mesh( shadowGeo, shadowMaterial );
				shadowMesh.position.y = - 250;
				shadowMesh.position.x = - 400;
				shadowMesh.rotation.x = - Math.PI / 2;
				scene.add( shadowMesh );

				shadowMesh = new THREE.Mesh( shadowGeo, shadowMaterial );
				shadowMesh.position.y = - 250;
				shadowMesh.position.x = 400;
				shadowMesh.rotation.x = - Math.PI / 2;
				scene.add( shadowMesh );
```

## 调整Shader阴影

用来控制灯光产生阴影的范围
```js
dirLight.castShadow = true;
dirLight.shadow.mapSize.width = 2048;
dirLight.shadow.mapSize.height = 2048;
dirLight.shadow.camera.left = -50;
dirLight.shadow.camera.right = 50;
dirLight.shadow.camera.top = 50;
dirLight.shadow.camera.bottom = -50;
```