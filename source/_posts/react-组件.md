title: 如何封装react 组件
author: Steady
date: 2020-11-03 20:43:27
tags:
---
由于公司需要一套自己的前端UI公共组件，所以有这次分享

本篇文章重点阐述 SRP，即单一职责原则。
React组件式开发方式。你可以将复杂的用户界面分割为一个个组件，利用组件的可重用性和抽象的DOM操作。
基于组件的开发是高效的：一个复杂的系统是由专门的、易于管理的组件构建的。然而，只有设计良好的组件才能确保组合和复用的好处。
尽管应用程序很复杂，但为了满足最后期限和意外变化的需求，你必须不断地走在架构正确性的细线上。你必须将组件分离为专注于单个任务，并经过良好测试。
不幸的是，遵循错误的路径总是更加容易：编写具有许多职责的大型组件、紧密耦合组件、忘记单元测试。这些增加了技术债务，使得修改现有功能或创建新功能变得越来越困难。
编写React应用程序时，经常问自己：
• 如何正确构造组件？
• 在什么时候，一个大的组件应该拆分成更小的组件？
• 如何设计防止紧密耦合的组件之间的通信？
幸运的是，可靠的组件具有共同的特性。让我们来研究以下有用的标准，并将其详细到案例研究中。
# 单一职责
当一个组件只有一个改变的原因时，它有一个单一的职责。
编写React组件时要考虑的基本准则是单一职责原则。单一职责原则(缩写：SRP)要求组件有一个且只有一个变更的原因。
组件的职责可以是呈现列表，或者显示日期选择器，或者发出 HTTP 请求，或者绘制图表，或者延迟加载图像等。你的组件应该只选择一个职责并实现它。当你修改组件实现其职责的方式（例如，更改渲染的列表的数量限制），它有一个更改的原因。
为什么只有一个理由可以改变很重要？因为这样组件的修改隔离并且受控。单一职责原则制了组件的大小，使其集中在一件事情上。集中在一件事情上的组件便于编码、修改、重用和测试。
下面我们来举几个例子
实例1：一个组件获取远程数据，相应地，当获取逻辑更改时，它有一个更改的原因。
发生变化的原因是：
• 修改服务器URL
• 修改响应格式
• 要使用其他HTTP请求库
• 或仅与获取逻辑相关的任何修改。
示例2：表组件将数据数组映射到行组件列表，因此在映射逻辑更改时有一个原因需要更改。
发生变化的原因是：
• 你需要限制渲染行组件的数量（例如，最多显示25行）
• 当没有要显示的项目时，要求显示提示信息“列表为空”
• 或仅与数组到行组件的映射相关的任何修改。
你的组件有很多职责吗？如果答案是“是”，则按每个单独的职责将组件分成若干块。
如果您发现SRP有点模糊，请阅读本文。
在项目早期阶段编写的单元将经常更改，直到达到发布阶段。这些更改通常要求组件在隔离状态下易于修改：这也是 SRP 的目标。
## 1.1 多重职责陷阱
当一个组件有多个职责时，就会发生一个常见的问题。乍一看，这种做法似乎是无害的，并且工作量较少：
• 你立即开始编码：无需识别职责并相应地规划结构
• 一个大的组件可以做到这一切：不需要为每个职责创建组成部分
• 无拆分-无开销：无需为拆分组件之间的通信创建 props 和 callbacks
这种幼稚的结构在开始时很容易编码。但是随着应用程序的增加和变得复杂，在以后的修改中会出现困难。同时实现多个职责的组件有许多更改的原因。现在出现的主要问题是：出于某种原因更改组件会无意中影响同一组件实现的其它职责。
不要关闭电灯开关，因为它同样作用于电梯。
这种设计很脆弱。意外的副作用是很难预测和控制的。
例如，<ChartAndForm> 同时有两个职责，绘制图表，并处理为该图表提供数据的表单。<ChartandForm> 就会有两个更改原因：绘制图表和处理表单。
当你更改表单字段（例如，将 <input> 修改为 <select> 时，你无意中中断图表的渲染。此外，图表实现是不可重用的，因为它与表单细节耦合在一起。
解决多重责任问题需要将 <ChartAndForm> 分割为两个组件：<Chart> 和<Form>。每个组件只有一个职责：绘制图表或处理表单。组件之间的通信是通过props 实现。
多重责任问题的最坏情况是所谓的上帝组件（上帝对象的类比）。上帝组件倾向于了解并做所有事情。你可能会看到它名为 <Application>、<Manager> 、<Bigcontainer> 或 <Page>，代码超过500行。
在组合的帮助下使其符合SRP，从而分解上帝组件。(组合（composition）是一种通过将各组件联合在一起以创建更大组件的方式。组合是 React 的核心。)
  
