title: 记录package.json字段用处
author: John Doe
date: 2019-02-26 01:28:53
tags:
---
npm install 就是安装模块，npm run dev  就是执行npm script中的命令。当我们执行npm命令的时候，它到哪里去找，这就要说到每个node项目中都有的核心文件package.json 文件。

　　项目开始时，我们就要建立这个文件。假设我们要创建一个node 项目，我们会新建一个文件夹命名node， 这时我们就要创建package.json文件。进入node 文件夹，你可以手动创建，就和创建一个txt文件一样，但我们一般都会使用命令创建，打开cmd 窗口，输入npm init， 这时你要回答几个问题，如name,version 等，输入内容，一路回车键，当你输入yes的时候，package.json 创建成功，你的文件夹中多了这个文件。

　　其中有几个字段比较重要：

　　name 项目名称， 你可能在项目中使用过 var express = require(‘express’), require 函数后面的参数，就是package.json 中的name字段，所以这个name一定要简短，且不能有大写，这是规定。

　　version版本号：安装一个模块的时候， 你可能指定过特定的版本号，npm install express @4.13.2, 版本号4.13.2 就是我们这里的version。版本号有三个组成部分，4：表示的是大版本，一般是重大升级。13：表示的是小版本, 在大版本的基础进行的小的更新，如某个功能废弃了，新增了那个功能。2： 对该版本进行补丁，主要是版本bug的修复。

　　script： 我们在命令行中执行的所有命令都写在这个地方，然后用 npm run 去执行这个命令。

　　项目初始完成后，我们就可以进行项目开发。在开发过程中我们都会用到一些第三方库和 框架，尤其是node 开发, 因为它提供的API 有点底层. 如果想用第三方的东西，就要事先安装。上面说到 安装用的命令是npm install。 npm install express --save  --save 是什么意思？--save表示，我们安装模块的时候，同时把它写到package.json 文件中。这时打开package.json 文件，我们看到多了一个dependencies字段，它包括了我们刚安装的express. node中有些es6/es7 的语法不支持，我们使用的时候，就需要进行转换。这时安装babel. npm install babel-cli babel-preset-es2015 --save–dev  --save-dev 又是什么，它也表示安装依模块的时候，把它写到package.json中，不过它写入的不是dependencies, 而是devDependencies中。

　　devDependencies 和dependencies 有什么区别？dependencies: 是项目运行时的依赖，就是程序上线后仍然需要依赖，比如express, 我们程序就是用express 写的，如果没有express, 我们的程序根本无法运行，更直白一点，dependencies 就是我们在程序开发的过程中手动require的模块。进行express 开发时,server.js中，都会写  var express  = require(‘express’), 我们程序直接依赖，所以是dependencies. 

　　devDependencies, 开发依赖，就是我们在开发过程中需要的依赖。比如babel, 它只负责转换es6+ 到es5， 转换完成后，我们只要转换后的代码，上线的时候，直接把转换后的代码部署上线，不需要bebal.  这就是开发依赖，只在开发时候起作用， 上线不需要。其实就是我们在使用webpack开发时，它配置文件里所有的依赖，都是开发依赖。

　　无论devDependencies还是dependencies中，安装的模块版本号前面还有符号^, 其实这里还有很多符号也可以无符号，符号主要是限定版本。

　　"express": "4.15.2" 版本号前面什么符号都没有，它表示固定版本，安装版本时，只会安装这个指定的版本。

　　"express": "~4.15.2"，版本号前面有符号~，它表示安装4.15.x 的版本，只x>3 就可以。在这里，我们express指定是4.15.2 版本，当我们npm install 安装的时候，它可能在项目中安装4.15.5或者4.15.6 版本。

　　"express": "^4.15.2" , 版本号前面有符号^, 它表示可以安装4.x.x 的版本，只要中间的x  大于15就可以。

　　>=4.15.3  版本号前面有符号>=, 它安装大于我们指定的版本，就可以。

　　有时还看到一个*， 表示安装最新版本。

　　版本号一定要注意，因为有些框架和库在进行版本升级的时候，向后兼容性必较差，容易引起代码冲突。但npm install 进行安装的时候，它默认是^ 符号，如果不符合我们要求，我们可以对package.json 进行手动修改，如 把^号改成~, 或直接去掉符号， package.json文件，只是一个文件， 我们可以手动地进行任何修改。

　　最后再说一下package.json 中的scripts. 这个字段主要用于运行命令。我们用es6 写一个hello World项目体验下。在node文件夹中新建index.js 

由于node 不支持 import 命令，所以要把它转成require 的形式，这要用到babel 命令: babel index.js –o server.js，由于babel-cli  是安装到本地的，所以不能全局使用，那么这个命令写在什么地方？就是写在scripts 中，



　　在scripts中的命令，都要用npm run 命令名启动。这时在命令行中输入npm run build，可以看到目录中多了一个server.js文件，再在命令行中输入node server, 就可以启动服务器。浏览器地址栏中输入localhost:8080, 看到hello world.

　　这里我们在命令行中输入了两次命令，其实可以把这两个命令合并到 一个命令中，修改scripts 中的build 如下， 直接npm run build  就可以启动服务器。

"build": "babel index.js -o server.js && node server"  
　　npm 也可以做部分的任务自动化。

　　整个package.json文件如下：

```
{
  "name": "node",
  "version": "1.0.0",
  "description": "node project",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel index.js -o server.js && node server"  
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.15.2"
  },
  "devDependencies": {
    "babel-cli": "^6.24.1",
    "babel-preset-es2015": "^6.24.1"
  }
}
```
