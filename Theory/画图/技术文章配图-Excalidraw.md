# 技术文章配图——Excalidraw

标签: Design
URL: https://zhuanlan.zhihu.com/p/425837520
状态: 待完成
类型: 文字

![](https://pic1.zhimg.com/v2-9a12725336525e6afa849455e072d05f_r.jpg?source=172ae18b)

## 引言

在技术文章的创作或文档撰写中，我们除了需要展示文字和代码之外，经常还需要配以图表或者图形来更加丰富的呈现。

优秀的作图工具有许多，例如文本绘图工具 PlantUML，流程图设计工具 [Draw.io](https://link.zhihu.com/?target=http3A//draw.io/，还有专业绘图工具 Sketch 和 Figma 等，但是我最推荐的还是手绘风格的绘图工具——Excalidraw。

在本文我将介绍 Excalidraw 是什么以及它的一些基本使用。

![](https://pic3.zhimg.com/v2-264e6830ee69ea01868d32a943d4cc86_b.jpg)

Excalidraw 是一款开源的虚拟白板绘图工具，可以让您绘制看起来像是手工完成的图表。它的开发始于 2020 年 1 月 1 日，仅在一年后就成为了一款成熟的白板产品，到目前为止，它在 [Github](https://github.com/excalidraw/excalidraw) 上已经拥有 24k 颗星。

## 基本使用

以下是这款虚拟白板一些不错的功能。

### 手绘草图

Excalidraw 的用户界面十分简洁，我们可以快速的在画布中添加以下手绘元素：

- 矩形 (R)
- 菱形 (D)
- 椭圆 (E)
- 箭头 (A)
- 线 (L)
- 文字 (T)

![](https://pic3.zhimg.com/v2-58dadb923dab537cffac7d856168c71e_b.jpg)

对于每个形状，您可以调整以下几个属性：

- 颜色
- 填充
- 描边宽度
- 描边样式
- 边框
- 边角
- 不透明度

下面的 GIF 显示了形状如何随着每个属性的变化而改变：

![](https://pic3.zhimg.com/v2-53b6e96877923a4aeee1fbd0d3c8dc82_b.jpg)

### 箭头随图形移动

当箭头指向某个形状的时候，箭头会随着图形移动而调整自身始终跟随。此功能十分高效，可以节省不少手动调整形状和箭头的时间。

![](https://pic4.zhimg.com/v2-fd7671a33727de3215034719a06be383_b.jpg)

### 绘制折线或曲线

我们可以通过拖动来创建一条直线。但是如果要创建曲线或折线，那么可以选择一条直线或一个箭头，然后通过点击创建多个拐点，最终可以双击再次调整该折线。

![](https://pic4.zhimg.com/v2-c71ca22f5500e165fe99f4efff4e1d8b_b.jpg)

### 通过 Excel 创建图表

Excalidraw 还有一个隐藏小技巧，允许您通过从 Excel 或纯逗号分隔文本（csv格式）复制和粘贴数据来创建图表！例如，如果复制下表并将其粘贴到 Excalidraw：

```
Day,Commits
Mon,167
Tue,92
Wed,114
Thu,195
Fri,155
Sat,97
Sun,42
```

将自动获得以下两个图表：

![](https://pic1.zhimg.com/v2-87d17e9115b3fab8de0fa4c5ef7e0948_b.jpg)

### 多人实时协作

Excalidraw 开始多人协作也十分容易，在左上角有一个协作图标，点击即可获得指向新开启的会话的共享链接，通过打开分享链接就可以和队友进行实时协作了。

![](https://pic2.zhimg.com/v2-747239eb049e4adee837f0dcd62f21c9_b.jpg)

### 组件库

此外，如果想创建更加复杂的图形，Excalidraw 提供了丰富的组件库用于快速搭建我们想要的效果。要从库中选择新形状，请单击顶部工具栏上的方形图标，然后选择要添加的组件。单击浏览库从 Excalidraw 库下载更多形状。

![](https://pic2.zhimg.com/v2-3380f394bad6715918a9b2203a56aba9_b.jpg)

当然，我们也可以创建属于我们自己的本地组件库，保存下来以备将来使用。

![](https://pic1.zhimg.com/v2-f4c463cd8c6ccde4258eb277de2c1244_b.jpg)

### 中文手写体

Excalidraw 的官方在线版只提供英文字体的手写版，默认情况下并不支持中文手写体。如果我们有中文手写体的需求的话，则需要对官方仓库进行修改部署。

## 示例

关于 Excalidraw 都能够应对哪些主流场景，我在官网和 Twiiter 上找了一些例子以供参考。

**架构图**

![](https://pic4.zhimg.com/v2-3296f65e59749cdc1dc7e7bb506da673_b.jpg)

**流程图**

![](https://pic2.zhimg.com/v2-62ff0bdbc9e9feb9bee47b54218f3c51_b.jpg)

**笔记讲解**

![](https://pic2.zhimg.com/v2-ebf6191af64c9427fdad73f9a6b529f5_b.jpg)

**维恩图**

![](https://pic1.zhimg.com/v2-239d58ae039612d51b55b3abc2ccbe30_b.jpg)

## 总结

就像前面介绍的，Excalidraw 设计简洁，易于上手，能够绘制各种漂亮的手绘风格图形，特别是对于配图有颜值需求的写作者，我强烈推荐您使用体验一下。

当然它也不是适用于所有的绘图场景，例如一些正式、复杂精确或者特定领域的制图任务。找到合适的工具才是最重要的。

## 参考文献

- [Rethinking Virtual Whiteboard](https://link.zhihu.com/?target=https3A//blog.excalidraw.com/rethinking-virtual-whiteboard/
- [One Year of Excalidraw](https://link.zhihu.com/?target=https3A//blog.excalidraw.com/one-year-of-excalidraw/
- [Create Diagrams That Look Like Hand-drawn](https://link.zhihu.com/?target=https3A//pakstech.com/blog/draw-diagrams/