## 1.2 案例研究：使组件只有一个职责

设想一个组件向一个专门的服务器发出 HTTP 请求，以获取当前天气。成功获取数据时，该组件使用响应来展示天气信息：
``` js
import axios from 'axios';
// 问题: 一个组件有多个职责
class Weather extends Component {
    constructor(props) {
        super(props);
        this.state = { temperature: 'N/A', windSpeed: 'N/A' };
    }
    render() {
        const { temperature, windSpeed } = this.state;
        return (
            <div className="weather">
                <div>Temperature: {temperature}°C</div>
                <div>Wind: {windSpeed}km/h</div>
            </div>
        );
    }
    componentDidMount() {
        axios.get('http://weather.com/api').then(function (response) {
            const { current } = response.data;
            this.setState({
                temperature: current.temperature,
                windSpeed: current.windSpeed
            })
        });
    }
}
```

在处理类似的情况时，问问自己：是否必须将组件拆分为更小的组件？通过确定组件可能会如何根据其职责进行更改，可以最好地回答这个问题。
这个天气组件有两个改变原因：
1. componentDidMount() 中的 fetch 逻辑：服务器URL或响应格式可能会改变。
2. render() 中的天气展示：组件显示天气的方式可以多次更改。
解决方案是将 <Weather> 分为两个组件：每个组件只有一个职责。命名为 <WeatherFetch> 和 <WeatherInfo>。
<WeatherFetch> 组件负责获取天气、提取响应数据并将其保存到 state 中。它改变原因只有一个就是获取数据逻辑改变。
  
``` js
import axios from 'axios';
// 解决措施: 组件只有一个职责就是请求数据
class WeatherFetch extends Component {
    constructor(props) {
        super(props);
        this.state = { temperature: 'N/A', windSpeed: 'N/A' };
    }
    render() {
        const { temperature, windSpeed } = this.state;
        return (
            <WeatherInfo temperature={temperature} windSpeed={windSpeed} />
        );
    }
    componentDidMount() {
        axios.get('http://weather.com/api').then(function (response) {
            const { current } = response.data;
            this.setState({
                temperature: current.temperature,
                windSpeed: current.windSpeed
            });
        });
    }
}
```

这种结构有什么好处？
例如，你想要使用 async/await 语法来替代 promise 去服务器获取响应。更改原因：修改获取逻辑
// 改变原因: 使用 async/await 语法
``` js
class WeatherFetch extends Component {
    // ..... //
    async componentDidMount() {
        const response = await axios.get('http://weather.com/api');
        const { current } = response.data;
        this.setState({
            temperature: current.temperature,
            windSpeed: current.windSpeed
        });
    }
}
```

因为 <WeatherFetch> 只有一个更改原因：修改 fetch 逻辑，所以对该组件的任何修改都是隔离的。使用 async/await 不会直接影响天气的显示。
<WeatherFetch> 渲染 <WeatherInfo>。后者只负责显示天气，改变原因只可能是视觉显示改变。
// 解决方案: 组件只有一个职责，就是显示天气
  
  ``` js
function WeatherInfo({ temperature, windSpeed }) {
    return (
        <div className="weather">
            <div>Temperature: {temperature}°C</div>
            <div>Wind: {windSpeed} km/h</div>
        </div>
    );
}
  ```
让我们更改<WeatherInfo>，如不显示 “wind:0 km/h” 而是显示 “wind:calm”。这就是天气视觉显示发生变化的原因：
// 改变原因: 无风时的显示
  ``` js
function WeatherInfo({ temperature, windSpeed }) {
    const windInfo = windSpeed === 0 ? 'calm' : `${windSpeed} km/h`;
    return (
        <div className="weather">
            <div>Temperature: {temperature}°C</div>
            <div>Wind: {windInfo}</div>
        </div>
    );
}
  ```
  
同样，对 <WeatherInfo> 的修改是隔离的，不会影响 <WeatherFetch> 组件。
<WeatherFetch> 和 <WeatherInfo> 有各自的职责。一种组件的变化对另一种组件的影响很小。这就是单一职责原则的作用：修改隔离，对系统的其他组件产生影响很轻微并且可预测。


