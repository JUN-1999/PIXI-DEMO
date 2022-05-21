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

### 	加载图形到纹理

因为 Pixi 使用 WebGL 在 GPU 上渲染图像，图像需要转换为 GPU 可以处理的东西，这个就是**纹理**

```js
方式一：使用纹理缓存
let texture = PIXI.utils.TextureCache["images/anySpriteImage.png"]; // texture cache 纹理缓存
let sprite = new PIXI.Sprite(texture); // 使用纹理，通过 Sprite 生成精灵
```

```js
方式二：加载图像文件并将其转换为纹理（一般方式）
PIXI.loader
  .add("images/anyImage.png")
  .load(setup);

function setup() {
  //这段代码将在加载程序加载完图像后运行
    
    // 如果你使用loader（加载器），你应该通过引用loader的resources对象中的纹理来 创建精灵
    let sprite = new PIXI.Sprite(
      PIXI.loader.resources["images/anyImage.png"].texture
    );
}

=======================================================
// 当有多个图像文件想要转换为纹理时，用数组的方式
PIXI.loader
  .add([
    "images/imageOne.png",
    "images/imageTwo.png",
    "images/imageThree.png"
  ])
  .load(setup);
```

### 	显示精灵

```js
//加载图像并使用它创建精灵之后，需要使用Pixi的stage.addChild方法把它添加到PIXI的stage中
app.stage.addChild(cat);

// 删除精灵的方法
// 1.app.stage.removeChild(anySprite)  从舞台上删除一个精灵
// 2.anySprite.visible = false; 将精灵的visible属性设置为false是使精灵消失的一种更简单、更有效的方法
```

### 	使用别名

经常使用的Pixi对象和方法创建简短的别名，您可以节省一些输入，并使您的代码更具可读性。

```js
let TextureCache = PIXI.utils.TextureCache; // 创建TextureCache对象的别名
let texture = TextureCache["images/cat.png"]; // 使用别名 创建了一个精灵
```

## 三、定位

```js
function setup() {

  // 创建一个名为cat的精灵
  let cat = new Sprite(resources["images/cat.png"].texture);

  // 修改cat的x，y坐标位置
  cat.x = 96;
  cat.y = 96;
    
  // sprite.position.set(x, y)  可以一起设置x,y

  // 把cat添加到场景中
  app.stage.addChild(cat);
}
```

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220516163933939.png" alt="image-20220516163933939" style="zoom: 50%;" />坐标是从左上角开始计算，横为x，纵为y

## 四、大小和缩放

### 	大小

```js
cat.width=80; // 设置cat精灵的宽度为80
cat.height=120;// 设置cat精灵的宽度为120
```

### 	缩放

```js
cat.scale.x=0.5; // 按比例改变精灵的宽度
cat.scale.y=0.5; // 按比例改变精灵的高度
// cat.scale.set(x, y);  一起设置
```

## 五、旋转

锚点

```js
cat.rotation = 0.5;// 旋转的弧度值
// 设置锚点 现在修改为0.5，0.5时，是位于精灵的中心
cat.anchor.x = 0.5;  //纹理尺寸的百分比
cat.anchor.y = 0.5;
// cat.anchor.set(x, y)
```

原点

```js
// pivot轴心点设置了精灵的原点 改变了它，然后旋转精灵，精灵会绕着原点旋转旋转
cat.pivot.set(32, 32)//单位是纹理像素值 // 假设精灵是64x64像素，精灵现在会绕着它的中心点旋转。
```

## 六、使用雪碧图制作精灵

```js
loader.add("images/tileset.png").load(setup); // 讲图片转为纹理
function setup() {
  // 获得一个纹理缓存（为什么是缓存？估计是因为现在还不是最后的纹理，还需要修改，所以需要纹理缓存）
  let texture = TextureCache["images/tileset.png"]; // =PIXI.utils.TextureCache
  // 创建一个矩形对象，参数 x,y,width,height
  let rectangle = new Rectangle(96, 64, 32, 32); // =PIXI.Rectangle
  // 告诉纹理使用矩形对象的信息
  texture.frame = rectangle;
  //使用纹理缓存创建一个精灵对象
  let rocket = new Sprite(texture); // =PIXI.Sprite
  //改变精灵的位置
  rocket.x = 32;
  rocket.y = 32;
  //加入舞台
  app.stage.addChild(rocket);
  //重新渲染
  app.renderer.render(app.stage);
}
```

