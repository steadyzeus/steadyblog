title: react 生命周期
author: John Doe
date: 2018-11-21 22:05:39
tags:
---
# react 生命周期函数
初始化阶段：

getDefaultProps:获取实例的默认属性

getInitialState:获取每个实例的初始化状态

componentWillMount：组件即将被装载、渲染到页面上

render:组件在这里生成虚拟的 DOM 节点

componentDidMount:组件真正在被装载之后

运行中状态：

componentWillReceiveProps:组件将要接收到属性的时候调用

shouldComponentUpdate:组件接受到新属性或者新状态的时候（可以返回 false，接收数据后不更新，阻止 render 调用，后面的函数不会被继续执行了）

componentWillUpdate:组件即将更新不能修改属性和状态

render:组件重新描绘

componentDidUpdate:组件已经更新

销毁阶段：

componentWillUnmount:组件即将销毁



# shouldComponentUpdate 是做什么的，（react 性能优化是哪个周期函数？）
shouldComponentUpdate 这个方法用来判断是否需要调用 render 方法重新描绘 dom。因为 dom 的描绘非常消耗性能，如果我们能在 shouldComponentUpdate 方法中能够写出更优化的 dom diff 算法，可以极大的提高性能。

参考react 性能优化-sf

# 为什么虚拟 dom 会提高性能?(必考)
虚拟 dom 相当于在 js 和真实 dom 中间加了一个缓存，利用 dom diff 算法避免了没有必要的 dom 操作，从而提高性能。

用 JavaScript 对象结构表示 DOM 树的结构；然后用这个树构建一个真正的 DOM 树，插到文档当中当状态变更的时候，重新构造一棵新的对象树。然后用新的树和旧的树进行比较，记录两棵树差异把 2 所记录的差异应用到步骤 1 所构建的真正的 DOM 树上，视图就更新了。

参考 如何理解虚拟 DOM?-zhihu


# react diff 原理（常考，大厂必考）
把树形结构按照层级分解，只比较同级元素。

给列表结构的每个单元添加唯一的 key 属性，方便比较。

React 只会匹配相同 class 的 component（这里面的 class 指的是组件的名字）

合并操作，调用 component 的 setState 方法的时候, React 将其标记为 dirty.到每一个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制.

选择性子树渲染。开发人员可以重写 shouldComponentUpdate 提高 diff 的性能。

参考：React 的 diff 算法

# 箭头函数设置 onClick事件
![upload successful](/images/pasted-5.png)


#  “React connect 和 shouldComponentUpdate” 组件更新1次

首先，因为connect使用的是HOC模式，所以他基本控制了你组件S的渲染。

其次，在connect中，每次父级传入的props发生改变的时候，都会经过一次shallow equal再决定是否有必要渲染S组件。

最后，虽然你的父组件Content的state发生了改变，但是传入S的props没有发生改变，所以在connect中被拦截，所以组件S就没有渲染。