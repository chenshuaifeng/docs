## 多实例物体InstanceMesh(geometry, material, count)

创建由多个物体组成的立方体

```js
let index=0; // 单位物体
const offset=(amount -1)/2;/// 4.5
const matrix = new THREE.Matrix4();

for(let i=0;i<amount;i++){
    for(let j=0;j<amount;j++){
        for(let k=0;k<amount;k++){
            matrix.setPosition(offset-i,offset-j,offset-k);
            meshes.setMatrixAt(index,matrix);
            meshes.setColorAt(index,white);
            index=index + 1;
        }
    }
}
```
- `matrix.setPosition`
将位置坐标保存在矩阵中

- `setMatrixAt` 
At给给定物体添加矩阵数据，.instanceMatrix : InstancedBufferAttribute，表示所有实例的本地变换。 如果你要通过 .setMatrixAt() 来修改实例数据，你必须将它的 needsUpdate 标识为 true 。

- `setColorAt`
给物体添加颜色，instanceColor : InstancedBufferAttribute，如果通过.setColorAt()修改实例化数据，则必须将它的needsUpdate标志设置为 true。

- `getColorAt `
获取当前mesh的颜色

## Color
- `color.equals(white)`
用来判断两个颜色是否相等


## 骨架（Skeleton）
用于人体骨架mesh
