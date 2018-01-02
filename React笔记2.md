[toc]
## reactWeek2

### 高阶组件
高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。
这个新的组件会使用你传给它的组件作为子组件.
#### 高阶组件的作用
高阶组件的作用其实不言而喻，其实就是为了组件之间的代码复用。组件可能有着某些相同的逻辑，把这些逻辑抽离出来，放到高阶组件中进行复用。高阶组件内部的包装组件和被包装组件之间通过 props 传递数据。
```
import React, {Component} from 'react'

export default (WrappedComponent) => {
    class NewComponent extends Component {
    //NewComponent这个就可以称为高阶组件,以我的理解,外面的函数称为高阶函数更恰当.
        constructor() {
            super();
            this.state = {
                username: ''
            }
        }

        componentWillMount() {
            let username = localStorage.getItem('username');
            this.setState({
                username: username
            })
        }

        render() {
            return <WrappedComponent username={this.state.username}/>
        }
    }
//返回的新的组件
    return NewComponent
}
```

代码的简单理解就是:高阶组件就是把username通过props传递给目标组件了。目标组件只管从props里面拿来用就好了
### context
>一个组件可以通过 getChildContext 方法返回一个对象，这个对象就是子树的 context，提供 context 的组件必须提供 childContextTypes 作为 context 的声明和验证。

>如果一个组件设置了 context，那么它的子组件都可以直接访问到里面的内容，它就像这个组件为根的子树的全局变量。任意深度的子组件都可以通过 contextTypes 来声明你想要的 context 里面的哪些状态，然后可以通过 this.context 访问到那些状态。

>context 打破了组件和组件之间通过 props 传递数据的规范，极大地增强了组件之间的耦合性。而且，就如全局变量一样，context 里面的数据能被随意接触就能被随意修改，每个组件都能够改 context 里面的内容会导致程序的运行不可预料。
### redux
使用点
1.用户操作复杂
2.用户间的协作
3.需要与服务器大量交互
 4.组件需要共享
 5.一个组件需要改变另一个组建的状态
### redux的核心
#### store
看成是一个保存数据的地方，一个容器。（react中的一个数据库） 整个应用  只能有  有一个store
        提供的方法：
            提供 getState() 方法获取 state；
            提供 dispatch(action) 方法更新 state；唯一一个更新state的方法
            通过 subscribe(listener) 注册监听器; 监听action、state的变化，一旦state发生变化，就是行这个 listener。一般 react 的项目就是组件的render
            或者是setState方法。返回一个函数A，调用A接触监听通过 subscribe(listener) 返回的函数注销监听器。
#### action
 通过 state 的状态来改变显示的东西（View）。但是，用户接触不到state，接触的是View。state变化导致View变化。这个Action就是View发出的通知，表示state应该要变化了
        action是一个对象，{} ，里面至少有一个属性  type（必须拥有），表示的就是action的名字，其他的属性可以自己设置
            {
                type:'ADD',
                text,   ==> text:text
                id    ==> id:id
            }
#### renducer（描述 action 如何改变 state 树）
 render单独放在一个文件夹里面（reducers）用来改变状态的一个函数
        reducer 就是一个纯函数，接收旧的 state 和 action，返回新的 state。（注意： state 的形式取决于你，可以是基本类型、数组、对象、 甚至是 Immutable.js 生成的数据结构。惟一的要点是当 state 变化时需要返回全新的对象，而不是修改传入的参数。）
        store收到action以后，给出一个新的state，View发生改变。
        这种state的计算过程就是reducer

### react-redux
#### Provider
Provider这个模块是作为整个App的容器，在你原有的App Container的基础上再包上一层，它的工作很简单，就是接受Redux的store作为props，并将其声明为context的属性之一，子组件可以在声明了contextTypes之后可以方便的通过this.context.store访问到store。不过我们的组件通常不需要这么做，将store放在context里，是为了给下面的connect用的。
```
// config app root
const history = createHistory()
const root = (
  <Provider store={store} key="provider">
    <Router history={history} routes={routes} />
  </Provider>
)

// render
ReactDOM.render(
  root,
  document.getElementById('root')
)

```
#### connect,mapStateToProps和mapDispatchToProps
首先store中维护了一个state，我们dispatch一个action，接下来reducer根据这个action更新state。

