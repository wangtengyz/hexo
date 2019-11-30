---
title: redux基础分享
date: 2019-10-30 23:56:41
# categories: 技术分享 #文章分类目录 可以省略
tags: redux #文章标签 可以省略
description: redux初步认识 #你对本页的描述
---
### redux初识
目录
* 一、在后台管理项目开发中的思考
* 二、为什么会出现状态难以管理的问题
* 三、redux基本概念 
* 四、redux三大原则
* 五、redux组成部分：action、reducer、store
* 六、redux部分源码剖析
* 七、React-Redux使用
* 八、Redux DevTools工具使用

<!-- more -->
ppt内容地址：https://github.com/wangtengyz/ppt （拉下代码后直接点击redux.html打开即可）
ppt使用工具---reveal.js ：github地址：https://github.com/hakimel/reveal.js
demo地址：github地址：https://github.com/wangtengyz/reduxDemo


### 一、在后台管理项目开发中的思考 

当我们使用React开发web应用程序时，在React组件内，可以使用this.setState()和this.state处理或访问组件内状态，但是随着项目变大，状态变复杂，通常需要考虑组件间通信问题，主要包括以下两点：

某一个状态需要在多个组件间共享（访问，更新）；

某组件内交互需要触发其他组件的状态更新；

关于这些问题，React组件开发实践推荐将公用组件状态提升：

https://react.docschina.org/docs/lifting-state-up.html

```
Often, several components need to reflect the same changing data.  We recommend lifting the shared state up to their closest common ancestor
通常多组件需要处理同一状态，我们推荐将共享状态提升至他们的共同最近祖先组件内。
```

举个日常开发项目例子 

