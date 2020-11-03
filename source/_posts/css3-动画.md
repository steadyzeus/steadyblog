title: 'css3 相关(动画,语法)'
author: John Doe
tags: []
categories:
  - css3
date: 2018-11-23 03:28:00
---
# 1.使用 mix-blend-mode: screen 滤色模式制作 loading 效果
<iframe title="mix-blend-mode: screen" src="https://codepen.io/Chokcoco/embed/zwPyWj/?height=265&amp;theme-id=0&amp;default-tab=css,result&amp;embed-version=2" frameborder="no" scrolling="no" width="750" height="365"></iframe>

更多：https://www.cnblogs.com/coco1s/p/6829372.html

# 2.选择器
选择第一个类名： .exerciseInfo: nth-of-type(1) { ... };

选择最后一个类名： .exerciseInfo: last-child { ... };

<iframe height="265" style="width: 100%;" scrolling="no" title="【iCSS：布局】使用:not()去除无用属性" src="https://codepen.io/steadyzeus/embed/jOOPgYJ?height=265&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/steadyzeus/pen/jOOPgYJ'>【iCSS：布局】使用:not()去除无用属性</a> by Steady
  (<a href='https://codepen.io/steadyzeus'>@steadyzeus</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

# 3.使用object-fit规定图像尺寸

要点：通过object-fit使图像脱离background-size的约束，使用<img>来标记图像背景尺寸

<iframe height="265" style="width: 100%;" scrolling="no" title="【iCSS：布局】使用object-fit规定图像尺寸" src="https://codepen.io/steadyzeus/embed/mddedOJ?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/steadyzeus/pen/mddedOJ'>【iCSS：布局】使用object-fit规定图像尺寸</a> by Steady
  (<a href='https://codepen.io/steadyzeus'>@steadyzeus</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


# 4.iCSS：布局】使用transform描绘1px边框

//从左上角开始缩放，不然是从右下角开始
transform: scale(.5);
transform-origin: left top;

<iframe height="265" style="width: 100%;" scrolling="no" title="【iCSS：布局】使用transform描绘1px边框" src="https://codepen.io/JowayYoung/embed/YzKqMVO?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/JowayYoung/pen/YzKqMVO'>【iCSS：布局】使用transform描绘1px边框</a> by JowayYoung
  (<a href='https://codepen.io/JowayYoung'>@JowayYoung</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


# 5.css中“~”（波浪号）、“，”（逗号）、 “ + ”（加号）和 “ > ”（大于号）是什么意思？




为所有相同的父元素中位于 p 元素之后的所有 ul 元素设置背景：
```
    p~ul{
　　     background:#ff0000;
        }
 
      <p>快乐生活</p>
      <ul>
　　    <li>生活</li>
　　    <li>生活</li>
　　    <li>生活</li>
      </ul>

 
p~ul 选择前面有 <p> 元素的每个 <ul> 元素。“~”的定义和用法,p~ul选择器 p之后出现的所有ul。

　　两种元素必须拥有相同的父元素，但是 ul不必直接紧随 p。

       css中“>”是css3特有的选择器，A>B 表示选择A元素的所有子B元素。

　　与A B的区别在于，A B选择所有后代元素，而A>B只选择一代。

       .a，.b｛逗号指相同的css样式｝；

       .a .b｛空格指后代元素｝；

       .a>.b｛大于号指子代元素｝；

       .a+.b｛这个“+”是选择相邻兄弟，叫做“相邻兄弟选择器”
       
       ```
　　如果需要选择紧接在另一个元素后的元素，而且二者有相同的父元素，可以使用相邻兄弟选择器(Adjacent sibling selector)。
　　例如，如果要增加紧接在 h1 元素后出现的段落的上边距，可以这样写：
　　h1 + p {margin-top:50px;}
　　这个选择器读作：“选择紧接在 h1 元素后出现的段落，h1 和 p 元素拥有共同的父元素”。｝；
  ```
  
# 6.ease,seae-in,ease-in-out,ease-out
css 动画中 ease,seae-in,ease-in-out,ease-out,效果区别
linear	规定以相同速度开始至结束的过渡效果（等于 cubic-bezier(0,0,1,1)）。(匀速)
ease	规定慢速开始，然后变快，然后慢速结束的过渡效果（cubic-bezier(0.25,0.1,0.25,1)）（相对于匀速，中间快，两头慢）。
ease-in	规定以慢速开始的过渡效果（等于 cubic-bezier(0.42,0,1,1)）（相对于匀速，开始的时候慢，之后快）。
ease-out	规定以慢速结束的过渡效果（等于 cubic-bezier(0,0,0.58,1)）（相对于匀速，开始时快，结束时候间慢，）。
ease-in-out	规定以慢速开始和结束的过渡效果（等于 cubic-bezier(0.42,0,0.58,1)）（相对于匀速，（开始和结束都慢）两头慢）。
cubic-bezier(n,n,n,n)	在 cubic-bezier 函数中定义自己的值。可能的值是 0 至 1 之间的数值。
  
  
# 7.animation-direction：定义是否循环交替反向播放动画。

normal	默认值。动画按正常播放。
reverse	动画反向播放。
alternate	动画在奇数次（1、3、5…）正向播放，在偶数次（2、4、6…）反向播放。
alternate-reverse	动画在奇数次（1、3、5…）反向播放，在偶数次（2、4、6…）正向播放。
initial	设置该属性为它的默认值。
inherit	从父元素继承该属性。

animation 属性一共有 6个基本属性：

animation-name    ：keyframe name

animation-duration

animation-timing-function

animation-delay

animation-iteration-count

animation-direction

animation-play-state: running | paused 检索或设置对象动画的状态，running为默认值


# 8.animation-fill-mode 使用


首先，你要明白动画分为 初始状态 等待期 动画执行期 完成期 四个阶段。

初始状态，就是没有触发动画效果时，你元素原本应该有的状态。
例如你的动画是通过点击触发的，那么你元素在还没有点击的时候，是受初始状态样式控制的，也就css中不含 animation 的其他属性控制。

等待期，就是 animation-delay 设置的延迟期间。

按照点击触发为例子，等待期就是从你点击元素开始计算，持续 animation-delay 计时结束的这段时间。

这个期间的样式会受到 animation-fill-mode 取值的影响。

如果为 none，表示等待期间元素没有变化，还是初始状态的样式。
如果为 backwards 或者 both，表示等待期元素样式为第一帧的样式。

注意是第一帧的概念，可能时 @keyframes 中的 0%，也有可能为 100% 。取决于 animationo-direction 属性。

1、当 animationo-direction 为 normal 或者 alternate 时，第一帧就是 0% 中定义样式。
2、当 animation-direction 为 reverse 或者 alternate-reverse时，第一帧就时 100% 中定义样式。

如果为 forwards，对于等待期而言没有意义，这个是定义完成状态时元素样式，下面会细说。

动画执行期，指的是 delay 结束瞬间开始执行动画，一直持续到最后一帧。
注意最后一帧的概念也是很绕，它一定属于 @keyframes 中的 0% 或者 100% 中之一。但具体为哪一个受到 animation-direction 和 animation-iteration-count 取值影响。

总结：
1、当 animation-direction：normal 时，最后一帧总为 100% 样式，无关 animation-iteration-count。
2、当 animation-direction：reverse 时，最后一帧总为 0% 样式，也无关 animation-iteration-count。
3、当 animation-direction：alternate时，animation-iteration-count 为单数时，最后一帧 为 100%， 双数为 0%；
4、当 animation-direction：alternate-reverse时，animation-iteration-count 为单数时，最后一帧 为 0%， 双数为 100%；

完成状态，执行完最后一帧时，元素处于的状态
如果你理解最后一帧，就能理解完成状态。ifinite 的动画没有完成状态。
animation-fill-mode 取值 none。表示动画结束，元素回归初始状态，而且是瞬间回归，无动画效果。

animation-fill-mode 取值 forwards 或者 both。表示动画执行完最后一帧，保持在最后一帧样式。再次申明，最后一帧可能为 @keyframes 中的 0% 或者 100% 之一。

最后总结
none 表示 等待期和完成期，元素样式都为初始状态样式，不受动画定义（@keyframes）的影响。

both 表示 等待期样式为第一帧样式，完成期保持最后一帧样式。

backwards 表示等待期为第一帧样式，完成期跳转为初始样式

forwards 表示等待期保持初始样式，完成期间保持最后一帧样式。


# 9.CSS3 filter

grayscale灰度
sepia褐色（求专业指点翻译）
saturate饱和度
hue-rotate色相旋转
invert反色
opacity透明度
brightness亮度
contrast对比度
blur模糊
drop-shadow阴影


注意: 滤镜通常使用百分比 (如：75%), 当然也可以使用小数来表示 (如：0.75)。

Filter	描述
none	默认值，没有效果。
blur(px)	给图像设置高斯模糊。"radius"一值设定高斯函数的标准差，或者是屏幕上以多少像素融在一起， 所以值越大越模糊；

如果没有设定值，则默认是0；这个参数可设置css长度值，但不接受百分比值。
brightness(%)	给图片应用一种线性乘法，使其看起来更亮或更暗。如果值是0%，图像会全黑。值是100%，则图像无变化。其他的值对应线性乘数效果。值超过100%也是可以的，图像会比原来更亮。如果没有设定值，默认是1。

contrast(%)	调整图像的对比度。值是0%的话，图像会全黑。值是100%，图像不变。值可以超过100%，意味着会运用更低的对比。若没有设置值，默认是1。

drop-shadow(h-shadow v-shadow blur spread color)	
给图像设置一个阴影效果。阴影是合成在图像下面，可以有模糊度的，可以以特定颜色画出的遮罩图的偏移版本。 函数接受<shadow>(在CSS3背景中定义)类型的值，除了"inset"关键字是不允许的。该函数与已有的box-shadow box-shadow属性很相似；不同之处在于，通过滤镜，一些浏览器为了更好的性能会提供硬件加速。<shadow>参数如下：

<offset-x> <offset-y> (必须)
这是设置阴影偏移量的两个 <length>值. <offset-x> 设定水平方向距离. 负值会使阴影出现在元素左边. <offset-y>设定垂直距离.负值会使阴影出现在元素上方。查看<length>可能的单位.
如果两个值都是0, 则阴影出现在元素正后面 (如果设置了 <blur-radius> and/or <spread-radius>，会有模糊效果).
<blur-radius> (可选)
这是第三个code><length>值. 值越大，越模糊，则阴影会变得更大更淡.不允许负值 若未设定，默认是0 (则阴影的边界很锐利).
<spread-radius> (可选)
这是第四个 <length>值. 正值会使阴影扩张和变大，负值会是阴影缩小.若未设定，默认是0 (阴影会与元素一样大小). 
注意: Webkit, 以及一些其他浏览器 不支持第四个长度，如果加了也不会渲染。
 
<color> (可选)
查看 <color>该值可能的关键字和标记。若未设定，颜色值基于浏览器。在Gecko (Firefox), Presto (Opera)和Trident (Internet Explorer)中， 会应用colorcolor属性的值。另外, 如果颜色值省略，WebKit中阴影是透明的。

grayscale(%)	
将图像转换为灰度图像。值定义转换的比例。值为100%则完全转为灰度图像，值为0%图像无变化。值在0%到100%之间，则是效果的线性乘子。若未设置，值默认是0；

hue-rotate(deg)	
给图像应用色相旋转。"angle"一值设定图像会被调整的色环角度值。值为0deg，则图像无变化。若值未设置，默认值是0deg。该值虽然没有最大值，超过360deg的值相当于又绕一圈。

invert(%)	
反转输入图像。值定义转换的比例。100%的价值是完全反转。值为0%则图像无变化。值在0%和100%之间，则是效果的线性乘子。 若值未设置，值默认是0。

opacity(%)	
转化图像的透明程度。值定义转换的比例。值为0%则是完全透明，值为100%则图像无变化。值在0%和100%之间，则是效果的线性乘子，也相当于图像样本乘以数量。 若值未设置，值默认是1。该函数与已有的opacity属性很相似，不同之处在于通过filter，一些浏览器为了提升性能会提供硬件加速。

saturate(%)	
转换图像饱和度。值定义转换的比例。值为0%则是完全不饱和，值为100%则图像无变化。其他值，则是效果的线性乘子。超过100%的值是允许的，则有更高的饱和度。 若值未设置，值默认是1。

sepia(%)	
将图像转换为深褐色。值定义转换的比例。值为100%则完全是深褐色的，值为0%图像无变化。值在0%到100%之间，则是效果的线性乘子。若未设置，值默认是0；

url()	
URL函数接受一个XML文件，该文件设置了 一个SVG滤镜，且可以包含一个锚点来指定一个具体的滤镜元素。

例如：

filter: url(svg-url#element-id)