title: React HOC ref 用法
author: Steady
date: 2020-06-04 10:26:11
tags:
---
昨天参加面试被问到hoc 父组件的static 方法的传递

关键点就是React.forwardRef的API中ref必须指向dom元素而不是React组件。



一、React.forwardRef使用示例
下面就是应用到React组件的错误示例：

const A=React.forwardRef((props,ref)=><B {...props} ref={ref}/>)
这就是我之前经常犯的错误， 这里的ref是无法生效的。

前面提到ref必须指向dom元素，那么正确方法就应用而生：
```js
const  A=React.forwardRef((props,ref)=>(
<div ref={ref}>
<B {...props} />
</div>
))
```

二、React.forwardRef应用到高阶组件中
2.1. withComponent类型的高阶组件【1】
```jd
import React from 'react'
import A from './a.jsx'
import PropTypes from 'prop-types';

function withA(Component){
    const ForWardedComponent = React.forwardRef((props, ref) => <div ref={ref}>
               <Component {...props} />
           </div>);
     class MidComponent extends React.Component {
        render() {
            const props = this.props
            return (
                <A {...props}>
                  <ForWardedComponent  ref={props.forwardedRef} {...props}/>
            </A>
            )
        }
    }
    
    //对MidComponent组件属性进行类型经查 
    MidComponent.propTypes = {
        forwardedRef: PropTypes.object,
    }
    return  MidComponent
}
exports.withA=withA
这样，在上述示例的组件A中，A的周期componentDidMount() 调用 this.props.forwardedRef.current ,指向的就是上述示例中ForWardedComponent对应的dom元素。
是B组件对应的dom的父元素，而不是该dom
在a.jsx中某处:

    componentDidMount(){
     console.log(this.props.forwardedRef.current)
    }
最后应用实例：

import React from 'react'
import ReactDOM from  'react-dom'
//假设withA存储于withA.js文件。
import {withA}   from  './withA.js'  
 const B=()=><h2>hello world</h2>
const B2=withA(B)
class App extends React.Component {
      constructor(props) {
        super(props)
        this.forwardedRef=React.creactRef()        
        }
        
        render() {
           return  <div>
               <B2  forwardedRef={this.forwardedRef}/>
           </div>
        }
}

ReactDOM.render(<App/>,document.getElementById('app'))
     
2.2 纯粹的高阶组件（Parent-Child）
【1】中并不是React组件，只是一个React组件为参数的函数，调用以后才成为React组件。那么直接写入一个Parent组件又该如何呢？

import React from 'react'
import A from './a.jsx'
import PropTypes from 'prop-types';

function AasParent(props){
    const ForWardedComponent = React.forwardRef((props, ref) => <div ref={ref}>
               {props.children}
           </div>);
      return (
                <A {...props}>
                  <ForWardedComponent  ref={props.forwardedRef} {...props}/>
            </A>)
}
AasParent.propTypes = {
        forwardedRef: PropTypes.object,
    }
 
module.exports=AasParent
最后应用实例：

import React from 'react'
import ReactDOM from  'react-dom'
//假设AasParent存储于AasParent.jsx文件。注意与【1】中的区别
import AasParent   from  './AasParent.jsx'  
 const B=(props)=><h2>{props.greetings}</h2>

class App extends React.Component {
      constructor(props) {
        super(props)
        this.forwardedRef=React.creactRef()        
        }
        
        render() {
           return  <AasParent forwardedRef={this.forwardedRef}>
               <B2  greetings="你好，Melo"/>
           </AasParent>
        }
}

ReactDOM.render(<App/>,document.getElementById('app'))
     
```

三、总结 
1.React.forwardRef的API中ref必须指向dom元素而不是React组件。
2.在【1】的组件A中，A的周期componentDidMount() 调用 this.props.forwardedRef.current ,指向的就是【1】中ForWardedComponent对应的dom元素。是【1】中B组件对应的dom的父dom元素，而不是该dom。