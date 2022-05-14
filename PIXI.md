## 一、创建舞台 stage

```js
let app = new PIXI.Application({ 
    width: 256,         // default: 800 宽度
    height: 256,        // default: 600 高度
    antialias: true,    // default: false 反锯齿
    transparent: false, // default: false 透明度
    resolution: 1 ,     // default: 1 分辨率
    forceCanvas: true,  // 用canvas去绘制  默认为WebGL
  }
);
app.renderer.backgroundColor = 十六位颜色;//创建画布后更改背景颜色
app.renderer.view.width; // 获取画布的宽
app.renderer.view.height; // 获取画布的高

// 如果要更改画布的大小 使用 renderer.resize
// 为了确保画布的大小调整与分辨率匹配，将autoResize设置为true
app.renderer.autoResize = true;
app.renderer.resize(512, 512);
```

如果你想要让画布填充整个窗口，可以应用下面的 CSS 样式并将渲染器的大小调整到浏览器窗口的大小。

```js
app.renderer.view.style.position = "absolute";
app.renderer.view.style.display = "block";
app.renderer.autoResize = true;
app.renderer.resize(window.innerWidth, window.innerHeight);

// 另外确保所有HTML元素的默认内边距和边距设置为0
<style>* {padding: 0; margin: 0}</style>
```

## 二、创建精灵 sprites

​	加载 

## 三、定位