## 七、使用纹理贴图集制作精灵

```js
loader.add("images/treasureHunter.json").load(setup); // 加载纹理信息

// 方法一： 纹理缓存TextureCache
let texture = TextureCache["frameId.png"], //  =PIXI.utils.TextureCache
    sprite = new Sprite(texture); // =PIXI.Sprite
// 方法二：已经使用Pixi的loader加载了纹理贴图集，那你可以用loader的resource来创建
let sprite = new Sprite( // =PIXI.Sprite
  resources["images/treasureHunter.json"].textures["frameId.png"] // =PIXI.loader.resources
);
// 方法三：为了创建一个精灵，输入了太多的东西！所以我建议你创建一个别名id，指向贴图集的textures对象
let id = PIXI.loader.resources["images/treasureHunter.json"].textures;  // 去除贴图集的纹理对象
let sprite = new Sprite(id["frameId.png"]); // 使用具体纹理去创建精灵 =PIXI.Sprite
```

使精灵垂直居中

```js
// app.stage.height 获得场景的高     // app.stage.width 获得场景的宽
explorer.y = app.stage.height / 2 - explorer.height / 2; // 场景的高一半 - 自身的高一半 类似css垂直居中
```

完整代码

```html
<!doctype html>
<meta charset="utf-8">
<title>Make a sprite from a texture atlas</title>
<body>
<script src="../pixi/pixi.min.js"></script>
<script>

//设置别名
let Application = PIXI.Application,
    Container = PIXI.Container,
    loader = PIXI.loader,
    resources = PIXI.loader.resources,
    TextureCache = PIXI.utils.TextureCache,
    Sprite = PIXI.Sprite,
    Rectangle = PIXI.Rectangle;

//创建舞台
let app = new Application({ 
    width: 512, 
    height: 512,                       
    antialias: true, 
    transparent: false, 
    resolution: 1
  }
);

//将舞台添加到body上
document.body.appendChild(app.view);

//使用纹理贴图集创建精灵
loader
  .add("images/treasureHunter.json")
  .load(setup);

//定义变量 
let dungeon, explorer, treasure, door, id;

//纹理加载完成后 调用这个函数
function setup() {

  //这是三种方法从纹理中添加精灵

  //1. 使用纹理缓存
  let dungeonTexture = TextureCache["dungeon.png"];
  dungeon = new Sprite(dungeonTexture);
  app.stage.addChild(dungeon);

  //2. 使用resources，从json中获取到具体的纹理创建简历
  explorer = new Sprite(
    resources["images/treasureHunter.json"].textures["explorer.png"]
  );
  explorer.x = 68;

  //并且垂直居中
  explorer.y = app.stage.height / 2 - explorer.height / 2;
  app.stage.addChild(explorer);

  //3. 起一个别名id，指向纹理对象
  id = PIXI.loader.resources["images/treasureHunter.json"].textures; 
  
  //并通过id，取到具体的纹理去创建宝藏精灵
  treasure = new Sprite(id["treasure.png"]);
  app.stage.addChild(treasure);

  //把宝藏放到舞台最右侧，并且居中
  treasure.x = app.stage.width - treasure.width - 48;
  treasure.y = app.stage.height / 2 - treasure.height / 2;
  app.stage.addChild(treasure);

  //通过id，取到具体的纹理去创建门精灵
  door = new Sprite(id["door.png"]); 
  door.position.set(32, 0);
  app.stage.addChild(door);

    
  let numberOfBlobs = 6,
      spacing = 48,
      xOffset = 150;

  for (let i = 0; i < numberOfBlobs; i++) {
	// 使用纹理生成小怪精灵
    let blob = new Sprite(id["blob.png"]);
    let x = spacing * i + xOffset;
    let y = randomInt(0, app.stage.height - blob.height);
    blob.x = x;
    blob.y = y;
    app.stage.addChild(blob);
  }
}

//一个产生随机数的方法
function randomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

</script>
</body>
```



