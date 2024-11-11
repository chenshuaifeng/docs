## 标注
CSS3DRenderer CSS3DObject

1. 创建元素：元素种类：div\iframe\video

2. 创建css3DObject,并将标签传入到Object

3. CSS3DRenderer 代替 WebGLRenderer,注意只能add CSSObject3D

`css3dRenderer.domElement.style.position`


## CSS3DSprite
```js
import { CSS3DRenderer, CSS3DSprite } from 'three/addons/renderers/CSS3DRenderer.js';

const image = document.createElement( 'img' );
				image.addEventListener( 'load', function () {

					for ( let i = 0; i < particlesTotal; i ++ ) {

						const object = new CSS3DSprite( image.cloneNode() );
						object.position.x = Math.random() * 4000 - 2000,
						object.position.y = Math.random() * 4000 - 2000,
						object.position.z = Math.random() * 4000 - 2000;
						scene.add( object );

						objects.push( object );

					}

					transition();

				} );
				image.src = 'textures/sprite.png';

```

一个三D平面的算法,已三角函数波得形式
```js
// Plane

				const amountX = 16;
				const amountZ = 32;
				const separationPlane = 150;
                // 偏移的一半，x负半轴， z的负半轴开始
				const offsetX = ( ( amountX - 1 ) * separationPlane ) / 2;
				const offsetZ = ( ( amountZ - 1 ) * separationPlane ) / 2;

				for ( let i = 0; i < particlesTotal; i ++ ) {
					// 取模运算 x = [0 , 15]
					const x = ( i % amountX ) * separationPlane;
                    // 当x= [0, 15时], z的值是0 
					const z = Math.floor( i / amountX ) * separationPlane;
                    // 正玄波动函数
					const y = ( Math.sin( x * 0.5 ) + Math.sin( z * 0.5 ) ) * 200;

					positions.push( x - offsetX, y, z - offsetZ );

				}
	// Cube

				const amount = 8;
				const separationCube = 150;
				const offset = ( ( amount - 1 ) * separationCube ) / 2;

				for ( let i = 0; i < particlesTotal; i ++ ) {

					const x = ( i % amount ) * separationCube;
					const y = Math.floor( ( i / amount ) % amount ) * separationCube;
					const z = Math.floor( i / ( amount * amount ) ) * separationCube;

					positions.push( x - offset, y - offset, z - offset );

				}

                	// Random

				for ( let i = 0; i < particlesTotal; i ++ ) {

					positions.push(
						Math.random() * 4000 - 2000,
						Math.random() * 4000 - 2000,
						Math.random() * 4000 - 2000
					);

				}

				// Sphere

				const radius = 750;

				for ( let i = 0; i < particlesTotal; i ++ ) {
                    // 球坐标算法
					const phi = Math.acos( - 1 + ( 2 * i ) / particlesTotal );
					const theta = Math.sqrt( particlesTotal * Math.PI ) * phi;

					positions.push(
						radius * Math.cos( theta ) * Math.sin( phi ),
						radius * Math.sin( theta ) * Math.sin( phi ),
						radius * Math.cos( phi )
					);

				}
```

## ElementImage\Canvas与CSS3D相结合
示例:css3d_molecules

1. 使用Canvas+图片创建自定义图片
2. 单独给图片添加颜色
```js
function colorify( ctx, width, height, color ) {

				const r = color.r, g = color.g, b = color.b;

				const imageData = ctx.getImageData( 0, 0, width, height );
				const data = imageData.data;

				for ( let i = 0, l = data.length; i < l; i += 4 ) {

					data[ i + 0 ] *= r;
					data[ i + 1 ] *= g;
					data[ i + 2 ] *= b;

				}

				ctx.putImageData( imageData, 0, 0 );

			}
```


> 如何将所有的小球用线连起来  

positionBonds存储着所有的连线关系   
两个点确定一条线,分别是i和i+1



