title: React 高阶组件 属性代理和反向继承 生命周期调研
author: John Doe
date: 2019-12-22 01:43:58
tags:
---
## 属性代理(Props Proxy)： HOC 对传给 WrappedComponent W 的 porps 进行操作，反向继承(Inheritance Inversion)： HOC 继承 WrappedComponent W。


### 1.属性代理

使用 Props Proxy 可以做什么？

操作 props
通过 Refs 访问到组件实例
提取 state
用其他元素包裹 WrappedComponent
```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.message = 'World';
  }
  componentWillMount(){
        console.log("属性代理1 willmount");  
        }
        componentDidMount(){
        console.log("属性代理1 didmount");
        }
  render() {
    return (<h1>Hello {this.message}</h1>);
  }
}

const MyContainer = (WrappedComponent) => 
    
    class extends React.Component {
        componentWillMount(){
        console.log("HOC属性代理1 willmount");  
        }
        componentDidMount(){
        console.log("HOC属性代理1 didmount");
        }
      
        render(){
            return <WrappedComponent {...this.props} />
        }
    }
const Hocfirst=MyContainer(App);

ReactDOM.render(<Hocfirst/>, window.root);

\\\

"HOC属性代理1 willmount"
"属性代理1 willmount"
"属性代理1 didmount"
"HOC属性代理1 didmount"
```

### 2.反向继承
```js
function replacer(key, value) {
  if (typeof value === 'function') {
    return `function ${value.name}() {...}`
  }

  return value
}

function stringify(value) {
  return JSON.stringify(value, replacer, 2)
}

// II debug example
// We are using the Inheritance Inversion technique to display
// the current state and props of the WrappedComponent (the component you want to debug).
// This is based on the technique that Mickael Jackson and Ryan Florence recommend
function IIHOC(WrappedComponent) {
  return class II extends WrappedComponent {
    render() {
      return (
        <div>
          <h2>
            HOC Debugger Component
          </h2>
          <p>
            Props
          </p>
          <pre>{stringify(this.props)}</pre>
          <p>
            State
          </p>
          <pre>{stringify(this.state)}</pre>
          {super.render()}
        </div>
      )
    }
  }
}


class Example extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      name: 'fran',
      email: 'franleplant@gmail.com'
    }
  }

  render() {
    return (
      <div>
        <h2>
          Wrapped Component
        </h2>
        <p>Im a wrapped component</p>
      </div>
    )
  }
}

const EnhancedExample = IIHOC(Example)

ReactDOM.render(<EnhancedExample date={(new Date).toISOString()} callback={function test() {}}/>, document.getElementById('root'))


E2：

class ComponentChild extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      num: 2019
    }
  }
 
  componentDidMount() {
    console.log("child component Did Mount")
  }
  clickComponent() {
    console.log("Component click")
  }
 
  render() {
    return ( <div > {
        this.state.num
      } </div>
    )
  }
}
 
 
 
let iihoc = (WrapComponet) => class extends WrapComponet {
 
  constructor(props) {
 
      super(props)
 
      this.state = {
 
          num: 2000
 
      }
 
  }
 
  componentDidMount() {
 
      console.log('iihoc componentDidMount')
 
      this.clickComponent()
 
  }
render(){
  return (
 
    <div>
 
        <div onClick={this.clickComponent}>iiHoc 点击</div>
 
       {/* <div><WrapComponet /></div> */}//用的是父组件的state；
       <div>{super.render()}</div>//反向继承，用的是子组件的state；并且会劫持父组件的生命周期钩子函数componentDidMount，不会执行父组件的componentDidMount
    </div>
 
)
}
}
 
 
var App = iihoc(ComponentChild)
 
ReactDOM.render(<App/>, window.root);

```
