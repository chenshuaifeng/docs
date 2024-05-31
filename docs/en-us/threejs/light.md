
### 聚光灯SportLight

- `angle`: Float
光线照射范围的角度，用弧度表示。不应超过 Math.PI/2。默认值为 Math.PI/3。


## RoomEnvironment 环境光
THREEJS内置的环境光纹理shdow
PMREM 是一种用于物理渲染（特别是基于物理的材质和光照）的优化技术，它允许你以较低的成本在多个不同的粗糙度级别上模拟光照
```JS
// 假设你已经有一个 THREE.WebGLRenderer 实例和一个 HDRI 纹理  
let renderer = new THREE.WebGLRenderer();  
let hdriTexture = ...; // 从某个源加载的 HDRI 纹理  
  
// 创建一个 PMREMGenerator 实例  
let pmremGenerator = new THREE.PMREMGenerator(renderer);  
  
// 使用 PMREMGenerator 从 HDRI 纹理生成 PMREM  
pmremGenerator.compileEquirectangularShader();  
let pmrem = pmremGenerator.fromEquirectangular(hdriTexture).texture;  
  
// 将生成的 PMREM 设置为场景的 environment 属性  
scene.environment = pmrem;
```


