1. 俯仰角elevation、方位角azimuth
elevation 
- 球形物体高度控制 
- 范围0~90。 0度时与x轴平行，90度时垂直
- 转为弧度制THREE.MathUtils.degToRad( 90 - parameters.elevation )

2. 使用鼠标按下、移动事件旋转rotation
```js
pointerXOnPointerDown = event.clientX - windowHalfX;
targetRotationOnPointerDown = targetRotation;

pointerX = event.clientX - windowHalfX;
targetRotation = targetRotationOnPointerDown + ( pointerX - pointerXOnPointerDown ) * 0.02;
```

3. 更新局部举证`updateMatrix`
当给mesh指定positin\rotation\scale后，mesh的矩阵之前更新
```js
// 示例：webgl_postprocessing_crossfade
dummy.updateMatrix();
mesh.setMatrixAt( i, dummy.matrix );
```


1. morph 变形

geometry:morphAttributes.position[0] morph的position与atrribute不一样。有个position属性，值为数组
mesh:morphTargetInfluences权重；morphTargetDictionary：索引



TODO:
1.webgl_instancing_scatter

TODO:
自定义shader
1. webgl_buffergeometry_instancing_billboards
2. webgl_buffergeometry_instancing: 自定义Shader + InstanceBuffferAttribute
3. webgl_custom_attributes_lines： 自定义Shader + BufferAttibute
4. webgl_custom_attributes_points
5. webgl_depth_texture
6. webgl_interactive_points：光线拾取
7. webgl_lights_hemisphere：
8. webgl_materials_curvature
9. webgl_materials_modified: 自定义Shader动态修改
10. webgl_materials_wireframe
11. webgl_modifier_tessellation：修改器Modify、自定义着色器

TODO2:
比较特殊的示例：
1. misc_controls_map： 控制器很陌生
2. misc_controls_pointerlock： 涉及到向量的速度计算、速度插值、相机位置、动态射线拾取
3. webaudio_sandbox： 视频播放相关API集成
4. webgl_buffergeometry_compression：性能优化，对集合点的压缩
5. webgl_buffergeometry_drawrange： 粒子系统、线、运动的粒子
6. webgl_buffergeometry_lines_indexed: 线的典型案例
7. webgl_buffergeometry_lines： morph与线的结合
8. webgl_buffergeometry_selective_draw：线的显示隐藏与线的创建
9. webgl_clipping_advanced：裁剪、矩阵的计算
10. webgl_decals: 向量的几何运算
11. webgl_geometry_extrude_shapes2：挤压几何体与形状几何体的综合运用
12. webgl_geometry_minecraft： 噪点及矩阵
13. webgl_geometry_nurbs：曲线和鼠标交互
14. webgl_geometry_terrain_raycast： 地形生成器与光线拾取
15. webgl_instancing_performance：多实例、性能
16. webgl_interactive_cubes_gpu：渲染器运用
17. webgl_interactive_lines：相机、矩阵、光线拾取、线
18. webgl_interactive_raycasting_points： 运动点云、光线拾取
19. webgl_interactive_voxelpainter：向量计算、光线拾取
20. webgl_lensflares： 光线内置透镜耀斑
21. webgl_lightningstrike：光、闪电
22. webgl_lights_spotlights： 光线、补间动画
23. webgl_lines_fat_raycasting： 线、光线拾取、segmentsGeometry
24. webgl_lines_fat_wireframe： 多场景应用
25. webgl_lines_sphere：动态的线
26. webgl_marchingcubes: 内置Shader应用、Shader工具、材质切换
27. webgl_materials_channels：矩阵变换、材质贴图、自定义Shader
28. webgl_materials_cubemap_dynamic：动态的环境贴图
29. webgl_materials_texture_canvas： Canvan实时自动更新
30. webgl_modifier_subdivision: 修改器modify的综合应用


TODO2：
细节知识
1. webgl_materials_texture_manualmipmap: 纹理的mipmaps属性对纹理效果的影响  
2. webgl_materials_texture_partialupdate: 纹理的粒子系统更新、DataTexture的应用
3. webgl_mirror: 镜像
4. webgl_modifier_curve_instanced： 插值运动、曲线

TODO3
屏幕交互：
1. webgl_math_obb : 射线拾取_点击事件、碰撞检测、矩阵运算
射线拾取多个，距离点击点最近的
2. webgl_math_orientation_transform: 矩阵、四元数、数学计算


webgl_morphtargets_face


