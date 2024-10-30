## Helper

helper是有材质的
```js
	const grid = new THREE.GridHelper( 200, 40, 0x000000, 0x000000 );
				grid.material.opacity = 0.2;
				grid.material.transparent = true;
```

## GUI

options([])：下拉框   

```js
function initGUI( layers ) {
	gui = new GUI( { title: 'layers' } );
	for ( let i = 0; i < layers.length; i ++ ) {
		const layer = layers[ i ];
		gui.add( layer, 'visible' ).name( layer.name ).onChange( function ( val ) {
			const name = this.object.name;
			scene.traverse( function ( child ) {
				if ( child.userData.hasOwnProperty( 'attributes' ) ) {
					if ( 'layerIndex' in child.userData.attributes ) {
						const layerName = layers[ child.userData.attributes.layerIndex ].name;
						if ( layerName === name ) {
							child.visible = val;
							layer.visible = val;
						}
					}
				}
			} );
		} );
	}
}
```