## 1.3 案例研究：HOC 偏好单一责任原则

按职责使用分块组件的组合并不总是有助于遵循单一责任原则。另外一种有效实践是高阶组件(缩写为 HOC)
高阶组件是一个接受一个组件并返回一个新组件的函数。
HOC 的一个常见用法是为封装的组件增加新属性或修改现有的属性值。这种技术称为属性代理：
``` js
function withNewFunctionality(WrappedComponent) {
    return class NewFunctionality extends Component {
        render() {
            const newProp = 'Value';
            const propsProxy = {
                ...this.props,
                // 修改现有属性:
                ownProp: this.props.ownProp + ' was modified',
                // 增加新属性:
                newProp
            };
            return <WrappedComponent {...propsProxy} />;
        }
    }
}
const MyNewComponent = withNewFunctionality(MyComponent);
```
你还可以通过控制输入组件的渲染过程从而控制渲染结果。这种 HOC 技术被称为渲染劫持：
``` js
function withModifiedChildren(WrappedComponent) {
    return class ModifiedChildren extends WrappedComponent {
        render() {
            const rootElement = super.render();
            const newChildren = [
                ...rootElement.props.children,
                // 插入一个元素
                <div>New child</div>
            ];
            return cloneElement(
                rootElement,
                rootElement.props,
                newChildren
            );
        }
    }
}
const MyNewComponent = withModifiedChildren(MyComponent);
```
如果您想深入了解HOCS实践，我建议您阅读“深入响应高阶组件”。
让我们通过一个例子来看看HOC的属性代理技术如何帮助分离职责。
组件 <PersistentForm> 由 input 输入框和按钮 save to storage 组成。更改输入值后，点击 save to storage 按钮将其写入到 localStorage 中。
input 的状态在 handlechange(event) 方法中更新。点击按钮，值将保存到本地存储，在 handleclick() 中处理：
``` js
class PersistentForm extends Component {
    constructor(props) {
        super(props);
        this.state = { inputValue: localStorage.getItem('inputValue') };
        this.handleChange = this.handleChange.bind(this);
        this.handleClick = this.handleClick.bind(this);
    }
    render() {
        const { inputValue } = this.state;
        return (
            <div className="persistent-form">
                <input type="text" value={inputValue}
                    onChange={this.handleChange} />
                <button onClick={this.handleClick}>Save to storage</button>
            </div>
        );
    }
    handleChange(event) {
        this.setState({
            inputValue: event.target.value
        });
    }
    handleClick() {
        localStorage.setItem('inputValue', this.state.inputValue);
    }
}
  ```
遗憾的是： <PersistentForm> 有2个职责：管理表单字段；将输入只保存中 localStorage。
让我们重构一下 <PersistentForm> 组件，使其只有一个职责：展示表单字段和附加的事件处理程序。它不应该知道如何直接使用存储：
  ``` js
class PersistentForm extends Component {
    constructor(props) {
        super(props);
        this.state = { inputValue: props.initialValue };
        this.handleChange = this.handleChange.bind(this);
        this.handleClick = this.handleClick.bind(this);
    }
    render() {
        const { inputValue } = this.state;
        return (
            <div className="persistent-form">
                <input type="text" value={inputValue}
                    onChange={this.handleChange} />
                <button onClick={this.handleClick}>Save to storage</button>
            </div>
        );
    }
    handleChange(event) {
        this.setState({
            inputValue: event.target.value
        });
    }
    handleClick() {
        this.props.saveValue(this.state.inputValue);
    }
}
  ```
组件从属性初始值接收存储的输入值，并使用属性函数 saveValue(newValue) 来保存输入值。这些props 由使用属性代理技术的 withpersistence() HOC提供。
现在 <PersistentForm> 符合 SRP。更改的唯一原因是修改表单字段。
查询和保存到本地存储的职责由 withPersistence() HOC承担：
  ``` js
function withPersistence(storageKey, storage) {
    return function (WrappedComponent) {
        return class PersistentComponent extends Component {
            constructor(props) {
                super(props);
                this.state = { initialValue: storage.getItem(storageKey) };
            }
            render() {
                return (
                    <WrappedComponent
                        initialValue={this.state.initialValue}
                        saveValue={this.saveValue}
                        {...this.props}
                    />
                );
            }
            saveValue(value) {
                storage.setItem(storageKey, value);
            }
        }
    }
}
  ```
