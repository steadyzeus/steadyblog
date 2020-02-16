title: nodejs exports
author: John Doe
tags:
  - steadytag
categories:
  - steadycate
date: 2019-07-23 21:29:00
---
# exports 与 module.exports

为了方便，Node为每个模块提供一个exports变量，指向module.exports。这等同在每个模块头部，有一行这样的命令。

var exports = module.exports;
于是我们可以直接在 exports 对象上添加方法，表示对外输出的接口，如同在module.exports上添加一样。注意，不能直接将exports变量指向一个值，因为这样等于切断了exports与module.exports的联系。

# 不同于CommonJS，ES6使用 export 和 import 来导出、导入模块。
```
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```

# 两个差异
CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

什么是值的拷贝，值的引用？
所谓值的拷贝，是在栈内存当中实现的，是一个复制的过程，复制之后两个值互不影响
值的引用，是在堆内存当中实现的，当一个引用类型赋值给一个变量时，此时变量拥有指向引用类型的一个指针，当变量发生改变，被引用的值也会发生改变
CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

什么是运行时加载，什么是编译输出？
在后端开发当中，只需要做到一次加载，因此在启动服务器中进行加载即可，对于CommonJS的设计当中符合后端的这种开发理念，然后这种效果带来了很大的便利性，在前端开发也是需要模块化加载方式，可是，前端的加载方式跟后端不一致，如果采用CommonJS相同的加载方式，前端性能会大打折扣，比如网页假死，以及难以改变引入模块的变量，因此，在编译阶段，先将模块按需进行处理，生成接口，在引入模块之后进行使用时，可以很方便的调用接口
export 与 export default (params表示一个对象)
export 输出的时候必须是一个接口，因此export 后面跟的是 { …params} 或则以声明变量的形式 var name = 。。。。或则export function

export default 中 default 相当于 export 之后的一个接口名称，因此之后可以表达式或则 { …params} 对象

加载方式

```
import * from ‘./test’
import { key, key1, key2 } from ‘./test’

modele.exports 与 exports (params表示一个对象)
modele.exports = {}写法
```
exports为var exports = modele.exports，因此exports后面重新赋值会改变指针，故不能为exports重新赋值，例如exports = newObject，只能类似exports.name = 'hello world’这种方式进行输出 , 实际上输出的是modele.exports = {name: ‘hello world’}

加载方式

var common = require(’./test’)






简单来说就是，在CommonJS规范下，通过require()导入的是模块的一份值拷贝，可以任意修改，且不会影响到原模块的值。修改原模块的值也不会变动当前文件下的值。-- 二者在导入那一刻失去了联系。

在ES6规范下，打入只是和原模块建立连接，当前文件下不允许修改原模块，即只读。修改原模块的值，会在当前文件下也起作用。

看下面的例子就很清晰了：

CommonJS规范下值拷贝
```
// calculator.js
var count = 0;
module.exports = {
	count: count,
	add: function(a, b) {
		count += 1;
		return a + b;
	}
};

// index.js
var count = require('./calculator.js').count;
var add = require('./callculator.js').add;

console.log(count); // 0
add(2, 3); // calculator.js中的count会变化
console.log(count); // 0，这里的count不受变化

count += 1; // 本地count值可变化
console.log(count); // 1

ES6规范下动态映射

// calculator.js
let count = 0;
const add = function(a, b) {
	count += 1;
	return a + b;
};
export { count, add };

// index.js
import { count, add } from './calculator.js';
console.log(count); // 0
add(2, 3); // 
console.log(count); // 1, 实时反映calculator.js中的count值的变化

count += 1; // 不允许，count是只读的
```
