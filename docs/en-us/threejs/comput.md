> 鼠标移动物体指定位置算法webgl_interactive_voxelpainter

1. 将position拷贝给物体
```js
rollOverMesh.position.copy( intersect.point ).add( intersect.face.normal );
```
点的位置加上面的法线,法线是单位向量，向量加上单位向量， 将物体从相交点沿着相交面的法线移动一段距离。
这个操作通常用于将物体“推出”到相交面的表面之外，以避免它嵌入到相交物体内部

2. 操作向量,使得鼠标在附近区域游走时，物体位置不发生变化,当超出特定区域后在发生位移变化
```js
rollOverMesh.position.divideScalar( 50 ).floor().multiplyScalar( 50 ).addScalar( 25 );
```
- divideScalar(50)：将位置向量的每个分量除以50，这通常用于缩放位置向量。   
- floor()：将位置向量的每个分量向下取整。这个操作可能会改变物体的位置，使其落在某个由50个单位组成的网格的格点上。 
- multiplyScalar(50)：将上一步得到的结果再乘以50，以恢复或改变原始的缩放比例。这个操作可能是为了将物体移动到最接近的、但仍然是网格上某点的位置。 
- addScalar(25)：将位置向量的每个分量增加25。这个操作可能是为了将物体沿所有方向稍微移动一点，以避免它完全位于网格的某个特定点上（例如，网格的原点）。  


> 在相同的地方点击如何新增物体

1. 捕获新物体
2. 重复1、2动作