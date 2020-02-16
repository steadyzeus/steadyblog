title: webpack打包记录
author: John Doe
date: 2018-12-21 02:59:09
tags:
---
# 解决 vender的hash因为文件新增而改变
两次构建之间，vender chunk的chunkhash以及各模块的模块ID都保持了一致。从而达到了最佳的缓存效果。
https://www.cnblogs.com/zhishaofei/p/8590627.html

1.Webpack 4 抛弃了原有的 CommonChunksPlugin，换成了更为先进的 SplitChunksPlugin，用于提取公用代码。

2.js Tree Shaking
清除到代码中无用的js代码，只支持import方式引入，不支持commonjs的方式引入
只要mode是production就会生效，develpoment的tree shaking是不生效的，因为webpack为了方便你的调试
  optimization: {
    usedExports:true,
  }

3. plugins: [
        new webpack.DllPlugin({
            // 定义程序中打包公共文件的入口文件vendor.js
            
4. 使用happypack并发执行任务
运行在 Node.之上的Webpack是单线程模型的，也就是说Webpack需要一个一个地处理任务，不能同时处理多个任务。 Happy Pack 就能让Webpack做到这一点，它将任务分解给多个子进程去并发执行，子进程处理完后再将结果发送给主进程。
cnpm i -D happypack
 
// webpack.config.js
 rules: [
     {
        // cnpm i babel-loader @babel/core @babel/preset-env -D
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: [
          {
            // 一个loader对应一个id
            loader: "happypack/loader?id=busongBabel"
          }
        ]
      }
      ]
 
//在plugins中增加
plugins:[
      new HappyPack({
      // 用唯一的标识符id，来代表当前的HappyPack是用来处理一类特定的文件
      id:'busongBabel',
      // 如何处理.js文件，用法和Loader配置中一样
      loaders:['babel-loader?cacheDirectory'],
      threadPool: HappyPackThreadPool,
  })
]


5. PWA优化策略
简言之：在你第一次访问一个网站的时候，如果成功，做一个缓存，当服务器挂了之后，你依然能够访问这个网页 ，这就是PWA。那相信你也已经知道了，这个只需要在生产环境，才需要做PWA的处理，以防不测。
 cnpm i workbox-webpack-plugin -D

const WorkboxPlugin = require('workbox-webpack-plugin') // 引入 PWA 插件
const prodConfig = {
  plugins: [
    // 配置 PWA
    new WorkboxPlugin.GenerateSW({
      clientsClaim: true,
      skipWaiting: true
    })
  ]
}

在入口文件加上
// 判断该浏览器支不支持 serviceWorker
if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker
      .register('/service-worker.js')
      .then(registration => {
        console.log('service-worker registed')
      })
      .catch(error => {
        console.log('service-worker registed error')
      })
  })
}

6.使用 HotModuleReplacement (热模块替换HMR)
建立了开发环境本地服务器 后，当修改内容后，网页会同步刷新，我们现在进入toCOunt页面


点击按钮，将数字加到一个不为0的数，比如加到6


然后你可以在代码中改变按钮的文字，随便改点东西，会发现，页面刷新后，数字重新变为0


这显然不是我们想要的，想要的是，能不能把页面的状态保存了，也就是更改了代码后，页面还是保存了数字为6的状态，也就是实现局部更改，首先需要用到：HotModuleReplacementPlugin插件
devServer: {
    hot: true
},
 
plugins: [
    new webpack.HotModuleReplacementPlugin()
],
复制代码完事之后，继续更上边的操作，点击按钮，数字增加，然后更改内容，发现还是没有保存状态。。。what？怎么办
对@！这还没完呢，接着往下看，我们还需要react-hot-loader这个插件
10、react-hot-loader记录react页面留存状态state
我们继续接着上边的进行操作，分一下四步
cnpm i react-hot-loader -D
 
// 在主文件里这样写
 
import React from "react";
import ReactDOM from "react-dom";
import { AppContainer } from "react-hot-loader";-------------------1、首先引入AppContainre
import { BrowserRouter } from "react-router-dom";
import Router from "./router";
 
/*初始化*/
renderWithHotReload(Router);-------------------2、初始化
 
/*热更新*/
if (module.hot) {-------------------3、热更新操作
  module.hot.accept("./router/index.js", () => {
    const Router = require("./router/index.js").default;
    renderWithHotReload(Router);
  });
}
 
 
function renderWithHotReload(Router) {-------------------4、定义渲染函数
  ReactDOM.render(
    <AppContainer>
      <BrowserRouter>
        <Router />
      </BrowserRouter>
    </AppContainer>,
    document.getElementById("app")
  );
}
 