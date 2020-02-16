title: JavaScript 原生语法
author: John Doe
date: 2018-11-23 00:57:24
tags:
---
var anv=document.getElementsByTagName('body')[0]
var abc=document.createElement('div')

e.preventDefault()阻止事件默认行为。
e.stopPropagation()阻止事件冒泡

# 屏蔽tmall的广告下载
``` bash
var links=document.getElementsByTagName('div');
for(var i=0;i<links.length;i++){  
  if( links[i].style.zIndex=="99999"){ 
     console.log(links[i]); links[i].parentNode.removeChild(links[i]);
  }
}
```

# promise wiki
https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014345008539155e93fc16046d4bb7854943814c4f9dc2000#0
``` bash
function multiply(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' x ' + input + '...');
        setTimeout(resolve, 500, input * input);
    });
}

// 0.5秒后返回input+input的计算结果:
function add(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' + ' + input + '...');
        setTimeout(resolve, 500, input + input);
    });
}

var p = new Promise(function (resolve, reject) {
    log('start new Promise...');
    resolve(123);
});

p.then(multiply)
 .then(add)
 .then(multiply)
 .then(add)
 .then(function (result) {
    log('Got value: ' + result);
});
```

# js递归方法需要return 这个方法，不然开始调用的地方收不到这个值

```
this.findMin=function(){
  return findMinPrivate(root);
}
   var findMinPrivate=function(node){
debugger;
if(node && node.left==null){
    return node.key;

}else {
if(node){
     node=node.left;
   return findMinPrivate(node);
}
}}
```

# 这是函数栈,然后想象一下，每次去把执行的函数压栈，执行完毕再出栈。



```
function foo(i) {
  if (i < 0)
  return;
  console.log('begin:' + i);
  foo(i - 1);
  console.log('end:' + i);
}
foo(3);
```

# 闭包
#示例二展示了一个我们工作中常见的场景，我们预期的值应该是0,1,2..实际结果都输出了最后一次循环的值。原因是这里的i我们是在window下声明可以，当我们调用funs数组中的方法时，循环已经结束i的值已经变成了5。们可以用闭包来解决这个问题：
```
var funs = []
var i=0
for(; i<5; i++) {
    (function parent(i){
        funs.push(function child() {
            console.log(i)
        })
    })(i)
}
funs[0]() // 0
funs[1]() // 1
funs[2]() // 2
```
我们声明了一个parent函数并立即调用了它，把i当做参数传入作为parent的部变量，并在child函数中调用i形成了一个闭包。

