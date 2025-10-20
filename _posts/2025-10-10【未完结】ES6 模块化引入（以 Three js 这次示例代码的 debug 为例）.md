---
title: 【未完结】ES6 模块化引入（以 Three.js 这次示例代码的 debug 为例）
date: 2025-10-10 15:00:00 +0800
categories: [前端]
tags: 
---

## 前情提要

晚上想要运行下 Shader 官方提供的 Three.js 代码，but 却运行不起来。

[https://thebookofshaders.com/04/?lan=ch](https://thebookofshaders.com/04/?lan=ch)

当然，首先我的任务是 “要看懂这段代码哪一部分分别在干嘛“，并找出核心部分。

经过我对 GPT 的追问~~以及直觉~~，我发现 **核心功能在这一部分：**

（不要管什么 stage、render、camera 乱七八糟的，**把它们当做 “搭基础运行环境” 就行，不用管。**关键在搭完环境后的 “主函数”。）

```jsx
function animate() {
        // 在下一帧（下一次屏幕刷新）时，再执行 animate() 这个函数。
        requestAnimationFrame(animate);
        render();
      }

      function render() {
        // 让 shader 内部的 u_time 持续增长
        // u_time 一变化，颜色就会随时间闪烁或流动
        uniforms.u_time.value += clock.getDelta();
        renderer.render(scene, camera);
      }
```

看懂了，可以放心运行 & debug 了。然后马上就遇到了问题：

## 一些 debug 简记

起初感觉不对劲，是因为：浏览器上什么都不显示，一片空白——这很不合理。

然后，控制台最频繁出现的错误是这个：

> Uncaught ReferenceError: THREE is not defined
> 

这个 THREE 的相关代码见下：

```jsx
     function init() {
        container = document.getElementById("container");

        // THREE is not defined: 脚本执行时，three.js 还没加载完
        camera = new THREE.Camera();
        camera.position.z = 1;

        scene = new THREE.Scene();
        clock = new THREE.Clock();
        
        ...
```

后面发现：果然~~和我第六感感觉的一样~~，错误在于 **没有引入 Three.js 的核心库，**也就是这段代码：

```html
    <!--我们没有这个 js 文件！-->
    <script src="js/three.min.js"></script>
```

- 然后我换成了 cdn 引入，但还是报 `undefined` 错误——遇到了 **异步执行顺序的问题，**但我没办法解决，遂放弃。

```html
<script src="https://cdn.jsdelivr.net/npm/three@0.161.0/build/three.min.js"></script>
```

- 接着我换成去 Github 库下载 js 核心库文件，并在本地引入（来避免异步执行的问题）
    - 我下载了[https://github.com/mrdoob/three.js/tree/dev/build](https://github.com/mrdoob/three.js/tree/dev/build)上的 `three.core.min.js` 并引入。但 `undefined` 错误没消失，还多了一个错误：
    
    ```html
    Uncaught SyntaxError: Unexpected token 'export'
    ```
    
    （参考链接：[**Getting Unexpected Token Export - javascript**](https://stackoverflow.com/questions/38296667/getting-unexpected-token-export)）（大概能知道 **export 引入是 ES6 专属）**
    
    然后 GPT 告诉我：我原来的 `<script>` 引入方式是传统方式，和 ES6 引入 “版本不搭”？
    
    > 
    > 
    > 
    > 这种方式会被浏览器视为 **传统的全局脚本引入方式**（非模块）。
    > 
    > 因此：
    > 
    > - 浏览器会把 three.js 里的所有定义（如 `THREE`）放进全局作用域；
    > - 但如果 three.js 是用 **ES6 模块语法（`export` / `import`）** 写的，
    >     
    >     浏览器就会报错：
    >     
    >     `Uncaught SyntaxError: Unexpected token 'export'`。
    >     
    > 
    > 👉 也就是说，这种传统 `<script>` 引入方式，**不支持解析 ES6 模块语法**。
    > 
    
    ```html
    <!--会被浏览器解析为 传统 JS 引入方式，就会无法识别 ES6 的引入语法-->
    <script src="js/three.min.js"></script>
    ```
    

在用 ES6 语法解决引入问题前 / 后，我先补充下 ES6 模块引入相关的知识：

> 
> 
> 
> ES6 提供了 `type="module"` 属性，让浏览器能识别模块语法：
> 
> ```html
> <script type="module">
>   import * as THREE from "https://cdn.jsdelivr.net/npm/three@0.161.0/build/three.module.js";
> 
>   const scene = new THREE.Scene();
>   ...
> </script>
> 
> ```
> 
> 这种写法的改进之处：
> 
> - 每个文件都有自己的作用域，不会污染全局；
> - 可以用 `import` 精确引入需要的功能；
> - 浏览器原生支持（现代浏览器无需打包器）；
> - 更方便模块化开发和构建。

## ES6 模块引入

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules#模块与经典脚本的其他不同](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules#%E6%A8%A1%E5%9D%97%E4%B8%8E%E7%BB%8F%E5%85%B8%E8%84%9A%E6%9C%AC%E7%9A%84%E5%85%B6%E4%BB%96%E4%B8%8D%E5%90%8C)