title: JavaScript 事件冒泡和捕获
author: John Doe
date: 2018-11-23 02:42:26
tags:
---
JS事件冒泡与捕获
# 1事件传播——冒泡与捕获
　　默认情况下，事件使用冒泡事件流，不使用捕获事件流。然而，在Firefox和Safari里，你可以显式的指定使用捕获事件流，方法是在注册事件时传入useCapture参数，将这个参数设为true。
# 2冒泡事件流
　　当事件在某一DOM元素被触发时，例如用户在客户名字节点上点击鼠标，事件将跟随着该节点继承自的各个父节点冒泡穿过整个的DOM节点层次，直到它遇到依附有该事件类型处理器的节点，此时，该事件是onclick事件。在冒泡过程中的任何时候都可以终止事件的冒泡，在遵从W3C标准的浏览器里可以通过调用事件对象上的stopPropagation()方法，在Internet Explorer里可以通过设置事件对象的cancelBubble属性为true。如果不停止事件的传播，事件将一直通过DOM冒泡直至到达文档根。
# 3捕获事件流
　　事件的处理将从DOM层次的根开始，而不是从触发事件的目标元素开始，事件被从目标元素的所有祖先元素依次往下传递。在这个过程中，事件会被从文档根到事件目标元素之间各个继承派生的元素所捕获，如果事件监听器在被注册时设置了useCapture属性为true,那么它们可以被分派给这期间的任何元素以对事件做出处理；否则，事件会被接着传递给派生元素路径上的下一元素，直至目标元素。事件到达目标元素后，它会接着通过DOM节点再进行冒泡。
复制代码
``` bash
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>bubble event</title>
    <style type="text/css">
        body{margin:0;}
        #one{
            width:500px;
            height:300px;
            background:rgb(255,0,0);
        }
        #two{
            width:400px;
            height:260px;
            background:rgb(255,50,50);
        }
        #three{
            width:300px;
            height:240px;
            background:rgb(255,100,100);
        }
        #four{
            width:200px;
            height:200px;
            background:rgb(255,150,150);
        }
    </style>
</head>
<body>
    <div id='one'>
      <div id='two'>
        <div id='three'>
          <div id='four'>
          </div>
        </div>
      </div>
    </div>
     
    <script>
        var one = document.getElementById('one');
        var two = document.getElementById('two');
        var three = document.getElementById('three');
        var four = document.getElementById('four');
    
        var useCapture = true; //false为冒泡获取【目标元素先触发】    true为捕获获取【父级元素先触发】
        one.addEventListener('click', function() {
            console.log('one');
        }, useCapture);
        two.addEventListener('click', function() {
            console.log('two');
        }, useCapture);
        three.addEventListener('click', function() {
            console.log('three');
        }, useCapture);
        four.addEventListener('click', function() {
            console.log('four');
        }, useCapture);        
        /*
        false
        冒泡
        点击four div
        输出结果：four three two one        
        
        true
        捕获
        点击four div
        输出结果： one two three four
        */
    </script>
</body>
</html>
复制代码
分析：
```

![upload successful](/images/pasted-6.png)
addEventListener第三个参数useCapture ，true时为捕获，false时为冒泡

冒泡从目标对象开始，向父级元素至window传递；捕获从window底层逐级至目标对象传递！





# jQuery中的事件委托方式比较丰富，就以同样的例子来说：

   1、用on方法,代码如下：
``` bash
复制代码
1  $(function(){
2             $("#lists").on("click","li",function(event){
3                 var target = $(event.target);
4                 target.css("background-color","red");
5             })
6         })
```



# 阻止事件冒泡
jQuery 提供了两种方式来阻止事件冒泡。

``` bash
**方式一: **event.stopPropagation();

$("#div1").mousedown(function(event){
    event.stopPropagation();
});

**方式二：**return false;

$("#div1").mousedown(function(event){
    return false;
});
```
<font color=red>但是这两种方式是有区别的。return false 不仅阻止了事件往上冒泡，而且阻止了事件本身。event.stopPropagation() 则只阻止事件往上冒泡，不阻止事件本身。</font>

