[toc]
# ReactWeek1
### 什么是React?
React是一个JavaScript框架,用于构建"可预期的"和"声明式的"Web用户界面.处理视图,渲染页面的
两个特点:
- 1.简单:`简单的表述任意时间点你的应用应该是什么样子的，React将会自动的管理UI界面更新当数据发生变化的时候。`
- 声明式:`在数据发生变化的时候，React从概念上讲与点击了F5一样，实际上它仅仅是更新了变化的一部分而已。
React是关于构造可重用组件的，实际上，使用React你做的仅仅是构建组件。通过封装，使得组件代码复用、测试以及关注点分离更加容易。`
### React主要原理
VirtualDOM 虚拟DOM
React为了尽可能减少对DOM的操作，提供了一种不同的而又强大的方式来更新DOM，代替直接的DOM操作。就是Virtual DOM,一个轻量级的虚拟的DOM，就是React抽象出来的一个对象，描述dom应该什么样子的，应该如何呈现。通过这个Virtual DOM去更新真实的DOM，由这个Virtual DOM管理真实DOM的更新。

>React有个diff算法，更新Virtual DOM并不保证马上影响真实的DOM，React会等到事件循环结束，然后利用这个diff算法，通过当前新的dom表述与之前的作比较，计算出最小的步骤更新真实的DOM。
### JSX语法和 React元素
是JavaScript和XML的混合,是js的语法扩展.
相当于`HTML 语言直接写在 JavaScript 语言之中`
JSX 其实就是 JavaScript 对象,编译之后就是将类似HTML结构的JSX,变成JavaScript对象
使用 JSX 来描述用户界面,声明React当中的元素
在JSX中使用表达式,在 JSX 代码的外面扩上一个小括号,可以有效防止分号自动插入'
`JSX到页面需要中间的编译(React.createElement会构建一个 JavaScript 对象来描述你 HTML 结构的信息，包括标签名、属性、还有子元素等)操作是因为?`
当数据变化，需要更新组件的时候，就可以用比较快的算法操作这个 JavaScript 对象，而不用直接操作页面上的 DOM，这样可以尽量少的减少浏览器重排，极大地优化性能
>1.JSX属性:
- 可以定义以字符串为值的属性
` const element = <div tabIndex="0"></div>`
- 使用大括号定义以JavaScript表达式为值
`const element = <img src={user.avatarUrl}></img>;`
- 表达式是一些变量和操作符的集合，一定要返回一个值
-在HTML标签里的 表达式里不能放对象,可以放字符串、数字等,还可以放函数的调用


>2.JSX的嵌套
```
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```
3.JSX React元素属性
`React DOM 首先会比较元素内容先后的不同，而在渲染过程中只会更新改变了的部分。`
- 普通属性
- 特殊属性 class=className for=htmlFor
-  如果属性名是多个单词的话，一定用驼峰命名法 tab-index=> tabIndex
-  React元素有一个非常重要的属性 children
元素事实上只是构成组件的一个部分
`元素具有不变性，一旦创建就不能修改`

4.ReactDOM渲染过程
 - ReactDOM渲染过程
 - 把JSX元素转成React.createElement方法的调用
 - createElement会返回一个虚拟DOM对象
 - render方法负责把虚拟DOM对象转成真实DOM并插入到容器内部

render方法的实现
```
function render(virtualDOM,container){
  let element = document.createElement(virtualDOM.type);
  for(let attr in virtualDOM.props){
     if(attr == 'children'){
       element.innerHTML = virtualDOM.props[attr];
     }else{
       element.setAttribute(attr,virtualDOM.props[attr])
     }
  }
  container.appendChild(element);
}
```

转译之后会转成
1.参数是元素的类型 2.参数是属性对象 3.子元素
```
function createElement(type,props,children){
   return  {type,props:{...props,children}};
}
```
createElement方法执行完成后会返回一个虚拟DOM对象