![举例](https://img.ikstatic.cn/MTU3MTg5NjM1MDg1NiM2MzIjZ2lm.gif "举例")

然后咱们看下文件目录 - 不便于维护和迭代

![举例](https://img.ikstatic.cn/MTU3MTg5NTE0NzQwNCM4MjAjcG5n.png "举例")

最后咱们再看看一个用了react-redux的项目开发目录对比一下

![举例](https://img.ikstatic.cn/MTU3NTEwMDg4MTkwMSM5MDQjcG5n.png "举例")

### 二、为什么会出现状态难以管理的问题
因为React就是一个简单的轻量级的视图层框架。React 主要的关注点是如何创建可用的视图层组件，对于组件之间的数据传递和状态管理并没有给出很好的解决方案。  
可能会有人有疑问，老铁！你是不是忘了context，

在React中使用Context并不是那么容易，

```
父组件提供Context需要通过childContextTypes进行“声明”

子组件使用父组件的Context属性需要通过contextTypes进行“申请”
```

React的Context是一种“带权限”的组件作用域， 用多了会有什么问题？

React App的组件是树状结构，一层一层延伸，父子组件是一对多的线性依赖。随意的使用Context其实会破坏这种依赖关系，导致组件之间一些不必要的额外依赖，降低组件的复用性，进而可能会影响到App的可维护性。

![举例](https://img.ikstatic.cn/MTU3MjMzMzQ3NzY1MSM1NDIjanBn.jpg "举例")
![举例](https://img.ikstatic.cn/MTU3MjMzMzQ4MDc5NiMxNTIjanBn.jpg "举例")

通过上图可以看到，原本线性依赖的组件树，由于子组件使用了父组件的Context，导致<Child />组件对<Node />和<App />都产生了依赖关系。一旦脱离了这两个组件，<Child />的可用性就无法保障了，减低了<Child />的复用性。


一切组件都是建立在树形结构下，咱们看下这些应用场景：

* 组件管理自己的state:简单

* 组件管理子组件的state:通过props，简单

* 组件管理子组件的子组件:通过props传递到子组件，子组件再通过props传递，复杂

* 一般组件管理子组件的子。。。。:通过props不断传递，复杂

* 一般组件传递状态给兄弟的子节点。。。

* 组件传递给父组件的父组件。。。

React管理不断变化的 state 非常困难，如果一个 model 的变化会引起另一个 model 变化，那么当 view 变化时，就可能引起对应 model 以及另一个 model 的变化，依次地，可能会引起另一个 view 的变化。直至你搞不清楚到底发生了什么。state 在什么时候，由于什么原因，如何变化已然不受控制当系统变得错综复杂的时候，想重现问题或者添加新功能就会变得举步维艰。

当数据和组件或者页面不和页面强关联时，那样你就不会觉的复杂了。

这就好比恋爱，要先认识女孩，再牵手，再接吻，最后你才能嗯...，成为男女朋友关系。这种形式肯定不符合一些现在人的快速生活的节奏，无聊了怎么办？所以就有了很多陪玩服务，陪聊服务的盛行等等，极大的方便了现代人的需求。

在开发中讲的就是效率，效率就需要把事情变简单，那只有React肯定不符合大型项目和快速开发。

需要视图层框架+数据层框架，两个相互结合，就可以更容易解决状态难以管理点的问题。

项目未使用redux和已使用的区别

![举例](https://img.ikstatic.cn/MTU3MTgwMTkzNjczMSM0NjkjanBn.jpg "举例")
    
状态管理解决方案之一-Redux

* Redux通过一系列约定的规范将修改应用状态的步骤标准化，让应用状态的管理不再错综复杂，而是如同一根长线般清晰
* Redux是目前React生态中，较（最）好的数据层框架。同类型的还有Mobx，Jumpsuit，Alt.js / dav等。
* Redux在react中使用还需加上React绑定库react-redux，食用更佳。
   

### 三、 redux基本概念 
Redux是一个用来管理管理数据状态和UI状态的JavaScript应用工具

Redux的思想继承自Facebook官方的Flux架构，但比Flux更加简洁易用

 简而言之，Redux就是降低管理难度的。（Redux支持React，Angular、jQuery甚至纯JavaScript）

redux官方图片- 工作流程

![举例](https://img.ikstatic.cn/MTU3MTk2NzMxNzY2NCM3MzgjanBn.jpg "举例")

图书馆版本

![举例](https://img.ikstatic.cn/MTU3MTk2OTUzMzQyMCM2OTQjanBn.jpg "举例")

### 四、redux三大原则
    1.唯一数据源

     Redux应用只维护一个全局的状态对象，存储在Redux的store中。唯一数据源是一种集中式管理应用状态的方式，便于监控任意时刻应用的状态和调试应用，减少出错的可能性。

    2.保持应用状态只读

    在任何时候都不能直接修改应用状态。当需要修改应用状态时，必须发送一个action，由这个action描述如何修改应用状态。这一看似烦琐的修改状态的方式实际上是Redux状态管理流程的核心，保证了在大型复杂应用中状态管理的有序进行。

    3.应用状态的改变通过纯函数完成

    action表明修改应用状态的意图，真正对应用状态做修改的是reducer。reducer必须是纯函数，所以reducer在接收到action时，不能直接修改原来的状态对象，而是要创建一个新的状态对象 返回。



### 五、redux主要组成部分：action、reducer、store
1. action

* action是Redux中信息的载体，是store唯一的信息来源

* 把action发送给store必须通过store的dispatch方法

* action是普通的JavaScript对象，但每个action必须有一个type属性描述action的类型，type一般被定义为字符串常量

* 除了type属性外，action的结构完全由自己决定，但应该确保action的结构能清晰地描述实际业务场景

* 一般通过action creator创建action，action  creator是返回action的函数

例如下面是一个新增待办事项的action creator

```
const addItem = text => {
    return {
        type: 'ADD_ITEM',
        text
    }
}
```

2. reducer

* action用于描述应用发生了什么操作，reducer则根据action做出响应，决定如何修改应用的状态state。

* 既然是修改state，那么就应该在编写reducer前设计好state。

* state既可以包含服务器端获取的数据，也可以包含UI状态。

* Reducers 指定了应用状态的变化如何响应 actions 并发送到 store 的，

* 记住 actions 只是描述了有事情发生了这一事实，并没有描述应用如何更新 state。

reducer是一个纯函数，它接 收两个参数，当前的state和action，返回新的state。reducer函数签名如下：

```
(previousState, action) => newState
```

我们先来创建一个最基本的reducer：

```
import { ADD_ITEM } from './action'
const defaultState = {
   list: ["早上6点跑步"]
};
export default function reducer (state = defaultState, action) {
    const { type,payload } = action;
    switch (type) {
        case ADD_ITEM:
        return  Object.assign({}, state, {
            list:[...payload]
        })   
        default:
            return state;
    }
};
```

3.store

store是Redux中的一个对象，也是action和reducer之间的桥梁。store主要负责以下几个工作：

* 保存应用状态

* 通过方法getState()访问应用状态

* 通过方法dispatch(action)发送更新状态的意图

* 通过方法subscribe(listener)注册监听函数、监听应用状态的改变

一个Redux应用中只有一个store，store保存了唯一数据源。

store通过createStore()函数创建，创建时需要传递reducer作为参数

创建应用的store的代码示例如下

```
import {createStore} from 'redux'
import reducer from './reducer'
// 创建store 并将reducer作为参数传入
const store = createStore(reducer)
export default store
```

通过简单demo1,demo2演示三者作用 github地址：https://github.com/wangtengyz/reduxDemo



总结下redux流程
1. 调用store.dispatch(action)。一个action是一个用于描述“发生了 什么”的对象。store.dispatch(action)可以在应用的任何地方调用，包括组 件、XHR 的回调，甚至在定时器中。

2. Redux的store调用reducer函数。store传递两个参数给reducer：当前应用的状态和action。reducer必须是一个纯函数，它的唯一职责是 计算下一个应用的状态。

3. 根reducer会把多个子reducer的返回结果组合成最终的应用状态。根reducer的构建形式完全取决于用户。Redux提供了combineReducers，方便把多个拆分的子reducer组合到一起，但完全可以不使用它。当使用combineReducers时，action会传递给每一个子reducer 处理，子reducer处理后的结果会合并成最终的应用状态。

4. Redux的store保存根reducer返回的完整应用状态。此时，应用状态才完成更新。如果UI需要根据应用状态进行更新，那么这就是更新UI的时机。对于React应用而言，可以在这个时候调用组件的setState方 法，根据新的应用状态更新UI。



### 六、redux部分源码剖析
redux源码目录结构

![举例](https://img.ikstatic.cn/MTU3MjI3NTQ0NjM0OCM3ODMjanBn.jpg "举例")

大家看目录结构，一共有 6 个文件，先说下各个文件大概功能

* utils 控制台输出警告、用于判断是否简单对象的函数、帮助开发者检查不要使用redux自带的action的类型

* applyMiddlewar.js 使用自定义的 middleware 来扩展 Redux

* bindActionCreators.js 把 action creators 转成拥有同名 keys 的对象,使用时可以直接调用

* combineReducers.js 一个比较大的应用，需要对 reducer 函数 进行拆分，拆分后的每一块独立负责管理 state 的一部分

* compose.js 从右到左来组合多个函数，函数编程中常用到

* createStore.js 创建一个 Redux Store 来放所有的state

* index.js是整个redux的入口文件

这次主要剖析下createStore.js文件

函数createStore接受三个参数（reducer、preloadedState、enhancer）

主要说一下enhancer，中文名叫增强器，顾名思义就是来增强redux的，实际上就是一个高阶函数，它的参数是创建store的函数（store creator），返回值是一个可以创建功能更加强大的store的函数(enhanced store creator)，这和React中的高阶组件的概念很相似。

它的类型的是Function，createStore.js里有这么一行代码：

```
if (typeof enhancer !== 'undefined') {
    if (typeof enhancer !== 'function') {
        throw new Error('Expected the enhancer to be a function.')
    }
    return enhancer(createStore)(reducer, preloadedState)
}
```

常见的enhancer就是redux-thunk以及redux-saga，一般都会配合applyMiddleware一起使用， 而applyMiddleware的作用就是将这些enhancer格式化成符合redux要求的enhancer，

import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';
const store = createStore(
rootReducer,
applyMiddleware(thunk)
);

疑问：createStore函数第二个参数不是preloadedState吗？redux其实做了这么一层转换

```
if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
    enhancer = preloadedState
    preloadedState = undefined
}
```

函数createStore执行完之后返回的对象都有什么？

```
return {
    dispatch,
    subscribe,
    getState,
    replaceReducer,
    [$$observable]: observable
}
```

他返回了有这么几个方法，其中前三个最为常用,下面主要剖析下这些

先看下文件里的常量声明

```
let currentReducer = reducer //从函数createStore第一个参数reducer获得
let currentState = preloadedState as S //从函数createStore第二个参数preloadedState获得
let currentListeners: (() => void)[] | null = [] //当前订阅者列表
let nextListeners = currentListeners // 新的订阅者列表
let isDispatching = false // 作为锁来用，同一个时间里,防止两个action同时触发reducer对同一数据的修改
```

1.dispatch方法

```
function dispatch(action) {
 // 判断action是否为简单对象(凡不是new Object()或者字面量的方式构建出 的对象都不是简单对象,该对象的__proto__等于Object.prototype)
    if (!isPlainObject(action)) {
            throw new Error(
                      'Actions must be plain objects. ' +
                        'Use custom middleware for async actions.'
                    )
                  }
                  // 判断action.type是否存在
                  if (typeof action.type === 'undefined') {
                    throw new Error(
                      'Actions may not have an undefined "type" property. ' +
                        'Have you misspelled a constant?'
                    )
                  }
                  // 判断当前是否有执行其他的reducer操作
                  if (isDispatching) {
                    throw new Error('Reducers may not dispatch actions.')
                  }
                  // 为啥要用try finally
                  // 执行前isDispatching设置为true，阻止后续的action进来触发reducer操作
                  // 得到的state值赋值给currentState，完成之后再finally里将isDispatching再改为false
                  // 允许后续的action进来触发reducer操作
                  // 接着一一通知订阅者做数据更新，不传入任何参数。最后返回当前的action
                  try {
                    isDispatching = true
                    currentState = currentReducer(currentState, action)
                  } finally {
                    isDispatching = false
                  }
                  // 函数dispatch里面将nextListeners和currentListeners引用了同一个数组
                  
                  const listeners = (currentListeners = nextListeners)
                  for (let i = 0; i < listeners.length; i++) {
                    const listener = listeners[i]
                    listener()
                  }
              
                  return action
                }
```

2.getState方法

```
function getState() {
    if (isDispatching) {
        throw new Error(
        'You may not call store.getState() while the reducer is     executing.  ' +
        'The reducer has already received the state as an argument. ' +
        'Pass it down from the top reducer instead of reading it from the store.'
        )
    }
    return currentState
}
```

getState相比较dispatch要简单许多,返回currentState即可， 这currentState在每次dispatch得时候都会得到响应的更新， 为了保证数据的一性，当在reducer操作的时候，是不可以读取当前的state值的；

咱们看了源码后，getState函数返回state的时候，并没有对currentState做一层拷贝再给我们，所以是可以直接修改的；

store通过getState得出的state是可以直接被更改的，但是redux不允许这么做，因为这样不会通知订阅者更新数据；

3.subscribe方法

```
function subscribe(listener) {
// 判断监听者是否为函数
if (typeof listener !== 'function') {
throw new Error('Expected the listener to be a function.')
}
// 是否有reducer正在进行数据修改（保证数据的一致性）
if (isDispatching) {
throw new Error(
'You may not call store.subscribe() while the reducer is executing. ' +
'If you would like to be notified after the store has been updated, subscribe from a ' +
'component and invoke store.getState() in the callback to access the latest state. ' +
'See https://redux.js.org/api-reference/store#subscribe(listener) for more details.'
)
}
let isSubscribed = true //表示该订阅者在订阅状态中，true-订阅中，false-取消订阅

ensureCanMutateNextListeners() // 判断nextListeners和currentListeners是否为同一个引用
// 当nextListeners和currentListeners为同一个引用时，则做一层浅拷贝,
// 为什么要拆开？
// 防止当redux在通知所有订阅者的时候，此时又有一个新的订阅者加进来，就会打乱原有的顺序
// function ensureCanMutateNextListeners() {
// if (nextListeners === currentListeners) {
// nextListeners = currentListeners.slice()
// }
//}
nextListeners.push(listener)
return function unsubscribe() {
// 是否已经取消订阅（已取消的不必执行）
if (!isSubscribed) {
return
}
// 是否有reducer正在进行数据修改（保证数据的一致性）
if (isDispatching) {
throw new Error(
'You may not unsubscribe from a store listener while the reducer is executing. ' +
'See https://redux.js.org/api-reference/store#subscribe(listener) for more details.'
)
}
isSubscribed = false
ensureCanMutateNextListeners()
const index = nextListeners.indexOf(listener)
nextListeners.splice(index, 1)
}
}
```



### 七、React-Redux使用
下载安装react-redux

npm install --save react-redux



react-redux仅有2个API，Provider和connect

1. Provider是顶层组件的作用，将store作为上下文提供给全局共享

Provider是一个提供器，只要使用了这个组件，组件里边的其它所有组件都可以使用store了，这也是React-redux的核心组件了

Provider 使组件层级中的 connect() 方法都能够获得 Redux store。正常情况下，你的根组件应该嵌套在 <Provider> 中才能使用 connect() 方法

```
ReactDOM.render(
    <Provider store={store}>
         <App />
    </Provider>,
    document.getElementById('root')
 )
```

2. connect方法做的事情是将state和dispatch绑定到Connect组件的参数上,Connect组件是局部组件，将某个react组件包装起来，传递指定的state和props给该组件访问

        connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])参数

* [mapStateToProps(state, [ownProps]): stateProps] (Function): 如定义该参数，组件将会监听 Redux store 的变化。任何时候，只要 Reduxstore 发生改变，mapStateToProps 函数就会被调用。该回调函数必须返回个纯对象，这个对象会与组件的 props 合并。如果你省略了这个参数，你的件将不会监听 Redux store。如果指定了该回调函数中的第二个参数ownProps，则该参数的值为传递到组件的 props，而且只要组件接收到新的props，mapStateToProps 也会被调用。
 * [mapDispatchToProps(dispatch, [ownProps]): dispatchProps] (Object or Function): 如果传递的是一个对象，那么每个定义在该对象的函数都将被当作 Redux action creator，对象所定义的方法名将作为属性名；每个方法将返回一个新的函数，函数中dispatch方法会将action creator的返回值作为参数执行。这些属性会被合并到组件的 props 中。如果你省略这个 mapDispatchToProps 参数，默认情况下，dispatch 会注入到你的组件 props 中。
* [mergeProps(stateProps, dispatchProps, ownProps): props] (Function): 如果指定了这个参数，mapStateToProps() 与 mapDispatchToProps() 的执行结果和组件自身的 props 将传入到这个回调函数中。该回调函数返回的对象将作为 props 传递到被包装的组件中。你也许可以用这个回调函数，根据组件的 props 来筛选部分的 state 数据，或者把 props 中的某个特定变量与 action creator 绑定在一起。如果你省略这个参数，默认情况下返回 Object.assign({}, ownProps, stateProps, dispatchProps) 的结果。

* [options] (Object) 如果指定这个参数，可以定制 connector 的行为。
[pure = true] (Boolean): 如果为 true，connector 将执行 shouldComponentUpdate 并且浅对比 mergeProps 的结果，避免不必要的更新，前提是当前组件是一个“纯”组件，它不依赖于任何的输入或 state 而只依赖于 props 和 Redux store 的 state。默认值为 true。
[withRef = false] (Boolean): 如果为 true，connector 会保存一个对被包装组件实例的引用，该引用通过 getWrappedInstance() 方法获得。默认值为 false。

简单demo3示例

connect是把UI组件（无状态组件）和业务逻辑代码的分开，然后通过connect再链接到一起，让代码更加清晰和易于维护。

八、Redux DevTools工具使用

第一步 科学上网下载Redux DevTools插件在浏览器上

第二步 按照示例位置输入window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__())

```
import {createStore} from 'redux'
import reducer from './reducer'
// 创建store 并将reducer作为参数传入
const store = createStore(reducer,
// 使用redux—tool工具
window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__())
export default store
```

第三步 按F12打开浏览器尽情食用
![举例](https://img.ikstatic.cn/MTU3MjMyMjMwOTYyNyMyOTMjZ2lm.gif "举例")