## 八、移动精灵

### 	游戏循环（game loop）

使用Pixi的`ticker`创建一个循环函数，这被称为**游戏循环(game loop)**。

```js
// 方法一
function setup() {
  // 通过ticker函数，新增了一个gameLoop函数，每秒会执行60次。
  app.ticker.add(delta => gameLoop(delta));
}
function gameLoop(delta){
  // delta值：表示帧之间的延迟分量。您可以选择将它添加到猫的位置，使猫的动画独立于帧速率
  cat.x += 1;
}
```

```js
// 方法二
function gameLoop() {
// requestAnimationFrame 函数，在下次页面刷新时，自动调用 1秒60次
  requestAnimationFrame(gameLoop);
  cat.x += 1;
}
gameLoop();
```

### 	使用速度属性

```js
  Sprite.vx = 0; // vx用于设置精灵在x轴上（水平）的速度和方向。
  Sprite.vy = 0; // vy用于在y轴上（垂直）设置精灵的速度和方向。 
```

```js
// 完整代码
function setup() {
  //创建cat精灵
  cat = new Sprite(resources["images/cat.png"].texture);
  cat.y = 96; 
  cat.vx = 0;
  cat.vy = 0;
  app.stage.addChild(cat);
  //开始游戏循环
  app.ticker.add(delta => gameLoop(delta));
}

function gameLoop(delta){
  //更新cat精灵的速度值
  cat.vx = 1;
  cat.vy = 1;

  // 将速度赋值给cat精灵，让其每秒移动
  cat.x += cat.vx;
  cat.y += cat.vy;
}
```

## 九、游戏状态

为了代码模块化，建议封装一下游戏函数

```js
// 定义多个函数中的变量
let cat, state;
function setup() {
  // 创建cat精灵
  cat = new Sprite(resources["images/cat.png"].texture);
  cat.y = 96; 
  cat.vx = 0;
  cat.vy = 0;
  app.stage.addChild(cat);
  // 设置游戏状态，现在只有一个，但是如果多个游戏状态，可以给他赋值给state，方便控制。
  state = play;
  // 开始游戏循环
  app.ticker.add(delta => gameLoop(delta));
}
function gameLoop(delta){
  // 更新当前游戏状态
  state(delta);
}
function play(delta) {
  // 移动cat精灵
  cat.vx = 1
  cat.x += cat.vx;
}
```

## 十、键盘移动

### keyboard 自定义函数

```js
function keyboard(value) {
  let key = {};
  key.value = value;
  key.isDown = false;
  key.isUp = true;
  key.press = undefined;
  key.release = undefined;
  // 按下处理
  key.downHandler = event => {
    if (event.key === key.value) {
      if (key.isUp && key.press) key.press();
      key.isDown = true;
      key.isUp = false;
      event.preventDefault();
    }
  };

  // 弹起处理
  key.upHandler = event => {
    if (event.key === key.value) {
      if (key.isDown && key.release) key.release();
      key.isDown = false;
      key.isUp = true;
      event.preventDefault();
    }
  };

  // 添加事件监听
  const downListener = key.downHandler.bind(key);
  const upListener = key.upHandler.bind(key);
  
  window.addEventListener(
    "keydown", downListener, false
  );
  window.addEventListener(
    "keyup", upListener, false
  );
  
  // 删除原生事件监听
  key.unsubscribe = () => {
    window.removeEventListener("keydown", downListener);
    window.removeEventListener("keyup", upListener);
  };
  
  return key;
}
```

### keyboard 自定义函数的使用

```js
let keyObject = keyboard(keyValue);
keyObject.press = () => {
  //当按键按下
};
keyObject.release = () => {
  //当按键弹起
};
keyObject.unsubscribe(); // 删除事件监听器
```

### 完整代码如下

