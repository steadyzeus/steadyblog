title: vue 记录
author: John Doe
tags: []
categories: []
date: 2019-08-30 05:42:00
---


# vue中事件修饰符详解(stop, prevent, self, once, capture, passive)


.stop 是阻止冒泡行为,不让当前元素的事件继续往外触发,如阻止点击div内部事件,触发div事件

.prevent 是阻止事件本身行为,如阻止超链接的点击跳转,form表单的点击提交

.self 是只有是自己触发的自己才会执行,如果接受到内部的冒泡事件传递信号触发,会忽略掉这个信号

.capture 是改变js默认的事件机制,默认是冒泡,capture功能是将冒泡改为倾听模式

.once 是将事件设置为只执行一次,如 .click.prevent.once 代表只阻止事件的默认行为一次,当第二次触发的时候事件本身的行为会执行

.passive 滚动事件的默认行为 (即滚动行为) 将会立即触发，而不会等待 onScroll 完成。这个 .passive 修饰符尤其能够提升移动端的性能。

————————————————

.passive 和 .prevent 不能一起使用:
.prevent 将会被忽略

.self 和 .stop 区别:
self只响应当前元素自身触发的事件，不会响应经过冒泡触发的事件，并不会阻止冒泡继续向外部触发。
stop是从自身开始不向外部发射冒泡信号


#  this.$set
使用示列：this.$set(arr,  index,  val)。当然，this.$set除了用于操作数组外还可以操作对象，使用示例：this.$set( obj, key, val).


