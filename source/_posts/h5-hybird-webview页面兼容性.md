title: h5 hybird webview页面兼容性
author: John Doe
date: 2019-12-20 22:53:07
tags:
---
1.在滚动容器上增加滚动 touch 方法
将-webkit-overflow-scrolling 值设置为 touch

.wrapper {
    -webkit-overflow-scrolling: touch;
}

2.iOS 上拉边界下拉出现白色空白
```javascript
document.body.addEventListener('touchmove', function(e) {
    if(e._isScroller) return;
    // 阻止默认事件
    e.preventDefault();
}, {
    passive: false
});
```
3.软键盘将页面顶起来、收起未回落问题
表现
Android 手机中，点击 input 框时，键盘弹出，将页面顶起来，导致页面样式错乱。
移开焦点时，键盘收起，键盘区域空白，未回落。
产生原因
我们在app 布局中会有个固定的底部。安卓一些版本中，输入弹窗出来，会将解压 absolute 和 fixed 定位的元素。导致可视区域变小，布局错乱。
原理与解决方案
软键盘将页面顶起来的解决方案，主要是通过监听页面高度变化，强制恢复成弹出前的高度。
// 记录原有的视口高度
const originalHeight = document.body.clientHeight || document.documentElement.clientHeight;

window.onresize = function(){
  var resizeHeight = document.documentElement.clientHeight || document.body.clientHeight;
  if(resizeHeight < originalHeight ){
    document.body.clientHeight = originalHeight;
  }
}
复制代码键盘不能回落问题出现在 iOS 12+ 和 wechat 6.7.4+ 中，而在微信 H5 开发中是比较常见的 Bug。
兼容原理，1.判断版本类型 2.更改滚动的可视区域
const isWechat = window.navigator.userAgent.match(/MicroMessenger\/([\d\.]+)/i);
if (!isWechat) return;
const wechatVersion = wechatInfo[1];
const version = (navigator.appVersion).match(/OS (\d+)_(\d+)_?(\d+)?/);
 
 // 如果设备类型为iOS 12+ 和wechat 6.7.4+，恢复成原来的视口
if (+wechatVersion.replace(/\./g, '') >= 674 && +version[1] >= 12) {
  window.scrollTo(0, Math.max(document.body.clientHeight, document.documentElement.clientHeight));
}
复制代码
window.scrollTo(x-coord, y-coord)，其中window.scrollTo(0, clientHeight)恢复成原来的视口

4.微信公众号分享问题
表现
在微信公众号 H5 开发中，页面内部点击分享按钮调用 SDK，方法不生效。
解决方案
解决方法：添加一层蒙层，做分享引导。
因为页面内部点击分享按钮无法直接调用，而分享功能需要点击右上角更多来操作。
然后用户可能不知道通过右上角小标里面的功能分享。又想引导用户分享，这时应该怎么做呢？
技术无法实现的，从产品出发。