title: React 高阶组件 属性代理和反向继承 生命周期调研
author: John Doe
date: 2019-12-22 01:43:58
tags:
---
### 1.属性代理
```
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