### React组件
- 我们可以把一个页划分为若干上独立的组件
- 组件就像一个纯函数，接收任意的参数(props)，返回一个并且只有一个React元素
>声明组件的两种方式
>- 函数式
>这就是一个合法的组件,函数式组件接收一个props的参数，是一个对象
>- 类定义组件
>必须继承自React.Component
函数组件没有实例，也没有this
类组件是有实例的，也有this,所以要通过this来调用属性
```
class Welcome extends React.Component{
  //render用来指定这个组件长什么样子
  render(){
    if(this.props.username){
      return <h1>hello {this.props.username}</h1>;
    }else{
      return <h1>hello stranger</h1>;
    }
  }
}
```
>`react-dom 负责把这个用来描述 UI 信息的 JavaScript 对象变成 DOM 元素，并且渲染到页面上。`

#### 组件的render方法
一个组件类必须要实现一个 render 方法，这个 render 方法必须要返回一个 JSX 元素,必须要用一个外层元素将把内容包起来
```
render () {
  return (
    <div>
      <div>第一个</div>
      <div>第二个</div>
    </div>
  )
}
```
其中内容的插入方式
- 表达式,函数插入
- 根据不同条件返回不同JSX
 ```render () {
  const isGoodWord = true
  return (
    <div>
      <h1>
        React 小书
        {isGoodWord
          ? <strong> is good</strong>
          : null
        }
      </h1>
    </div>
  )
}
 ```
- 元素变量:将元素赋给变量,直接插入变量
`组件的注意`

1.组件的名称必须首字母大写
因为组件和元素的使用方式完全相同，但渲染和使用方式不同。元素是直接渲染，组件是渲染返回值。那么render只能通过首字母来判断这个标签是元素还是组件

2.组件函数要返回并且只能返回一个顶级React元素
>组件的提取
>一些可复用,特别复杂的部分,可以提取出来作为单独的组件;`这样可复用,还容易维护`
>组件的组合
>一个App组件，用来多次渲染Welcome组件
>`组件的返回值只能有一个根元素。这也是我们要用一个<div>来包裹所有<Welcome />元素的原因。`
>`function App() {
    return (
        <div>
          <Welcome name="Sara" />
          <Welcome name="Cahal" />
          <Welcome name="Edite" />
        </div>
    );
}
`
### 组件的props
在不同的场景下对这个组件的需求可能会根据情况有所不同.
组件能适应不同场景下的需求，我们就要让组件具有一定的“可配置”性。
props可以达到这个效果
组件内部是通过 this.props 的方式获取到组件的参数的，如果 this.props 里面有需要的属性我们就采用相应的属性，没有的话就用默认的属性。
>JSX 的表达式插入可以在标签属性上使用。所以其实可以把任何类型的数据作为组件(自定义组件标签)的参数，包括字符串、数字、对象、数组、甚至是函数等等。
>注意:`JSX语法中HTML结构中标签内{这里面不能放对象,没有任何意义}`

默认属性对象
 ` static defaultProps = {
    number:5
  }
`
静态属性是属于类的属性  Counter.propTypes
 用来较验父组件传进来的属性的类型，如果是number就正常执行，如果不是number就报警告
`static propTypes = {
    number:PropTypes.number
 }`
props 一旦传入，你就不可以在组件内部对它进行修改。但是你可以通过父组件主动重新渲染的方式来传入新的 props，从而达到更新的效果。
### 组件的状态(State)
1.组件的状态用来描述组件内部可以变化的数据
2.组件的状态是类组件所特有的
3.当类实例化的时候，会自动调用构造函数，我们可以在构造函数里初始化状态对象
> 渲染组件的过程
 1. 初始化属性对象,然后调用类的构造函数，并把属性对象传进去,得到组件的实例。
 2. 会调用实例的render方法得到返回React元素
 3. render方法会把此React元素转成真实的DOM元素并挂载到容器内部

状态与属性十分相似，但是状态是私有的，完全受控于当前组件。
`为了提高性能，React可能会把多个setState调用合并成一个`