映射到我们的React应用中，store中维护的state就是我们的app state，一个React组件作为View层，做两件事：render和响应用户操作。于是connect就是将store中的必要数据作为props传递给React组件来render，并包装action creator用于在响应用户操作时dispatch一个action。

`connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])`
mapStateToProps是一个函数，返回值表示的是需要merge进props的state。默认值为() => ({})，即什么都不传。

`(state, props) => ({  }) `// 通常会省略第二个参数
mapDispatchToProps是可以是一个函数，返回值表示的是需要merge仅props的actionCreators，这里的actionCreator应该是已经被包装了dispatch了的，推荐使用redux的bindActionCreators函数。

`(dispatch, props) => ({ // 通常会省略第二个参数
 ...bindActionCreators({
   ...ResourceActions
 }, dispatch)
})`
更方便的是可以直接接受一个对象，此时connect函数内部会将其转变为函数，这个函数和上面那个例子是一模一样的。

mergeProps用于自定义merge流程，下面这个是默认流程，parentProps值的就是组件自身的props，可以发现如果组件的props上出现同名，会被覆盖。

`(stateProps, dispatchProps, parentProps) => ({
  ...parentProps,
  ...stateProps,
  ...dispatchProps
})`
options共有两个开关：pure代表是否打开优化，详细内容下面会提，默认为true，withRef用来给包装在里面的组件一个ref，可以通过getWrappedInstance方法来获取这个ref，默认为false。

connect返回一个函数，它接受一个React组件的构造函数作为连接对象，最终返回连接好的组件构造函数。

然后几个问题：

React组件如何响应store的变化？
为什么connect选择性的merge一些props，而不是直接将整个state传入？
pure优化的是什么？
我们把connect返回的函数叫做Connector，它返回的是内部的一个叫Connect的组件，它在包装原有组件的基础上，还在内部监听了Redux的store的变化，为了让被它包装的组件可以响应store的变化:
```
trySubscribe() {
  if (shouldSubscribe && !this.unsubscribe) {
    this.unsubscribe = this.store.subscribe(::this.handleChange)
    this.handleChange()
  }
}

handleChange () {
  this.setState({
    storeState: this.store.getState()
  })
}
```
但是通常，我们connect的是某个Container组件，它并不承载所有App state，然而我们的handler是响应所有state变化的，于是我们需要优化的是：当storeState变化的时候，仅在我们真正依赖那部分state变化时，才重新render相应的React组件，那么什么是我们真正依赖的部分？就是通过mapStateToProps和mapDispatchToProps得到的。

具体优化的方式就是在shouldComponentUpdate中做检查，如果只有在组件自身的props改变，或者mapStateToProps的结果改变，或者是mapDispatchToProps的结果改变时shouldComponentUpdate才会返回true，检查的方式是进行shallowEqual的比较。

所以对于某个reducer来说：

`export default (state = {}, action) => {
  return { ...state } `// 返回的是一个新的对象，可能会使组件reRender
  `return state `// 可能不会使得组件reRender
}
另外在connect的时候，要谨慎map真正需要的state或者actionCreators到props中，以避免不必要的性能损失。

最后，根据connect的API我们发现可以使用ES7 decorator功能来配合React ES6的写法：
```
connect(
  state => ({
    user: state.user,
    resource: state.resource
  }),
  dispatch => ({
    ...bindActionCreators({
      loadResource: ResourceActions.load
    }, dispatch)
  })
)
export default class Main extends Component {

}

```
### react路由
路由传参：
this.props.router.push({
pathname: '/contactsDetail',
query: {
  searchValue: ""
}
});
获得参数：
let searchValue = this.props.location.query.searchValue;


组件的属性：
<Filter ref="filter" toSearch={this.toSearch.bind(this)}
                filterValue={this.state.searchValue}/>

组件属性，与组成组件的html标签的属性，是两个东西，注意区分。
>在子组件里，因为是暴露给父组件的属性，无论是方法还是属性，对于父组件来说，都是属性，所以通过 this.props.属性名 来获取。

>`ref`，在父组件中，写在子组件里的一个属性，子组件不用有体现。ref定义了一个唯一的标识，相当于id.
相同组件，在不同父组件中ref可以是不同的值，但是最好对于一个组件，设置一个相同的值。
在父组件中，通过 this.refs.ref属性值 可以访问。
