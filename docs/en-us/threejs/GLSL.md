## vue3+vite导入GLSL解决方案
1. 安装依赖
```js
npm i vite-plugin-glsl
```

2. 配置vite
```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import glsl from "vite-plugin-glsl";
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), glsl()],
});

```

3. 解决ts报错
```js
import name form "./test.glsl"

// 在项目的typings目录或直接在src目录下创建shaders.d.ts（有这个文件的可以不用另外创建）
declare module '*.glsl' {
  const value: string;
  export default value;
}
```

在shader中是能够访问到bufferAttribute中的属性的,在shader中也是能够访问到Uniform变量的值