```js
function setup() {

  // 创建 cat 精灵 
  cat = new Sprite(resources["images/cat.png"].texture);
  cat.y = 96; 
  cat.vx = 0;
  cat.vy = 0;
  app.stage.addChild(cat);

  //捕获键盘方向键
  let left = keyboard("ArrowLeft"),
      up = keyboard("ArrowUp"),
      right = keyboard("ArrowRight"),
      down = keyboard("ArrowDown");

  //left 按下左键的方法
  left.press = () => {
    //当按下左键时，改变精灵的速度
    cat.vx = -5;
    cat.vy = 0;
  };
  
  //左键弹起的方法
  left.release = () => {
    // 右键没有按下 并且 小猫在y轴上没有速度，则小猫停止
    if (!right.isDown && cat.vy === 0) {
      cat.vx = 0;
    }
  };

  //Up 上健按下的方法
  up.press = () => {
    cat.vy = -5;
    cat.vx = 0;
  };
   //上键弹起的方法
  up.release = () => {
     // 下键没有按下，并且，x轴上没有速度，小猫则停止
    if (!down.isDown && cat.vx === 0) {
      cat.vy = 0;
    }
  };

  //right 右键 按下的方法
  right.press = () => {
    cat.vx = 5;
    cat.vy = 0;
  };
  // 右键松开的方法
  right.release = () => {
      //左键没有按下且y轴没有速度，小猫则停止
    if (!left.isDown && cat.vy === 0) {
      cat.vx = 0;
    }
  };

  //Down 下健按下的方法
  down.press = () => {
    cat.vy = 5;
    cat.vx = 0;
  };
   // 下键松开的方法
  down.release = () => {
    if (!up.isDown && cat.vx === 0) {
      cat.vy = 0;
    }
  };
  //设置游戏状态
  state = play;
  //启动游戏循环
  app.ticker.add(delta => gameLoop(delta));
}

function gameLoop(delta){
  //开启游戏状态
  state(delta);
}

function play(delta) {
  //用小猫的速度去移动小猫
  cat.x += cat.vx;
  cat.y += cat.vy
}
```

## 十一、精灵分组

​	Pixi有一个名为`Container`的对象，它可以把精灵们组合在一起。

### 分组

```js
// 创建三个精灵 并设置他们的位置
// 这里纹理应该是使用了纹理贴图的形式
let id = PIXI.loader.resources["images/xxxx.json"].textures;
//The cat
let cat = new Sprite(id["cat.png"]);
cat.position.set(16, 16);
//The hedgehog
let hedgehog = new Sprite(id["hedgehog.png"]);
hedgehog.position.set(32, 32);
//The tiger
let tiger = new Sprite(id["tiger.png"]);
tiger.position.set(64, 64);

// 创建一个animals容器（container）  容器(Container)视为一种不包含纹理的特殊精灵
let animals = new PIXI.Container();
// addChild 把它们三个添加到分组(group)
animals.addChild(cat);
animals.addChild(hedgehog);
animals.addChild(tiger);
// 会影响容器下的所有子精灵，等同于整个容器去修改
animals.position.set(64, 64);

注：可能多的容器 嵌套到其他容器中，但是一个可显示对象DisplayObject(例如一个精灵或另一个容器)只能显示一个父级。
```

### 局部坐标和全局坐标

局部坐标

```js
cat.x; // 获取到cat精灵相对于父容器的坐标
```

全局坐标

```js
// 全局坐标是从舞台左上角到精灵的定位点(通常是精灵的左上角)的距离。
// 如果您直接向舞台添加一个精灵，那么舞台将是精灵的父容器
parentSprite.toGlobal(childSprite.position)// parentSprite 父容器 childSprite 子容器（需要父容器）
cat.parent;// 获得精灵的父容器
cat.parent.toGlobal(cat.position);//先获取到精灵的父容器，再获取到全局坐标
```

不关心父容器下获取 全局坐标

```js
tiger.getGlobalPosition().x // 可直接获取到tiger精灵相对于舞台stage的x坐标
tiger.getGlobalPosition().y // 可直接获取到tiger精灵相对于舞台stage的y坐标
```

全局坐标转换为本地坐标

```js
sprite.toLocal(sprite.position, anyOtherSprite)
// 老虎和刺猬的相对位置。
tiger.toLocal(tiger.position, hedgehog).x
tiger.toLocal(tiger.position, hedgehog).y
```

### 使用粒子容器ParticleContainer对精灵进行分组