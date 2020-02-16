title: chrome插件开发教程
author: John Doe
date: 2019-11-18 02:58:35
tags:
---
相关链接

代码仓库： git@git.tuan800-inc.com:ued/cd_ued_chrome_extensions.git ，相关开发流程和使用说明在项目的README中均有描述
参考

 

Chrome扩展及应用开发（首发版），这本书非常实用，配合书中的项目，可以快速上手Chrome扩展开发
Chrome扩展程序官方文档，查API就靠它了，
非官方中文文档，可以先快速过一遍api
这里主要整理了chrome开发技术、cd_ued_chrome_extensions项目的开发环境和打包流程以及Chrome应用商店的使用步骤。

Chrome开发相关技术
扩展程序由HTML、CSS和JavaScript等web技术组成，并由浏览器提供了一系列增强API。

在项目文件夹根目录新建一个manifest.json的文件，用于声明相关配置。配置文件主要用来指定文件路径、，配置权限等。

{   
    // 区分扩展程序和应用，在扩展程序中只能为2
    "manifest_version": 2,
    "name": "扩展程序测试",
    "version": "1.0",
    "description": "Chrome扩展",
    // 在chrome://extensions/扩展程序列表下的图标
    "icons": {},
    // 当进入指定URL页面时调用对应脚本
    "content_scripts": [],
    // 在浏览器右上角的扩展程序的属性
    "browser_action": {},
    // 一些权限设置
    "permissions": [],
    // 一直在后台运行的脚本
    "background": {},
    // 选项页面
    "options_page": "options.html"
}
整个扩展程序可分为下面几个部分

content_scripts，进入指定URL页面可注入的脚本
background，在后台运行
browser_action，右上角弹窗
options_page，扩展程序选项页面
content_scripts
扩展程序一个非常重要的功能就是可以操作用户正在浏览的页面。在配置文件中通过指定对应的match字段，匹配页面url，并在浏览器访问这些页面时，注入对应的脚本文件。

"content_scripts": [
  {
    "matches": [
      "*://local.com/*", // 其中 * 表示通配符
      "*://www.amazon.com/*"
    ],
    "js": [
      "js/content.js"
    ]
  }
]
content_scripts里面的脚本运行在于网页文档相同的环境下，包括访问和操作页面的DOM和BOM。

browser_action
有时候需要扩展程序快速展示一些信息功能，右上角的弹窗页面就很有用，弹窗页可以看做是扩展程序与用户的交互窗口。

"browser_action": {
  "default_icon": {
    "19": "images/icon19.png",
    "38": "images/icon38.png"
  },
  "default_popup": "popup.html" // 可以在页面上通过script标签引入对应的页面js文件
},
options_page
如果配置了options_page选项，并制定了对应的页面，则右击右上角图标是，会出现“选项”栏，点击可跳转到对应的选项页面。选项页面一般用于为用户进行一些插件配置操作，诸如偏好设置等。

options_page可以看做是我们为扩展程序提供的一个主页。

background
上面部分是基于前端进行开发的，包括DOM、html、CSS等，可以发现，他们都需要用户的主动操作，要么访问对应的页面，要么点击右上角的图标弹出页面，这类页面统称为UI页面。如果希望扩展程序自动运行并常驻后台来实现一些特定的功能，就可以使用background后台页面。

"background": {
    "page": "background.html"
  },
background在后台启动时就会开始运行，可以用于进行插件数据的初始化等操作。

页面交互
一个扩展程序往往不止一个页面，有时候页面之间需要进行数据传递或事件通知，这时候需要考虑页面交互的问题。

本地存储
一种常见的应用场景是选项页面和其他页面之间的配置选项交互，由于同一个扩展程序的页面(除了注入页面的content_scripts)被认为在同一个域下，因此只需要通过localStorage本地存储就可以轻松做到数据的传递。

UI页面与后台页面的交互
另外一种应用场景时某个页面需要主动通知其他页面，可以通过chrome.runtime接口进行

// content.js 发送信息
chrome.runtime.sendMessage(message);

// background.js 接收消息
chrome.runtime.onMessage.addListener(function(message, sender, sendResponse) { // message就是传递的data });
 

由于sendMessage无法指定特定的listener，因此onMessage的监听器会接收到所有的消息推送，如果需要区分消息，则需要开发者自己实现相关的逻辑。message可以是字符串也可以是对象，因此可以在参数上携带发送者的消息。

let messageStrategy = {
  // ... 对应的命令逻辑
}
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
    let {command, content} = message
    messageStrategy[command] && messageStrategy[command](content)
});
后台页面与标签页交互
可以通过向当前的标签页发送消息，从而获取页面内容的信息

sendMessageToCurrentTab(data) {
    return new Promise((resolve, reject) => {
        chrome.tabs.query({active: true, currentWindow: true}, function (tabs) {
            chrome.tabs.sendMessage(tabs[0].id, data, function (response) {
                resolve(response)
            });
        });
    })
}
一些常见问题
脚本只能通过script标签引入，使用行内脚本会提示
配置了popup页面之后，会导致监听插件按钮点击事件`chrome.browserAction.onClicked.addListener`无效，这是因为默认事件被打开popup.html阻止了，可以在页面初始化逻辑中进行处理点击事件相关的逻辑
Chrome提供的大部分API是不支持在content_scripts中运行的，在background中执行没有问题
注意配置的match，如果需要在全站页面使用，后面的路径记得配置*，例如*://www.amazon.com/*，否则只有首页可以注入脚本...
项目开发打包流程
## 开发环境
由于后期需要维护多个扩展程序，因此搭建此开发环境，便于开发和后期维护

