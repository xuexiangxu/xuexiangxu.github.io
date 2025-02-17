---
title: SPA首屏加载优化
date: 2025-02-17 15:31:01
tags: [Frontend, SPA]
---

单页面应用（SPA）在首次加载时通常需要加载大量的 JavaScript、CSS 和 API 请求，导致**首屏渲染（First Paint, FP）和首屏可交互时间（Time to Interactive, TTI）较慢**。以下是一些常见的优化策略：

---

## **1. 资源优化**

### **1.1 代码分割（Code Splitting）**

**问题**：SPA 需要加载大量 JavaScript 文件，导致首屏加载变慢。  
**解决方案**：使用 **Webpack/Vite** 进行代码分割，按需加载（懒加载）。  
🔹 **使用动态 `import()` 进行路由懒加载**

```js
import { lazy, Suspense } from "react";

const Home = lazy(() => import("./Home"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Home />
    </Suspense>
  );
}
```

✅ **减少首屏需要下载的 JS 体积，提高加载速度。**

---

### **1.2 资源压缩（Minification & Compression）**

**问题**：未压缩的 JS、CSS、HTML 体积较大，影响加载速度。  
**解决方案**：

- **JS/CSS 压缩**：使用 Webpack `TerserPlugin` 或 Vite 内置压缩。
- **Gzip/Brotli** 压缩：在服务器端开启压缩，减少传输体积。  
  - **Nginx 配置 Gzip**

    ```nginx
    gzip on;
    gzip_types text/css application/javascript;
    gzip_min_length 1024;
    ```

  - **Brotli（更优）**

    ```nginx
    brotli on;
    brotli_types text/html text/css application/javascript;
    ```

✅ **减少资源体积，提高传输速度。**

---

### **1.3 Tree Shaking（去除无用代码）**

**问题**：项目可能引入了不必要的代码。  
**解决方案**：使用 Webpack/Vite **Tree Shaking**，去除未使用的代码：

```js
import { add } from "lodash";  // 只导入 add，避免导入整个 lodash
```

✅ **减少 JS 体积，避免加载无用代码。**

---

## **2. 静态资源优化**

### **2.1 图片优化**

**问题**：大图片加载缓慢，影响首屏渲染。  
**解决方案**：

- **使用 WebP/AVIF 格式**（比 PNG/JPG 体积更小）
- **图片懒加载**

  ```html
  <img src="placeholder.jpg" data-src="large-image.jpg" loading="lazy" />
  ```

- **CDN 加速图片**

  ```html
  <img src="https://cdn.example.com/image.webp" />
  ```

✅ **减少图片加载时间，提高首屏速度。**

---

### **2.2 CSS 优化**

**问题**：CSS 过大，导致阻塞渲染。  
**解决方案**：

- **Critical CSS**（关键 CSS 内联，减少阻塞）

  ```html
  <style>
    body { font-size: 16px; }
  </style>
  ```

- **使用 Tailwind CSS / CSS Modules**，避免加载无用样式。
- **CSS 代码分割**

  ```js
  import "./styles.css";  // 仅在需要的页面加载
  ```

✅ **提高首屏渲染速度，减少 CSS 阻塞。**

---

## **3. 网络优化**

### **3.1 HTTP/2 和 HTTP/3**

**问题**：传统 HTTP/1.1 需要多次请求才能加载多个资源。  
**解决方案**：

- **使用 HTTP/2 或 HTTP/3** 进行多路复用，减少请求开销。

✅ **减少请求阻塞，提高资源加载效率。**

---

### **3.2 使用 CDN**

**问题**：资源请求速度受服务器物理距离影响。  
**解决方案**：

- **使用 CDN**（如 Cloudflare、Fastly、阿里云 CDN）
- **缓存静态资源**

  ```nginx
  location /static/ {
      expires 30d;
      add_header Cache-Control "public, max-age=2592000";
  }
  ```

✅ **加速资源加载，减少服务器压力。**

---

### **3.3 预加载 & 预渲染**

**问题**：SPA 需要等 JS 解析完后再加载页面。  
**解决方案**：

- **预加载重要资源**

  ```html
  <link rel="preload" href="styles.css" as="style">
  ```

- **DNS 预解析**

  ```html
  <link rel="dns-prefetch" href="//cdn.example.com">
  ```

✅ **减少关键资源加载时间。**

---

## **4. 首屏渲染优化**

### **4.1 服务端渲染（SSR）**

**问题**：SPA 需要等 JavaScript 加载后才能渲染，影响**首屏白屏时间**。  
**解决方案**：

- **使用 Next.js 进行 SSR**

  ```js
  export async function getServerSideProps() {
      const data = await fetchData();
      return { props: { data } };
  }
  ```

✅ **SSR 提前渲染 HTML，提高首屏加载速度。**

---

### **4.2 预渲染（Prerendering）**

**问题**：SSR 适合动态页面，但有时内容是固定的。  
**解决方案**：

- **使用 prerender-spa-plugin 进行静态预渲染**

  ```js
  new PrerenderSPAPlugin({
      staticDir: path.join(__dirname, 'dist'),
      routes: ['/home', '/about']
  });
  ```

✅ **适用于静态页面，避免 SSR 服务器压力。**

---

### **4.3 骨架屏（Skeleton Screen）**

**问题**：数据未加载时，用户看到白屏，影响体验。  
**解决方案**：

- **使用 CSS 占位**

  ```html
  <div class="skeleton"></div>
  ```

- **使用 React/Vue 组件**

  ```js
  function Skeleton() {
      return <div className="skeleton">Loading...</div>;
  }
  ```

✅ **提升用户体验，减少白屏时间。**

---

## **总结**

| 优化方式 | 方法 |
|----------|------|
| **资源优化** | 代码分割、JS/CSS 压缩、Tree Shaking |
| **静态资源优化** | 图片优化、关键 CSS 内联、CDN |
| **网络优化** | HTTP/2、预加载、缓存优化 |
| **首屏渲染优化** | SSR、预渲染、骨架屏 |

✅ **最重要的优化方案**：

1. **使用 `<!DOCTYPE html>` 触发标准模式**（避免浏览器渲染异常）
2. **减少 JS 体积**（代码分割、Tree Shaking、Gzip 压缩）
3. **优化图片 & CSS 资源加载**（WebP/CDN/懒加载）
4. **使用 SSR / 预渲染 / 骨架屏**（减少首屏白屏时间）
