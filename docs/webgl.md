WebGL 备忘清单
===

WebGL 是一个用于在 Web 浏览器中渲染 2D 和 3D 图形的 JavaScript API。以下是一些常用的 WebGL 操作和用法。

入门
---

### 初始化 WebGL 上下文

```javascript
const canvas = document.getElementById('canvas');
const gl = canvas.getContext('webgl');

if (!gl) {
  console.error('WebGL not supported');
}
```

### 清空画布

```javascript
gl.clearColor(0.0, 0.0, 0.0, 1.0);
gl.clear(gl.COLOR_BUFFER_BIT);
```

### 创建着色器

```javascript
function createShader(gl, type, source) {
  const shader = gl.createShader(type);
  gl.shaderSource(shader, source);
  gl.compileShader(shader);

  if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
    console.error(gl.getShaderInfoLog(shader));
    gl.deleteShader(shader);
    return null;
  }
  return shader;
}

const vertexShaderSource = `...`;
const fragmentShaderSource = `...`;

const vertexShader = createShader(gl, gl.VERTEX_SHADER, vertexShaderSource);
const fragmentShader = createShader(gl, gl.FRAGMENT_SHADER, fragmentShaderSource);
```

### 创建着色器程序

```javascript
function createProgram(gl, vertexShader, fragmentShader) {
  const program = gl.createProgram();
  gl.attachShader(program, vertexShader);
  gl.attachShader(program, fragmentShader);
  gl.linkProgram(program);

  if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
    console.error(gl.getProgramInfoLog(program));
    gl.deleteProgram(program);
    return null;
  }
  return program;
}

const program = createProgram(gl, vertexShader, fragmentShader);
gl.useProgram(program);
```

### 设置顶点数据

```javascript
const vertices = new Float32Array([
  0.0,  0.5,
  -0.5, -0.5,
  0.5, -0.5
]);

const buffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
gl.bufferData(gl.ARRAY_BUFFER, vertices, gl.STATIC_DRAW);

const position = gl.getAttribLocation(program, 'a_position');
gl.enableVertexAttribArray(position);
gl.vertexAttribPointer(position, 2, gl.FLOAT, false, 0, 0);
```

### 绘制图形

```javascript
gl.clear(gl.COLOR_BUFFER_BIT);
gl.drawArrays(gl.TRIANGLES, 0, 3);
```

着色器
---

### 顶点着色器示例

```javascript
const vertexShaderSource = `
attribute vec4 a_position;

void main() {
  gl_Position = a_position;
}
`;
```

### 片段着色器示例

```javascript
const fragmentShaderSource = `
precision mediump float;

void main() {
  gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0);
}
`;
```

纹理
---

### 加载纹理

```javascript
const texture = gl.createTexture();
gl.bindTexture(gl.TEXTURE_2D, texture);

const image = new Image();
image.src = 'path/to/image.png';
image.onload = () => {
  gl.bindTexture(gl.TEXTURE_2D, texture);
  gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, image);
  gl.generateMipmap(gl.TEXTURE_2D);
};
```

### 设置纹理参数

```javascript
gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
```

矩阵与变换
---

### 使用 glMatrix 库

```javascript
import { mat4 } from 'gl-matrix';

const modelMatrix = mat4.create();
mat4.translate(modelMatrix, modelMatrix, [0.0, 0.0, -5.0]);
mat4.rotate(modelMatrix, modelMatrix, Math.PI / 4, [0, 1, 0]);
```

### 传递矩阵到着色器

```javascript
const uModelMatrix = gl.getUniformLocation(program, 'u_modelMatrix');
gl.uniformMatrix4fv(uModelMatrix, false, modelMatrix);
```

缓冲区
---

### 创建和绑定缓冲区

```javascript
const buffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
```

### 传递数据到缓冲区

```javascript
const vertices = new Float32Array([
  0.0,  0.5,
  -0.5, -0.5,
  0.5, -0.5
]);
gl.bufferData(gl.ARRAY_BUFFER, vertices, gl.STATIC_DRAW);
```

### 配置顶点属性指针

```javascript
const position = gl.getAttribLocation(program, 'a_position');
gl.enableVertexAttribArray(position);
gl.vertexAttribPointer(position, 2, gl.FLOAT, false, 0, 0);
```

调试与优化
---

### 调试 WebGL

- 使用 `WEBGL_debug_renderer_info` 扩展获取图形卡和驱动程序信息
- 使用 Chrome 或 Firefox 开发者工具的 WebGL 检查器

### 性能优化

- 使用 `gl.bufferSubData` 而不是 `gl.bufferData` 更新缓冲区数据
- 合理使用纹理，避免频繁切换绑定的纹理
- 尽量减少 draw call 数量

工具
---

### 有用的库

- [glMatrix](http://glmatrix.net/)：高效的矩阵和向量运算库
- [twgl.js](https://github.com/greggman/twgl.js)：简化 WebGL API 的库

### 资源

- [WebGL Fundamentals](https://webglfundamentals.org/)
- [MDN WebGL 文档](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)

另见
---

- [WebGL 官方规范](https://www.khronos.org/registry/webgl/specs/latest/1.0/)
- [WebGL Insights](https://www.webglinsights.com/)