withPersistence()是一个 HOC，其职责是持久的。它不知道有关表单域的任何详细信息。它只聚焦一个工作：为传入的组件提供 initialValue 字符串和 saveValue() 函数。
将 <PersistentForm> 和 withpersistence() 一起使用可以创建一个新组件<LocalStoragePersistentForm>。它与本地存储相连，可以在应用程序中使用：
const LocalStoragePersistentForm
    = withPersistence('key', localStorage)(PersistentForm);
const instance = <LocalStoragePersistentForm />;
只要 <PersistentForm> 正确使用 initialValue 和 saveValue()属性，对该组件的任何修改都不能破坏 withPersistence() 保存到存储的逻辑。
反之亦然：只要 withPersistence() 提供正确的 initialValue 和 saveValue()，对 HOC 的任何修改都不能破坏处理表单字段的方式。
SRP的效率再次显现出来：修改隔离，从而减少对系统其他部分的影响。
此外，代码的可重用性也会增加。你可以将任何其他表单 <MyOtherForm> 连接到本地存储：
const LocalStorageMyOtherForm
    = withPersistence('key', localStorage)(MyOtherForm);
const instance = <LocalStorageMyOtherForm />;
你可以轻松地将存储类型更改为 session storage：
const SessionStoragePersistentForm
    = withPersistence('key', sessionStorage)(PersistentForm);
const instance = <SessionStoragePersistentForm />;
初始版本 <PersistentForm> 没有隔离修改和可重用性好处，因为它错误地具有多个职责。
在不好分块组合的情况下，属性代理和渲染劫持的 HOC 技术可以使得组件只有一个职责。

# 封装
一个封装组件提供 props 控制其行为而不是暴露其内部结构。
耦合是决定组件之间依赖程度的系统特性。根据组件的依赖程度，可区分两种耦合类型：
• 当应用程序组件对其他组件知之甚少或一无所知时，就会发生松耦合。
• 当应用程序组件知道彼此的许多详细信息时，就会发生紧耦合。
松耦合是我们设计应用结构和组件之间关系的目标。
松耦合应用(封装组件)
松耦合会带来以下好处：
• 可以在不影响应用其它部分的情况下对某一块进行修改。、
• 任何组件都可以替换为另一种实现
• 在整个应用程序中实现组件复用，从而避免重复代码
• 独立组件更容易测试，增加了测试覆盖率
相反，紧耦合的系统会失去上面描述的好处。主要缺点是很难修改高度依赖于其他组件的组件。即使是一处修改，也可能导致一系列的依赖组件需要修改。
紧耦合应用(组件无封装)
封装 或 信息隐藏 是如何设计组件的基本原则，也是松耦合的关键。
## 信息隐藏
封装良好的组件隐藏其内部结构，并提供一组属性来控制其行为。
隐藏内部结构是必要的。其他组件没必要知道或也不依赖组件的内部结构或实现细节。
React 组件可能是函数组件或类组件、定义实例方法、设置 ref、拥有 state 或使用生命周期方法。这些实现细节被封装在组件内部，其他组件不应该知道这些细节。
隐藏内部结构的组件彼此之间的依赖性较小，而降低依赖度会带来松耦合的好处。
## 通信
细节隐藏是隔离组件的关键。此时，你需要一种组件通信的方法：props。porps 是组件的输入。
建议 prop 的类型为基本数据（例如，string 、 number 、boolean）：
<Message text="Hello world!" modal={false} />;
必要时，使用复杂的数据结构，如对象或数组：
<MoviesList items={['Batman Begins', 'Blade Runner']} />
prop 可以是一个事件处理函数和异步函数：
<input type="text" onChange={handleChange} />
prop 甚至可以是一个组件构造函数。组件可以处理其他组件的实例化：
function If({ component: Component, condition }) {
    return condition ? <Component /> : null;
}
<If condition={false} component={LazyComponent} />
为了避免破坏封装，请注意通过 props 传递的内容。给子组件设置 props 的父组件不应该暴露其内部结构的任何细节。例如，使用 props 传输整个组件实例或 refs 都是一个不好的做法。
访问全局变量同样也会对封装产生负面影响。
## 案例研究：封装修复
组件的实例和状态对象是封装在组件内部的实现细节。因此，将状态管理的父组件实例传递给子组件会破坏封装。
我们来研究一下这种情况。
一个简单的应用程序显示一个数字和两个按钮。第一个按钮增加数值，第二个按钮减少数值：
这个应用由两个组件组成：<App> 和 <Controls>.
number 是 <App> 的 state 对象，<App> 负责 将这个数字渲染到页面。
// 问题: 封装被破坏
  ``` js
class App extends Component {
    constructor(props) {
        super(props);
        this.state = { number: 0 };
    }
    render() {
        return (
            <div className="app">
                <span className="number">{this.state.number}</span>
                <Controls parent={this} />
            </div>
        );
    }
}
  ```
