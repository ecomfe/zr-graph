
zr-graph 是一个web应用, 可以在线查看和简单编辑常见的图文件（目前只支持 [gexf](http://gexf.net/format/) 和 [gdf](https://gephi.org/users/supported-graph-formats/gdf-format/) )，使用力导向算法进行布局，导出成 [gexf](https://gephi.org/users/supported-graph-formats/gdf-format/) 文件。

#### [Try it](http://zr-graph.qiniudn.com)

<img src="http://pictures-shenyi.qiniudn.com/zr-graph/overview.jpg" width="700" />

### 独立使用 ForceAtlas2

zr-graph 中使用了 [ForceAtla2](http://webatlas.fr/tempshare/ForceAtlas2_Paper.pdf) 作为布局算法，你可以在 zr-graph 界面中尝试该布局算法，也可以引用 js 文件在自己的项目中独立使用。


##### 使用实例

参考 `static/tests/force_worker.html` 的代码

##### 引用代码

ForceAtlas2 的源文件是 `static/lib/` 目录下的 `ForctAtlas2.js` 和 `ForceAtlas2_worker.js`, `ForceAtlas2.js` 是你需要引用的文件，`ForceAtlas2_worker.js`是布局算法主要的代码，顾名思义这段代码是通过 WebWorker 在另一个进程中执行的，但是无需关心这个，只要跟 `ForceAtlas2.js` 放在同个目录下就行了。

##### 使用代码

+ 创建 ForceAtlas2 实例

```javascript
var forceAtlas2 = new ForceAtlas2();
```

+ 添加节点

```javascript

// 你可以使用 ForceAtlas2.Node 创建节点
var position = new Float32Array([10, 10]);
forceAtlas2.addNode(new ForceAtlas2.Node("", position));

// 也可以传入任意带有 position 属性的 object
var position = new Float32Array([10, 10]);
forceAtlas2.addNode({
    position: position,
    id: "",
    mass: 1,
    size: 1
});

```

其中 `id`, `mass`, `size` 属性都是可选的

+ 添加边

跟添加节点类似

```javascript

// 你可以使用 ForceAtlas2.Edge 创建边
forceAtlas2.addEdge(new ForceAtlas2.Edge(sNode, tNode));

// 也可以传入任意带有 source 和 target 属性的 object
forceAtlas2.addEdge({
    source: sNode,
    target: tNode
});

```

+ 初始化

初始化会创建一个 worker 并把顶点和边的初始数据传到 worker 中

```javascript

forceAtlas2.init();

```

+ 更新以及更新回调

```javascript
forceAtlas.onupdate = function(nodes, edges) {
    forceAtlas.update();
    // Clear canvas
    // Draw nodes and edges
}

forceAtlas.update();
```

每次更新会触发 worker 计算一次布局，每次计算的结果都是累积的，计算完之后会把节点新的位置同步回来并且调用 `onupdate` 函数。

> 因为渲染的时间往往会远大于布局计算的时间，所以上面代码经常是布局很快就计算完成返回了，但是程序还要等很长时间绘制完成才会调用下一次的布局计算，导致 worker 的利用率很低，这种情况下可以在 `update` 函数中传入一个参数告知这次更新迭代的次数，用来平衡布局的事件和渲染的时间，这个迭代次数可以简单的估一个值，也可以动态设置 `Math.round(renderTime / layoutTime)`