#### 改变状态(setState)
`改变状态不能直接用this.state=xxx来改变`而是用`setState`
这个方法是由父类Component所提供的;
`当我们调用这个函数的时候，React.js 会更新组件的状态 state ，并且重新调用 render 方法，然后再把 render 方法所渲染的最新的内容显示到页面上`
这个函数接受的是对象参数或者函数参数;
在调用这个setState方法时候,不会马上修改state,会放到一个队列中,新的状态合并到state中,不能及时更新,用函数参数就可以解决
`注意setState的合并问题?`
在 React.js 内部会把 JavaScript 事件循环中的消息队列的同一个消息中的 setState 都进行合并以后再重新渲染组件。

### 组件的属性
1.组件的属性是由父组件传入的，状态的值是内部初始化
2.组件的属性不能修改，状态的值是可以修改的,是可以改变的
3.组件的值自己不能改，但是父组件可以改的
4.组件的状态是内部初始化的，只能组件内部修改,外部是不能修改。
注意:
1.组件的属性和状态都是当前组件数据源
2.组件是属性是可以自上而下流动的。单向数据流.只能父传子，不能子传父，也不会兄弟之间传

### 组件化
简单的说就是帮我们解决了前端结构的复用性问题,整个页面都可以由不同的组件组合,嵌套构成;

`大致的思路就是一个组件都有自己的显示形态(HTML结构和一些内容),而他是由数据状态(state)和配置参数(props)共同决定的,当数据变化时,组件显示也会更新;
组件化的模式就是为了降低代码的复杂性,提高可维护性;`
### 组件化的简单实现
定义一个父类组件Compoent,在其中定义一些方法(自定义的)
- `onStateChange`:这个方法是告知将插入新的元素,删除旧的元素;
- `setState`:接受一个参数对象,然后设置实例的状态(state),然后重新调用render方法,并且调用onStateChange方法,
- `_renderDOM`:这个方法会调用this.render构建DOM元素并且监听事件.子组件只需要实现render方法即可
- `count`:这个方法是把组件内的DOM元素插入到页面,并且在setState调用时候,及时更新页面
>子组件中的方法调用
>- `render`方法构建DOM元素;
>- `constructor`构造函数,在里面设置状态,并且用super(props),将props传给父类,利用this.props拿到配置参数
子组件只需要继承Component

#### 事件的监听
- 事件绑定
给JSX中普通的标签监听事件,React中封装了许多on*的属性,主要用用驼峰命名法.没有特殊处理,这些事件的监听只能在普通HTML标签上绑定,不能再自定义的组件标签上监听事件
- event对象
这里的事件对象与普通浏览器的事件对象包含的属性方法基本一样.只不过这里的event对象不是浏览器提供的而是由内部自己构建的.是React.js将浏览器的原生event进行封装而已
- 事件中的this
这里事件监听中的函数中this一般不能获取实例,所以一般用bind将实例方法绑定到当前实例上,在传给绑定的事件
```
 render () {
    return (
      <h1 onClick={this.handleClick.bind(this)}></h1>
    )
  }
```
#### React中ref属性
ref 属性来帮助我们获取已经挂载的元素的 DOM 节点，可以给某个 JSX 元素加上 ref属性

给 input 元素加了一个 ref 属性，这个属性值是一个函数。当 input 元素在页面上挂载完成以后，React.js 就会调用这个函数，并且把这个挂载以后的 DOM 节点传给这个函数。在函数中我们把这个 DOM 元素设置为组件实例的一个属性，这样以后我们就可以通过 this.input 获取到这个 DOM 元素
然后我们就可以在 componentDidMount 中使用这个 DOM 元素，并且调用 this.input.focus() 的 DOM API。整体就达到了页面加载完成就自动 focus 到输入框的功能
```
class AutoFocusInput extends Component {
  componentDidMount () {
    this.input.focus()
  }

  render () {
    return (
      <input ref={(input) => this.input = input} />
    )
  }
}

ReactDOM.render(
  <AutoFocusInput />,
  document.getElementById('root')
)
```
