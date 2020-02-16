title: css GPU硬件加速原理
author: John Doe
date: 2018-11-16 02:08:40
tags:
---
满世界的动画性能优化技巧，例如：

只允许改变transform、opacity，其它属性不要动，避免重新计算布局（reflow）

对动画元素应用transform: translate3d(0, 0, 0)、will-change: transform等，开启硬件加速

动画元素尽量用fixed、absolute定位方式，避免reflow

对动画元素应用高一点的z-index，减少复合层数量

。。。其它可能有用的规则

那么问题是：已经小心遵守这些规则了，为什么动画还会卡顿、跳帧？还能优化吗？要从哪里入手？

# 一.硬件加速是非规范的
The most important thing I’d like to tell you before we dive deep into GPU compositing is this: It’s a giant hack. You won’t find anything (at least for now) in the W3C‘s specifications about how compositing works, about how to explicitly put an element on a compositing layer or even about compositing itself. It’s just an optimization that the browser applies to perform certain tasks and that each browser vendor implements in its own way.

很多情况下，开启硬件加速确实能带来明显的性能提升，但是，这部分内容是非规范的，W3C并没有相关规范说明其中细节，所以通过一些技巧（例如transform: translate3d(0, 0, 0)）开启硬件加速是规范之外的行为，可能得到性能提升，也可能带来严重的性能问题

也许在将来会形成规范，依照规范去做肯定能获得性能提升，但在这之前，除了必须遵从各项性能优化原则外，还要考虑实际渲染流程，从原理上解决性能问题

硬件加速（Hardware Acceleration）
硬件加速在CSS动画上是指GPU合成（GPU compositing），浏览器不直接通过CPU生成图像数据显示出来，而是把相关层数据发送给GPU，而GPU在图像数据运算方面有天生优势，所以算是加速

那么当硬件加速不可用时，浏览器怎样渲染页面？

在没有硬件加速的情况下，浏览器通常是依赖于CPU来渲染生成网页的内容，大致的做法是遍历这些层，然后按照顺序把这些层的内容依次绘制在一个内部存储空间上（例如bitmap），最后把这个内部表示显示出来，这种做法就是软件渲染（software rendering）

二.transform和opacity的特殊性
以前通过改变布局相关属性形成动画，例如：

@keyframes move {
   from { left: 30px; }
   to { left: 100px; }
}
对于动画的每一帧，浏览器都要重新计算元素的形状位置（reflow），把新状态渲染出来（repaint），再显示到屏幕上

整页reflow和repaint想想就觉得很慢，那么如果把动画元素抽出来作为前景，每帧其它部分作为背景不变，只重新渲染动画元素，再把前景背景合成起来，是不是会更快？当然会，因为GPU能快速地进行亚像素级图层合成

但是这样做的前提是能够按照动的，不动的划分出前景背景层，如果动画元素或者受布局影响，或者动的过程中影响到了布局，就会打破前景背景的界限，这样简单分为2层就有问题。那么，应用position: fixed | absolute是不是就能保证不会影响布局了？

不行，因为left可以接受百分比值、相对单位（em、vw等等），浏览器不能百分百肯定该属性的变化与布局无关，所以不能简单的分出前景背景层，例如：

@keyframes move {
   from { left: 30px; }
   to { left: 100%; }
}
但浏览器能百分百肯定transform和opacity的变化与布局无关，不受布局影响，其变化也不会影响现有布局，所以这两个属性的特殊性是：

does not affect the document’s flow,

does not depend on the document’s flow,

does not cause a repaint.

如果不影响布局，且不受布局影响，其变化不会导致其它部分需要repaint，那么这个东西肯定可以抽出去单独作为一层，放心交给GPU去处理，享受硬件加速带来的好处；

细腻（GPU能做到亚像素级精度，且对GPU来说不费劲）

流畅（不受其它运算密集的JS任务影响，动画交给GPU了，与CPU无关）

三.GPU合成的代价
It might surprise you, but the GPU is a separate computer. That’s right: An essential part of every modern device is actually a standalone unit with its own processors and its own memory- and data-processing models. And the browser, like any other app or game, has to talk with the GPU as it would with an external device.

GPU是独立的一部分，有自己的处理器、内存核数据处理模型，那么意味着通过CPU在内存里创建的图像数据无法直接与GPU共享，需要打包发送给GPU，GPU收到后才能执行我们期望的一系列操作，这个过程需要时间，而打包数据需要内存

需要的内存取决于：

复合层的数量

复合层的大小

相对于数量，复合层的大小影响更大一些，例如：

.rect {
    width: 320px;
    height: 240px;
    background: #f00;
}
这个红块如果要发送给GPU的话，需要的存储空间是：320 × 240 × 3 = 230400B = 225KB（rgb需要3个字节），如果图像含有透明部分，就需要320 × 240 × 4 = 307200B = 300KB

