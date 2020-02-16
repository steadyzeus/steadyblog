title: 浅谈vue和react的数据流和绑定
author: John Doe
date: 2019-11-27 03:48:41
tags:
---
数据流：
Vue和React均是单向数据流传递

单向数据流指只能从一个方向修改数据，姑且我们可以这样理解，如下图所示。一个父组件下有两个子组件1和子组件2，父组件可以向子组件传递数据。假如子组件都获取到了父组件的name，在子组件1中对name重新修改之后，子组件2和父组件中的值并不会发生改变，这正是因为Vue中的机制是单向数据流，子组件不能直接改变父组件的状态。但反过来，如果是父组件中的name修改了，当然两个子组件中的name也就改变了。


数据的绑定 :
Vue是双向绑定和React是单向绑定

它们俩主要是由MVVM框架实现，在Vue中主要由三个部分组成，View、ViewModel和Model组成，其中View和Model不能直接进行通信，他们要通过中间件ViewModel来进行。例如，当Model部分数据发生改变时，由于vue中Data Binding将底层数据和Dom层进行了绑定，ViewModel通知View层更新视图；当在视图 View数据发生变化也会同步到Model中。View和Model之间的同步完全是自动的，不需要人手动的操作DOM,所以叫双向绑定。

而在React中虽然modal和view之间也是通过ViewMode处理,但是l却需要setState去手动刷新渲染view，所以叫单向绑定
