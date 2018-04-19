# Redux实践笔记_001_context API & 项目目录结构组织

> @auther: [Yiniau](https://yiniau.com)
> @tags: **JavaScript**, **React**, **Redux**
> @quote: [React context](https://reactjs.org/docs/context.html#why-not-to-use-context), [How to safely use React context](https://medium.com/@mweststrate/how-to-safely-use-react-context-b7e343eff076)

---

## Why I use redux

其实在这个博客项目中并没有使用Redux，然后我就切实体会到了react中数据管理的难处，子组件向父组件传数据被react的单向数据流所限制，我使用的是在父组件中定义好函数，然后将函数传递给自组件，子组件通过 `props.function(data)` 这种函数参数传值的形式向父组件传递数据。

另一种实现是依赖官方的context API，这里就顺便学习一下react的 context API

### React‘s context API

#### before start

在正式的学习开始之前，react 文档就警告了我们尽可能不要使用context

- 如果你想要你的应用程序是稳定的，不要使用上下文。这是一个实验性的API，它可能在未来的React版本中被打破。
- 如果你不熟悉 Redux 或者 MobX 这样的状态管理库，也最好不要使用context，直接使用它们会是更好的选择，大部分情况下它们都能够满足需求
- 如果你不是一个经验丰富的react开发人员，也不要使用context，它需要和 `props` and `state` 相配合才能愉快的使用
- 如果你依然要使用 context，也请尝试将其隔离到尽可能小的范围內，以便API改动是升级代码

[Dan Abramov](https://medium.com/@dan_abramov)已经制定了一些明智的规则，何时把这颗宝石挂在树上：

![should I use react context feature](https://cdn-images-1.medium.com/max/1600/1*b6Ev2SZ8SBlqhKVrOGDZaA.jpeg)

#### 使用 context

```jsx
import PropTypes from 'prop-types';
import React from 'react';

class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.context.color}}>    
        {this.props.children}                              
      </button>                                            
    );
  }
}

Button.contextTypes = {
  color: PropTypes.string
};

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  getChildContext() {
    return {color: "purple"};
  }

  render() {
    const children = this.props.messages.map((message) =>
      <Message text={message.text} />
    );
    return <div>{children}</div>;
  }
}

MessageList.childContextTypes = {
  color: PropTypes.string
};
```

这个简单的例子使用在 MessageList*(context provider)* 中定义 `childContextTypes` 并使用 `getChildContext` 向Botton传递 `color` 的值，其中需要注意的是Botton中使用 `this.context.color` 接受数据，并且需要在Botton中对context做类型检测，即 `contextTypes` 不能省略，否则 context 将会为 empty Object

#### 生命周期钩子

如果在组件中定义了contextTypes，则以下生命周期方法将接收一个附加参数，即 `context` 对象：

- [`constructor(props, context)`](https://reactjs.org/docs/react-component.html#constructor)
- [`componentWillReceiveProps(nextProps, nextContext)`](https://reactjs.org/docs/react-component.html#componentwillreceiveprops)
- [`shouldComponentUpdate(nextProps, nextState, nextContext)`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)
- [`componentWillUpdate(nextProps, nextState, nextContext)`](https://reactjs.org/docs/react-component.html#componentwillupdate)


#### 在stateless Functional组件中使用context

如果contextType被实现，无状态的功能组件也能够引用context。以下代码显示了一个写成无状态功能组件的Button组件。

```Jsx
import PropTypes from 'prop-types';

const Button = ({children}, context) =>
  <button style={{background: context.color}}>
    {children}
  </button>;

Button.contextTypes = {color: PropTypes.string};
```

#### context 的 update

`getChildContext` 方法会在 `props` 和 `state` 改变的时候被调用

为了更新context中的data，可以在 context provider 组件中触发一个state的更新，这样可以触发一个新的context，并且改动也会被子组件接收。

```jsx
import PropTypes from 'prop-types';

class MediaQuery extends React.Component {
  constructor(props) {
    super(props);
    this.state = {type:'desktop'};
  }

  getChildContext() {
    return {type: this.state.type};
  }

  componentDidMount() {
    const checkMediaQuery = () => {
      const type = window.matchMedia("(min-width: 1025px)").matches ? 'desktop' : 'mobile';
      if (type !== this.state.type) {
        this.setState({type});
      }
    };

    window.addEventListener('resize', checkMediaQuery);
    checkMediaQuery();
  }

  render() {
    return this.props.children;
  }
}

MediaQuery.childContextTypes = {
  type: PropTypes.string
};
```

但是如果在 context provider 组件和 context 接收组件之间的 **中间父组件** 的 `shouldComponentUpdate` 返回了 `false`，那么其子组件就无法更新。

#### safely use context

为了避免 `shouldComponentUpdate` （SCU）或其实现(React.PureComponet & react-redux's connect & react-mobx's oberver) 引起的组件树重渲染的部分缩短问题(context的改变无法体现在末端的组件上)，我们需要遵循几个来避免问题的发生。

- context 不能被改变，它需要是浅不可变的(shallow immutable)
- 组件只能在被修建时接收一次context

> 或者，换句话说，我们不应该直接在context中存储state。相反，我们应该使用context作为依赖注入系统。

这意味着SCU不会再干涉需要传递的context，因为它永远不需要将新的context传递给它的孩子。Awesome！这解决了我们所有的问题！

##### 通过基于上下文的依赖注入(Dependency Injection)来传达变化

除了呃..如果我们想改变主题的颜色呢？很简单，我们有一个依赖注入系统（DI），所以我们可以传递一个store来管理我们的主题并订阅它。我们从来没有经过一个新的store，但只是确保store本身是有状态的，并可以通知组件有关的变化：

```jsx
// Theme stores the state of the current theme, and allows components to subscribe to future changes
class Theme {
  constructor(color) {
    this.color = color
    this.subscriptions = []
  }

  setColor(color) {
    this.color = color
    this.subscriptions.forEach(f => f())
  }

  subscribe(f) {
    this.subscriptions.push(f)
  }
}

class ThemeProvider extends React.Component {
  constructor(p, c) {
    super(p, c)
    // theme provider uses the same Theme object
    // during it's entire lifecycle
    this.theme = new Theme(this.props.color)
  }

  // update theme whenever needed. This propagate changes to subscribed components
  componentWillReceiveProps(next) {
    this.theme.setColor(next.color)
  }

  getChildContext() {
    return {theme: this.theme}
  }

  render() {
    return <div>{this.props.children}</div>
  }
}
ThemeProvider.childContextTypes = {
  theme: React.PropTypes.object
}

class ThemedText extends React.Component {
  componentDidMount() {
    // subscribe to future theme changes
    this.context.theme.subscribe(() => this.forceUpdate())
  }
  render() {
    return <div style={{color: this.context.theme.color}}>
      {this.props.children}
    </div>
  }
}
ThemedText.contextTypes = {
  theme: React.PropTypes.object
}

```

完整的Todo代码

```jsx
const TODOS = ["Get coffee", "Eat cookies"]

class TodoList extends React.PureComponent {
  render() {
    return (<ul>
      {this.props.todos.map(todo =>
        <li key={todo}><ThemedText>{todo}</ThemedText></li>
      )}
    </ul>)
  }
}

class App extends React.Component {
  constructor(p, c) {
    super(p, c)
    this.state = { color: "blue" }
  }

  render() {
    return <ThemeProvider color={this.state.color}>
      <button onClick={this.makeRed.bind(this)}>
      	<ThemedText>Red please!</ThemedText>
      </button>
      <TodoList todos={TODOS} />
    </ThemeProvider>
  }

  makeRed() {
    this.setState({ color: "red" })
  }
}

class Theme {
  constructor(color) {
    this.color = color
    this.subscriptions = []
  }

  setColor(color) {
    this.color = color
    this.subscriptions.forEach(f => f())
  }

  subscribe(f) {
    this.subscriptions.push(f)
  }
}

class ThemeProvider extends React.Component {
  constructor(s, c) {
    super(s, c)
    this.theme = new Theme(this.props.color)
  }

  componentWillReceiveProps(next) {
    this.theme.setColor(next.color)
  }

  getChildContext() {
    return {theme: this.theme}
  }

  render() {
    return <div>{this.props.children}</div>
  }
}
ThemeProvider.childContextTypes = {
  theme: React.PropTypes.object
}

class ThemedText extends React.Component {
  componentDidMount() {
    this.context.theme.subscribe(() => this.forceUpdate())
  }
  render() {
    return <div style={{color: this.context.theme.color}}>
      {this.props.children}
    </div>
  }
}
ThemedText.contextTypes = {
  theme: React.PropTypes.object
}

ReactDOM.render(
  <App />,
  document.getElementById("container")
)
```

> Note this example now reacts correctly to the color change. Yet, it still uses *PureComponent*. Also, the API of the important components *App*, *TodoList* and *ThemedText* didn’t change.
>
> Our *ThemeProvider* implementation has become more complex though. It creates a *Theme* object which holds the state of our theme. *Theme *is also a (poor man’s) event emitter. This enables components like *ThemedText *to subscribe to future changes. The *Theme* object is passed through the component tree by *ThemeProvider*. *Context* is still used for that, but beyond the initial pass the context is not relevant anymore, as future updates are propagated by *Theme *itself, instead of by creating a new context.

这个实现过于简单，一个正确的实现也需要清理componentWillUnmount中的事件监听器，而且应该使用setState而不是forceUpdate。但好消息是，这完全是您正在使用/正在建设的库所关心的问题。它不影响库的使用者。中间组件中意外的shouldComponentUpdate实现将不再打破库的运行。

通过限制上下文的使用只是一个依赖注入系统(DI)而不是一个状态容器(state container)，我们可以使基于上下文的库和shouldComponentUpdate行为正确，没有干扰，也不会破坏消费者的API。而且，非常重要的是，它在React的上下文系统的当前限制内工作。只要坚持这个简单的规则：**Context should be used as if it is received only once by each component.**

## Redux 项目目录结构组织

项目目录结构的组织永远是一个难题，相较于传统的 `action` & `container` & `reducer` 每个角色分配一个文件夹，ducks规范更加让我喜欢。

### Ducks: Redux Reducer Bundles

#### example

```javascript
// widgets.js

// Actions
const LOAD   = 'my-app/widgets/LOAD';
const CREATE = 'my-app/widgets/CREATE';
const UPDATE = 'my-app/widgets/UPDATE';
const REMOVE = 'my-app/widgets/REMOVE';

// Reducer
export default function reducer(state = {}, action = {}) {
  switch (action.type) {
    // do reducer stuff
    default: return state;
  }
}

// Action Creators
export function loadWidgets() {
  return { type: LOAD };
}

export function createWidget(widget) {
  return { type: CREATE, widget };
}

export function updateWidget(widget) {
  return { type: UPDATE, widget };
}

export function removeWidget(widget) {
  return { type: REMOVE, widget };
}

// side effects, only as applicable
// e.g. thunks, epics, etc
export function getWidget () {
  return dispatch => get('/widget').then(widget => dispatch(updateWidget(widget)))
}
```

#### 规则

一个模块...

1. 一定要 `export default` 一个叫 `reducer()` 的函数
2. 一定要 `export` 它的actionCreaters
3. 一定要有形如 `npm-module-or-app/reducer/ACTION_TYPE` 的action type
4. 如果外部reducer需要监听它们, 或者它是已发布的可重用的库, 可以将action type导出为 `UPPER_SNAKE_CASE`

对于作为可重用的Redux库共享的{actionType，action，reducer}捆绑包，建议使用相同的准则。



#### 项目实践

|**| project
|**| |
|**| | ...
|**| |-- src
|**|      |
|**|      |-- components # 项目通用组件
|**|      |-- lib        # 项目通用工具，在业务代码实现过程中逐渐抽象出的可服用工具
|**|      |-- views      # 页面
|**|           |
|**|           |-- Dashboard
|**|           |    |
|**|           |    |-- components            # 页面內组件
|**|           |    |-- containers            # redux 容器
|**|           |    |-- reduxs                # action/actionCreator & action type & reducer
|**|           |    |-- Dashboard.jsx         # 页面根组件
|**|           |    |-- DashboardContainer.js # 在这里注册store，connect组件
|**|           |
|**|           |-- Index
|**|           |    |...
|**|           |
|**|           |... # 横向的页面扩展，多页应用