<Controls> 负责渲染按钮，并为其设置事件处理函数，当用户点击按钮时，父组件的状态将会被更新：number 加1或者减1((updateNumber()方法`)
// 问题: 使用父组件的内部结构
  ``` js
class Controls extends Component {
    render() {
        return (
            <div className="controls">
                <button onClick={() => this.updateNumber(+1)}>
                    Increase
          </button>
                <button onClick={() => this.updateNumber(-1)}>
                    Decrease
          </button>
            </div>
        );
    }
    updateNumber(toAdd) {
        this.props.parent.setState(prevState => ({
            number: prevState.number + toAdd
        }));
    }
}
  ```
当前的实现有什么问题？
• 第一个问题是： <App> 的封装被破坏，因为它的内部结构在应用中传递。<App> 错误地允许 <Controls> 直接去修改其 state。
• 第二个问题是: 子组件 Controls 知道了太多父组件 <App> 的内部细节，它可以访问父组件的实例，知道父组件是一个有状态组件，知道父组件的 state 对象的细节(知道 number 是父组件 state 的属性)，并且知道怎么去更新父组件的 state.
一个麻烦的结果是： <Controls> 将很难测试和重用。对 <App> 结构的细微修改会导致需要对 <Controls> 进行修改（对于更大的应用程序，也会导致类似耦合的组件需要修改）。
解决方案是设计一个方便的通信接口，考虑到松耦合和封装。让我们改进两个组件的结构和属性，以便恢复封装。
只有组件本身应该知道它的状态结构。<App> 的状态管理应该从 <Controls>（updateNumber()方法）移到正确的位置：即 <App> 组件中。
<App> 被修改为 <Controls> 设置属性 onIncrease 和 onDecrease。这些是更新 <App> 状态的回调函数:
// 解决: 恢复封装
  ``` js
class App extends Component {
    constructor(props) {
        super(props);
        this.state = { number: 0 };
    }
    render() {
        return (
            <div className="app">
                <span className="number">{this.state.number}</span>
                <Controls
                    onIncrease={() => this.updateNumber(+1)}
                    onDecrease={() => this.updateNumber(-1)}
                />
            </div>
        );
    }
    updateNumber(toAdd) {
        this.setState(prevState => ({
            number: prevState.number + toAdd
        }));
    }
}
              ```
现在，<Controls> 接收用于增加和减少数值的回调，注意解耦和封装恢复时：<Controls> 不再需要访问父组件实例。也不会直接去修改父组件的状态。
而且，<Controls> 被修改为了一个函数式组件:
// 解决方案: 使用回调函数去更新父组件的状态
              ``` js
function Controls({ onIncrease, onDecrease }) {
    return (
        <div className="controls">
            <button onClick={onIncrease}>Increase</button>
            <button onClick={onDecrease}>Decrease</button>
        </div>
    );
}
              ```
<App> 组件的封装已经恢复，状态由其本身管理，也应该如此。
此外，<Controls> 不在依赖 <App> 的实现细节，onIncrease 和 onDecrease 在按钮被点击的时候调用，<Controls> 不知道(也不应该知道)这些回调的内部实现。
<Controls> 组件的可重用性和可测试性显著增加。
<Controls> 的复用变得很容易，因为它除了需要回调，没有其它依赖。测试也变得简单，只需验证单击按钮时，回调是否执行。

# 组成结构和权威组成部分。只需将它们分成较小的块，然后使用合成将整个背面粘合在一起，即可使复杂的过程变得简单。

## 单一责任

组成的一个重要方面是能够由较小的专用组件组成复杂的组件。这种分而治之的方法有助于授权机构遵循单一责任原则。
回顾以前的代码片段。<Application>负责呈现页眉，页脚，侧边栏和主要区域。
有意义的划分这个责任为四个子的责任，每个由专门的组件来实现<Header>，<Sidebar>，<Content>和<Footer>。后来的合成<Application>从这些专用组件重新粘合。
现在带来好处。组合<Application>通过允许其子级执行子职责而使其遵循单一职责原则。

## 可重用性
使用组合的组件可以重用通用逻辑。这就是可重用性的好处。
例如，组件<Composed1>和<Composed2>共享通用代码：
``` js
const instance1 = (
  <Composed1>
    /* Specific to Composed1 code... */
    /* Common code... */
  </Composed1>
);
const instance2 = (
  <Composed2>
    /* Common code... */
    /* Specific to Composed2 code... */
  </Composed2>
);
```

由于重复代码是一种不好的做法，因此如何使组件重用通用代码？
首先，将通用代码封装在一个新组件中<Common>。其次，<Composed1>并<Composed2>应使用组成包括<Common>，修复代码重复：
  ``` js
const instance1 = (
  <Composed1>
    <Piece1 />
    <Common />
  </Composed1>
);
const instance2 = (
  <Composed2>
    <Common />
    <Piece2 />
  </Composed2>
);
  ```
可重用组件有利于“不要重复自己（DRY）”原则。这种有益的做法节省了精力和时间。

# 可重用的组件是设计良好的系统的结果。尽可能重复使用代码，以避免重复。
一个可重用的组件写入一次，但多次使用。 
想象一个幻想世界，其中软件开发主要是在重新发明轮子。
编码时，不能使用任何现有的库或实用程序。即使在整个应用程序中，您也无法使用已经编写的代码。
在这种环境下，是否可以在合理的时间内编写应用程序？当然不。
欢迎重用。使事情起作用，而不是重新发明它们的工作方式。
## 4.1跨应用程序重用
根据“不要重复自己（DRY）”原则，每条知识在系统中必须具有唯一，明确，权威的表示形式。该原则建议避免重复。
代码重复会增加复杂性和维护工作，而不会增加重大价值。逻辑的更新迫使您修改应用程序中的所有克隆。
重复性问题通过可重用的组件解决。一次编写，多次使用：高效省时的策略。
但是，您不会免费获得可重用性属性。如果组件符合单一职责原则并具有正确的封装，则可以重用。
遵守单一责任至关重要：
重用组件实际上意味着重用其责任实现。 
仅负责一项的组件最容易重用。
但是，当一个组件错误地承担多个职责时，其重用会增加沉重的开销。您只想重用一个职责实施，还可以获取不适当的职责不必要的实施。
您想要一个香蕉，就得到一个香蕉，再加上所有的丛林。
正确的封装会创建一个不依赖于依赖项的组件。隐藏的内部结构和集中的支柱使组件可以很好地适合要重用的多个位置。
## 4.2重用第三方库
一个正常的工作日。您已经阅读了向应用程序添加新功能的任务。启动文本编辑器之前，请等待几分钟……
您开始解决的问题很有可能已经解决。由于React的受欢迎程度和强大的开源社区，值得寻找现有的解决方案。
请查看brillout / awesome-react-components存储库，该存储库具有可重用组件的已编译列表。
好的图书馆会对架构决策产生积极影响，并倡导最佳实践。以我的经验，最有影响力的人物是react-router和redux。
react-router使用声明性路由来构建单页应用程序。
使用将URL路径与您的组件相关联<Route>。然后，当用户访问匹配的URL时，路由器将为您呈现该组件。
redux和react-redux HOC引入了单向和可预测的应用程序状态管理。它从组件中提取异步和不纯净的代码（例如HTTP请求），支持单一职责原则并创建纯净或几乎纯净的组件。

# 诸如网络请求或全局变量之类的副作用使组件取决于环境。通过为相同的prop值返回相同的输出来使其纯净。
甚至认为使用Redux都需要其他构造，例如动作，减速器和Sagas，它有助于使<FetchWeather>纯净。
让我们进行修改<WeatherFetch>以与Redux一起使用：
``` js  
import { connect } from 'react-redux';
import { fetch } from './action';
export class WeatherFetch extends Component {
   render() {
     const { temperature, windSpeed } = this.props;
     return (
       <WeatherInfo temperature={temperature} windSpeed={windSpeed} />
     );
   }
   componentDidMount() {
     this.props.fetch();
   }
}
function mapStateToProps(state) {
  return {
    temperature: state.temperate,
    windSpeed: state.windSpeed
  };
}
export default connect(mapStateToProps, { fetch });
connect(mapStateToProps, { fetch })HOC包装<WeatherFetch>。
  ```

# 有意义的组件命名和表达性代码是可读性的关键。您的代码必须易于理解并且欢迎阅读。
一个有意义的组件很容易理解它的作用。 
很难低估可读代码的重要性。您多少次被代码遮住了？您看到字符，但看不到含义。
开发人员花费大量时间阅读和理解代码，而不是实际编写代码。编码活动占75％的时间理解代码，20％的时间修改现有代码和5％的代码编写新代码（源）。
花一点时间在可读性上会减少以后对队友和您自己的理解时间。随着应用程序的增长，命名实践变得很重要，因为理解的努力会随着代码量的增加而增加。
读取有意义的代码很容易。但是，有意义地编写代码需要简洁的代码实践和不断的努力以清晰地表达自己。
## 帕斯卡案
组件名称是在pascal情况下一个或多个单词（主要是名词）的串联。例如<DatePicker>，<GridItem>，<Application>，<Header>。

## 专业化
组件越特殊，其名称可能包含的单词越多。
名为的组件<HeaderMenu>建议在标题中显示菜单。名称<SidebarMenuItem>表示位于侧栏中的菜单项。
当名称有意义地暗示意图时，组件很容易理解。为此，通常必须使用冗长的名称。很好：冗长多于清晰。
假设您浏览一些项目文件并确定2个组件：<Authors>和<AuthorsList>。仅根据名称，您能得出它们之间的区别吗？很有可能不会。
要获取详细信息，您必须打开<Authors>源文件并浏览代码。完成此操作后，您将意识到<Authors>从服务器获取作者列表并呈现<AuthorsList>表示性组件。
使用更专业的名称代替<Authors>不会导致这种情况。更好的名称<FetchAuthors>，<AuthorsContainer>或<AuthorsPage>。
简洁胜于简洁。

## 一个字-一个概念
一个词代表一个概念。例如，呈现项目概念的集合由列表词表示。
每个概念选择一个词，然后在整个应用程序中保持关系的一致性。结果是可预测的单词心理映射-您已经习惯了概念。
当同一概念由许多单词表示时，可读性会受到影响。例如，您定义一个呈现订单列表的组件，<OrdersList>另一个定义呈现费用列表的组件<ExpensesTable>。
呈现项目集合的相同概念由两个不同的词表示：list和table。没有理由为同一概念使用不同的词。它增加了混乱并破坏了命名的一致性。
命名组件<OrdersList>和<ExpensesList>（使用列表词）或<OrdersTable>和<ExpensesTable>（使用表词）。使用您认为更好的任何单词，只要保持一致即可。
测试不仅是自动检测错误的方法。如果您发现难以测试的组件，则很可能是设计错误的组件。

# 在封装一个组件的时候应该先思考什么?
这个组件应该是做什么的
这个组件应该至少需要知道那些信息
这个组件会反馈什么东西
在设计一个组件的时候我们不应该仅限于实现当前的需求，
设计出一个只适用于单一项目的组件，而是应该是一个可以适应大部分同种需求的通用组件。
所以我们在碰到一个需求的时候应该首先对需求进行抽象，而不是看到设计稿就撸着袖子上。
例如碰到一个轮播图组件的需求的时候，我们拆分以下这个需求，可以得到：
（1） 这个组件要做什么：
可以展示多张图片
可以向左向右翻页，或者是可以是上下翻页
PageControl的状态会根据图片的滚动而相应改变 还有可能有一些隐藏的需求，类似于：
应该支持左右两侧或者上下无限循环滚动
可以选择的是否自动轮播
支持手动滑动切换图片
图片有点击事件，可以点击来进行相关的事件反应
（2）这个组件至少应该知道什么信息
一个好的组件应该是要像存在魔法一样，只需要极其少数的参数和条件就可以得到期望的效果。就像这个轮播图组件一样，组件应该至少知道的信息有：
图片的url地址数组
当图片不存在时候的占位图
其他可以知道也可以不知道的信息可以有：
是否开启自动轮播，默认是开启或者不开启
图片滚动是左右还是上下，默认是左右
等等 ………………………………
（3）这个组件会反馈什么
一个可用的轮播图效果