这样一个不起眼的小红块就需要2、300KB，页面动辄几十上百个元素，占全屏半屏的元素也不少，如果都作为复合层，交给GPU，内存消耗可想而知，所以一些很极端的硬件加速场景性能非常差：

技术分享

gpu compositing issue

对于1GB RAM的设备，去掉系统和后台进程的1/3，再去掉浏览器和当前页面的1/3，实际能用的只有200到300MB，如果复合层太多太大，内存会被迅速消耗，然后掉帧（卡顿、闪烁）现象，甚至浏览器/应用崩溃也就很合理了

P.S.详细见CSS3硬件加速也有坑！！！

四.创建复合层
浏览器在一些情况下会创建复合层，例如：

3D transforms: translate3d, translateZ and so on;

<video>, <canvas> and <iframe> elements;

animation of transform and opacity via Element.animate();

animation of transform and opacity via СSS transitions and animations;

position: fixed;

will-change;

filter;

。。。

还有很多，详细见CompositingReasons.h中定义的常量，分为几类：

这些大多是我们期望的，算是显式创建的复合层，而另一些情况也会创建复合层：

位于复合层之上的元素会被创建复合层（B的z-index大于A，对A做动画，B也会被**独立的复合层）

很容易理解，A在动画过程中可能会与B产生重叠，被B遮住，那么GPU需要每帧对A图层做动画，然后再与B图层合成，才能得到正确结果，所以B无论如何都要被**复合层，连同A一起交给GPU

隐式创建复合层主要出于重叠考虑，如果浏览器不确定会不会发生重叠，那么就要把不确定的东西都**复合层，所以，从这个角度看，高z-index原则是有道理的

五.硬件加速的优缺点
优点
动画非常流畅，能达到60fps

动画执行过程在独立线程里，不受计算密集的JS任务影响

缺点
把元素**复合层时需要额外重绘，有时很慢（可能需要整页重绘）

复合层数据传递给GPU有额外时耗，取决于复合层的数量和大小，这在中低端设备可能会导致闪烁

每个复合层都要消耗一部分内存，移动设备上内存很贵，过多占用会导致浏览器/应用崩溃

存在隐式复合层的问题，不注意的话内存飙升

文字模糊，元素有时会变形

最主要的问题集中在内存消耗和repaint上，所以动画性能优化目标是降低内存消耗，减少repaint

六.性能优化技巧
1.尽量避免隐式复合层
复合层直接影响repaint、内存消耗：动画开始时创建复合层、结束时删除复合层，都会引起repaint，而动画开始时必须把图层数据发送给GPU，内存消耗集中在这里。两条建议：

给动画元素应用高z-index，最好直接作为body的子元素，对于嵌套很深的动画元素，可以复制一个到body下，仅用于实现动画效果

给动画元素应用will-change，浏览器会提前把这些元素**复合层，可以让动画开始/结束时更流畅些，但不能滥用，在不需要的时候赶紧去掉，减少内存消耗

2.只改变transform和opacity
能用transform、opacity优先用，不能用的话想办法用，比如背景色渐变，可以用盖在上面的伪元素背景色opacity动画模拟；box-shadow动画可以用铺在下面的伪元素opacity动画模拟，这些曲折的实现方式能带来显著性能提升

3.减少复合层的大小
小元素放大展示，减小width、height，减少传递给GPU的数据，由GPU做scale放大展示，视觉效果无差异（多用于纯色背景元素，对不太重要的图片也可以进行5%到10%的宽高压缩），例如：

<div id="a"></div>
<div id="b"></div>

<style>
#a, #b {
    will-change: transform;
    background-color: #f00;
}

#a {
    width: 100px;
    height: 100px;
}

#b {
    width: 10px;
    height: 10px;
    transform: scale(10);
}
</style>
最终显示的两个红色块在视觉上没有差异，但减小了90%的内存消耗

4.考虑对子元素动画与容器动画
容器动画可能存在不必要的内存消耗，比如子元素之间的空隙，也会被当做有效数据发送给GPU，如果对各个子元素分别应用动画，就能避免这部分的内存消耗

例如12道太阳光线旋转，转容器就把容器整张图都发送给GPU，单独转12道光线就去掉了光线之间的11条空隙，能够节省一半内存

5.早早关注复合层的数量和大小
从一开始就关注复合层，尤其是隐式创建的复合层，避免后期优化影响布局

复合层的大小比数量影响更大，但浏览器会做一些优化操作，把几个复合层整合成一个，叫Layer Squashing，但有时一个大复合层比几个小复合层消耗的内存更多，有必要的话可以手动去掉这种优化：

// 给每个元素应用不同的translateZ
translateZ(0.0001px), translateZ(0.0002px)
6.不要滥用硬件加速
没事不要乱加transform: translateZ(0)、will-change: transform等强制开启硬件加速的属性，GPU合成存在缺点和不足，而且是非标准的行为，最好情况能带来显著性能提升，最坏情况可能会让浏览器崩溃