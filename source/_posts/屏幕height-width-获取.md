title: 屏幕height width 获取
author: John Doe
date: 2018-11-12 01:48:25
tags:
---
``` bash
网页可见区域宽： document.body.clientWidth
网页可见区域高： document.body.clientHeight
网页可见区域宽： document.body.offsetWidth (包括边线的宽)
网页可见区域高： document.body.offsetHeight (包括边线的高)
网页正文全文宽： document.body.scrollWidth
网页正文全文高： document.body.scrollHeight
网页被卷去的高： document.body.scrollTop
网页被卷去的左： document.body.scrollLeft
网页正文部分上： window.screenTop
网页正文部分左： window.screenLeft
屏幕分辨率的高： window.screen.height
屏幕分辨率的宽： window.screen.width
屏幕可用工作区高度： window.screen.availHeight
屏幕可用工作区宽度： window.screen.availWidth


JQuery:
$(document).ready(function(){
alert($(window).height()); //浏览器当前窗口可视区域高度
alert($(document).height()); //浏览器当前窗口文档的高度
alert($(document.body).height());//浏览器当前窗口文档body的高度
alert($(document.body).outerHeight(true));//浏览器当前窗口文档body的总高度 包括border padding margin

alert($(window).width()); //浏览器当前窗口可视区域宽度
alert($(document).width());//浏览器当前窗口文档对象宽度
alert($(document.body).width());//浏览器当前窗口文档body的宽度
alert($(document.body).outerWidth(true));//浏览器当前窗口文档body的总宽度 包括border padding margin

})



 网页被卷起来的高度/宽度（即浏览器滚动条滚动后隐藏的页面内容高度）

(javascript)        document.documentElement.scrollTop //firefox，chrome

(javascript)        document.documentElement.scrollLeft //firefox，chrome 

(javascript)        document.body.scrollTop //IE

(javascript)        document.body.scrollLeft //IE

(jqurey)             $(window).scrollTop() 

(jqurey)             $(window).scrollLeft()

 网页工作区域的高度和宽度  

(javascript)       document.documentElement.clientHeight// IE firefox       

(jqurey)             $(window).height()

 元素距离文档顶端和左边的偏移值  

(javascript)        document.documentElement.offsetTop //IE firefox

(javascript)        document.documentElement.offsetLeft //IE firefox

(jqurey)             jq对象.offset().top

(jqurey)             jq对象.offset().left

获取页面元素距离浏览器工作区顶端的距离
 页面元素距离浏览器工作区顶端的距离  =  元素距离文档顶端偏移值  -   网页被卷起来的高度  

即：

 页面元素距离浏览器工作区顶端的距离 =  document.documentElement.offsetTop  -  document.documentElement.scrollTop 
```