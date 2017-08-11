# WebGL入门demo

### three.js入门

## 开场白
哇哦，绘制气球耶，在网页上？对啊！厉害了！3D效果图也能在网页上绘制出来啊，这么好玩的事情，赶紧来看看！

这里是属于WebGL的应用，webGL可以让我们在canvas上实现3D效果。而three.js是一款webGL框架，由于其易用性被广泛应用。如果要学习webGL，抛弃那些复杂的原生接口从这款框架入手是一个不错的选择。跟着我一起走！

👉：[three.js参考文档 英文](https://threejs.org/examples/) <br>
👉：[three.js参考文档 中文](http://techbrood.com/threejs/docs/) <br>

### 看地球咯！
哈哈，别说了。先看地球：
![](https://github.com/carolineLH/WebGL/blob/master/GIF1.gif)

## 怎么画？
首先要理清逻辑。three.js框架是个法宝，要画东西的工具，模块，材料等等里面都有，找到API去用。所以，我们只需要：<br>

* 一张图片，也就是包裹地球身体的那张图片，<br>
* 一个球模型，<br>
* 把图片贴到球模型上去，地球就出来了，<br>
* 再给球加上一些动画效果，完工！<br>

## 开始画！
上面讲完了画的大致流程，现在要开始画了。但是你还需要知道，不止这么简单！远不止这么简单！你需要：<br>

1.设置three.js渲染器-renderer <br>

2.设置摄像机camera <br>

3.设置场景scene br>

4.设置物体object-地球 <br>

5.设置组织者 <br>

##### 是不是一脸懵逼？别怕，来讲个故事😁<br>
其实，就是拍电影啦。需要相机，演员（这里是地球），场景(scene)，导演（group）。导演组织这些活动，导演也要看场景的，他受场景的约束，演员也要听导演的。最后拍好了戏交给渲染器(renderer)来制片，发布。<br>

好吧，这么形象估计懂了，来，我们具体来讲讲。<br>

## 一步步画：
每个元素都是再定义了之后，在初始化函数内部执行。<br>

### 做准备：

用到three.js框架，要先引入以下：

```
<script src="https://threejs.org/build/three.js"></script>
<script src="https://threejs.org/examples/js/renderers/Projector.js"></script>
<script src="https://threejs.org/examples/js/renderers/CanvasRenderer.js"></script>
<script src="https://threejs.org/examples/js/libs/stats.min.js"></script>
```

### 画地球：
看代码：<br>
```
// 加载材质
var loader = new THREE.TextureLoader();
    loader.load('https://threejs.org/examples/textures/land_ocean_ice_cloud_2048.jpg',
     function(texture) {
        //画球体 形状
        var geometry = new THREE.SphereGeometry(200, 20, 20);
        // 贴图 材质纹理
        var material = new THREE.MeshBasicMaterial({
            map: texture,
            overdraw: 0.5
        })
        // 地球
        var mesh = new THREE.Mesh(geometry, material);
        group.add(mesh);
        }
```
画地球需要地球外面那张图片，还需要球模型geometry。图片需要裁剪之后变成material。再用这两个元素来new地球mesh,把地球交给group.

### 设置场景：
```
var scene;
scene = new THREE.Scene();
scene.add(group);
```

### 设置分组（导演）：

```
var group;
group = new THREE.Group();
```

### 设置相机：

```
var camera；
// 准备好镜头
    camera = new THREE.PerspectiveCamera(60, window.innerWidth/window.innerHeight,1,2000);//相机摆上 设置相机摆放位置 产生镜头
    camera.position.z = 500;//拍的景物离我500px
```
先设置一下相机，把他放到里面去。

### 设置渲染器：
```
var renderer;
renderer = new THREE.CanvasRenderer();
        renderer.setClearColor(0xffffff);//设置canvas背景颜色
        renderer.setPixelRatio(window.devicePixelRatio);
        renderer.setSize(window.innerWidth, window.innerHeight);//container展示的大小
        container.appendChild(renderer.domElement)//追加 【canvas】 元素到 【container】 元素中
        stats = new Stats();
        container.appendChild(stats.dom);
```
先设置一下渲染器，设置在画布上面显示的属性，再把画布添加到浏览器页面上面去。还有在动画过程中的循环渲染在下面讲解。

### 加动画啦！
```
var container,stas;
var mouseX=0,mouseY=0;
var windowHalfX=window.innerWidth/2;
var windowHalfY=window.innerHeight/2;
animate();
document.addEventListener('mousemove', onDocumentMouseMove, false);//用鼠标拖
window.addEventListener('resize',onWindowResize,false);

function onDocumentMouseMove (event) {
    mouseX = event.clientX - windowHalfX;//鼠标基于中心点的偏移量；
    mouseY = event.clientY - windowHalfY;//鼠标基于中心点的偏移量；
}

function onWindowResize (event) {
    windowHalfX = window.innerWidth / 2;
    windowHalfY = window.innerHeight / 2;
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth,window.innerHeight);
}

function animate () {
// 每秒60针递归调用 使地球旋转
    requestAnimationFrame(animate);
    render();
    stats.update();
}
function render () {
    camera.position.x
     += (mouseX-camera.position.x)*0.05;//在x轴上，相机根据鼠标的位置移动来移动的距离
    camera.position.y 
    += (-mouseY - camera.position.y)*0.05;//在y轴上，相机根据鼠标的位置移动来移动的距离
    camera.lookAt(scene.position);//设置视野的中心坐标
    group.rotation.y -= 0.005;//让它饶着y轴旋转 （间接的得到旋转的速度）
    renderer.render(scene, camera);//将webgl视图往外输出
}
```
设置在鼠标动的时候监听到，而且此时camera随即改变而改变。camera要改变根据鼠标的移动来移动它的距离在函数onDocumentMouseMove中得到，而且地球要有一种远小近大的感觉。随着鼠标移动，camera变化，地球的大小也在改变，也就是前面说的远小近大的感觉。在函数onWindowResize中实现。然后地球要动画起来要调用animate函数，每秒60针递归调用 使地球旋转，然后render函数就一直在不停的循环。状态也在不停的更新。

## 小结：

WebGL是是一种3D绘图标准，这种绘图技术里面用了JavaScript，所以会JavaScript，走遍天下都不怕啊😄<br>

👉：[源码](https://github.com/carolineLH/WebGL/blob/master/WebGL/earth-T.html) <br>

思考好逻辑，就可以动手的!好玩就要去尝试，就在慢慢成长。大家共同进步吧！