由于插件各个脚本存在一定的关联，如storage的key值、公共函数、公共接口等，需要引入模块化开发。

由于开发预览需要及时更新的文件，因此采用`gulp + watch`的形式搭建开发环境，使用`gulp-webpack`进行打包

```
# 开启文件改动监听
gulp js:w

# 打包生产环境的文件
gulp js --env production
```

* webpack相关配置位于`gulp-webpack`的webpack配置项下，用于修改项目名称和相关文件，
* 插件源文件开发位于`插件名/src`目录下，最终打包的文件位于`插件名/js`目录下，插件配置文件位于`插件名/manifest.json`下
* 开发时装配的插件直接使用`插件名`目录这个即可

## 打包
由于是本地安装chrome插件，因此只需要输出对应的插件资源即可，执行命令`npm run build + 对应插件目录名`即可

```
npm run build piseer
```

实际上打包过程只是剔除不必要的文件，在dist目录下生成对应的压缩包，方便传递或部署到SVN上。

相关的打包脚本位于`scripts/build.js`下

## 发布
由于chrome应用商店提供了扩展程序自动更新的机制，因此部分扩展程序可以通过托管到应用商店，只需要将打包的压缩包在[开发者中心](https://chrome.google.com/webstore/developer/dashboard/)添加上传即可。

发布相关
* 发布应用的chrome账号为`steadyzhe@gmail.com`，密码找云龙提供
* 发布需要上传的压缩包，直接使用`npm run build xxx`生成的压缩包即可
* 发布后一般一个小时内会在应用商店更新，在开发者中心可查看发布进度，一般开发者中心的显示已发布后还需要等待一会应用商店才会更新
* 如果manifest.json中的permissions过多（貌似还有其他一些限制），在更新包时可能会被系统检测导致人工审核，建议只申明必须的扩展程序权限。

插件在应用商店的地址
* [亚马逊插件](https://chrome.google.com/webstore/detail/shoppinghelper/phlggmjehjabmkeljbfimpgjkanlmbgc?utm_source=gmail)，权限为非公开
 自动更新
相关工单： 
* [亚马逊插件自动更新调研](http://redmine.tuan800-inc.com/issues/370684)
* [湃势插件自动更新调研](http://redmine.tuan800-inc.com/issues/372979)
采用开发者方式安装本地插件包，需要每次更新插件后手动重新下载并解压压缩包，因此需要实现自动更新机制。

 

通过应用商店托管扩展程序，有以下特性

扩展程序安全性，这里的安全性指的是允许用户安装的策略，在发布应用时有公开、非公开、指定账户访问三种等级，其中
公开是所有用户都可以通过应用商店搜索到该扩展程序
非公开则需要知道扩展程序的下载链接，才能够进行安装，无法直接通过搜索应用商店直接安装
指定账户访问则需要在开发者中心添加体验用户的chrome账号，才能够安装该插件，采用这种方式则需要收集使用者的chrome账户
自动更新，后续只需要增加版本号并重新在开发者中心发布，chrome会自动更新相关插件，一般会在新版本发布后6个小时内进行更新
强制更新，chrome自动更新有一定时间的延迟，如果在发布后想要立即体验更新版本，可以使用强制更新
打开`chrome://extensions/`，选择开发者模式，然后点击更新按钮，强制更新全部扩展程序
chrome账户重新登录，在登录状态下安装的扩展程序，重新登录后chrome会进行数据同步，此时也会更新到最新的版本
修改chrome扩展更新频率，可以通过在启动chrome时通过参数`--extensions-update-frequency=45`指定更新检测频率
采用非公开方式发布扩展程序，用户只需要第一次点击链接进行扩展程序的安装即可，后续更新都会由chrome自动更新完成，之前通过接口获取配置规则的逻辑也可由发布新版本的机制代替。

Chrome应用商店开发者中心相关操作流程
首先进入开发者中心https://chrome.google.com/webstore/developer/dashboard/g05227480483751387811?page=1&pli=1，应该需要输入当前Chome账号密码



在“您的列表”下面找到“添加新内容”，打开上传页面；也可以选在已发布的插件，点击右侧的修改操作，进入编辑页面，然后重新上传更新后的包



点击“选择文件”，选择`npm run build`打包的压缩文件，然后点击上传，等到文件上传完毕，会自动跳转到编辑页面



在编辑页面需要填写扩展程序的基本信息，如介绍、图标、轮播图等，此处填写的内容会经过应用商店的审核，某些内容可能会导致审核不通过~



编辑完成后拉到页面最下面，点击发布按钮即可，此处的公开程度选项决定了是否在应用商店展示该扩展程序。

发布更改后等待一段时间（一般几分钟到几个小时不等），就可以在应用商店安装对应的扩展程序了。

之前经历过连续审核未通过的情形，需要根据被拒邮件列举的理由进行调整并重新发布更改，常见的理由有

未上传图标、空白描述、无预览效果图等
未描述产品隐私策略，需要附上隐私政策网址，在描述中详细介绍使用用户数据做什么事情