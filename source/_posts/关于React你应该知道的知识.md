---
title: React知识点系列
# categories: 必须写
tags: React
description: React
date: 2020-01-05 22:21:12
---

### 目录：

一、 jsx语法你了解多少？  

二、 React组件和元素的概念  

三、 高阶组件的使用

四、 React Router用于构建单页面应用的解决方案

五、 React组件的性能优化

****
<!-- more -->

## 一、jsx语法你了解多少？
1.JSX语法由来  

   * 大背景： UI和数据分离一直是前端领域的一个重要关注点，前端领域发明了模板，将`UI的定义放入模板文件`，将`数据的逻辑维护在JS代码`中，然后通过模板引擎，根据数据和模板文件渲染出最终的HTML文件或代码片段。  

   * 小背景： React致力于通过组件的概念将页面进行拆分并实现组件复用。React 认为，一个组件应该是具备UI描述和UI数据的完整体，不应该将它们分开处理，于是发明了JSX，作为`UI描述和UI数据之间的桥梁`。

2.JSX本质

    * JSX 是一种用于描述UI的JavaScript扩展法，React使用这种语法描述组件的UI。本质上仍然是JavaScript。

3.JSX不是必需的

    * JSX语法对使用React来说并不是必需的，实际上，JSX语法只是React.createElement (component, props, ...children)的语法糖，所有的JSX语法最终都会被转换成对这个方法的调用。
```
//JSX语法  
const element = <div className='foo'>Hello,React</div>

//转换后
const element = React.createElement('div', {className:'foo'}, 'Hello, React')
```

## 二、 React组件和元素的概念 
1.组件可以是类或者函数，他可以有state并将 props 作为输入然后返回一个元素的树形结构作为输出。



```
// Button是一个React组件
class Button extends React.Component {
    render() {
        return (
            <div className='foo'>
                <button className='blue'>
                   OK
                </button>
            </div>
        );
    }
}
```
2.元素是用来展现 DOM 节点或组件的一个普通 JavaScript 对象。元素是一个纯的，不需要进行状态变化且创建成本较低的对象。`并且元素也不是实际的组件实例`。这个对象通过DOM节点或React组件描述界面是什么样子的。你不能在元素上调用任何方法。它只是一个带有两个字段的不可变描述对象：type: (string | ReactClass) && props: Object1。JSX语法就是用来创建React元素的。
```
上面的JSX代码会创建下面的React元素：
{
    type: 'div',
    props: {
        className: 'foo',
        children: {
            type: 'button',
            props: {
                className: 'blue',
                children: 'OK'
            }
        }
    }
}
```

## 高阶组件

1.概念： 高阶组件（简称HOC）接收React组件作为参数，并且返回一个新的React组件。主要用来实现组件逻辑的抽象和复用。

2.高阶组件的函数形式如下：  
```
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```
举个例子,有一个组件MyComponent，需要从LocalStorage中获取数据，然后渲染到界面
```
import React, { Component } from 'react'
class MyComponent extends Component {
    componentWillMount() {
        let data = localStorage.getItem('data');
        this.setState({data});
    }
    render() {
        return <div>{this.state.data}</div>
    }
}
```
假如当其他组件也需要从LocalStorage中获取同样的数据展示出来，使用高阶组件改写这部分代码
```
import React, { Component } from 'react'
function withPersistentData(WrappedComponent) {
    return class extends Component {
        componentWillMount() {
            let data = localStorage.getItem('data');
            this.setState({data});
        }
        render() {
        // 通过{...this.props} 把传递给当前组件的属性继续传递给被包装的组件
            return <WrappedComponent data={this.state.data} {...this.props} />
        }
    }
}

class MyComponent extends Component {
    render() {
        return <div>{this.props.data}</div>
    }
}
const MyComponentWithPersistentData = withPersistentData(MyComponent)

```
通过这个例子可以看出高阶组件的主要功能是封装并分离组件的通用逻辑，让通用逻辑在组件间更好地被复用。高阶组件的这种 实现方式本质上是装饰者设计模式

3.高阶组件的使用场景主要有以下4种  
（1）操纵props  
在被包装组件接收props前，高阶组件可以先拦截到props，对props执行增加、删除或修改的操作，然后将处理后的props再传递给被包装组件。刚才的例子就属于这种情况，高阶组件为WrappedComponent增加了一个data属性

（2）通过ref访问组件实例  
高阶组件通过ref获取被包装组件实例的引用，然后高阶组件就具备了直接操作被包装组件的属性或方法的能力。
```
function withRef(wrappedComponent) {
    return class extends React.Component {
        constructor(props) {
            super(props);
            this.someMethod = this.someMethod.bind(this);
        }
        someMethod() {
            this.wrappedInstance.someMethodInWrappedComponent();
        }
        render() {
        //为被包装组件添加ref属性，从而获取该组件实例并赋值给this.wrappedInstance
            return <WrappedComponent 
            ref={(instance) =>{this.wrappedInstance = instance}} 
            {...this.props} />
        }
    }
}
```
当WrappedComponent被渲染时，执行ref的回调函数，高阶组件通过this.wrappedInstance保存WrappedComponent实例的引用，在someMethod中，通过this.wrappedInstance调用WrappedComponent中的方法。这种用法在实际项目中很少会被用到，但当高阶组件封装的复用逻辑需要被包装组件的方法或属性的协同支持时，这种用法就有了用武之地。

（3）组件状态提升  
高阶组件可以通过将被包装组件的状态及相应的状态处理方法提升到高阶组件自身内部实现被包装组件的无状态化。一个典型的场景是，利用高阶组件将原本受控组件需要自己维护的状态统一提升到高阶组件中。


>function withControlledState(WrappedComponent) {
>    return class extends React.Component {
>        constructor(props) {
>            super(props);
>            this.state = {
>                value: ''
>            };
>        this.handleValueChange = this.handleValueChange.bind(this);
>        }
>        handleValueChange(event) {
>            this.setState({
>                value: event.target.value
>            });
>        }
>        render() {
>        // newProps 保存受控组件需要使用的属性和事件处理函数
>        const newProps = {
>            controlledProps: {
>                value: this.state.value,
>                onChange: this.handleValueChange
>            }
>        };
>        return <WrappedComponent {...this.props} {...newProps}/>
>        }
>    }
>}

这个例子把受控组件value属性用到的状态和处理value变化的回调函数都提升到高阶组件中，当我们再使用受控组件时，就可以这样使用:

```
class SimpleControlledComponent extends React.Component {
    render() {
        //此时的SimpleControlledComponent为无状态组件，状态由高阶组件维护
        return <input name="simple"
        {...this.props.controlledProps }/>
    }
}

const ComponentWithControlledState = withControlledState
(SimpleControlledComponent);
``` 

（4）用其他元素包装组件

我们还可以在高阶组件渲染WrappedComponent时添加额外的元素，这种情况通常用于为WrappedComponent增加布局或修改样式。


>function withRedBackground(WrappedComponent) {
>    return class extends React.Component {
>        render() {
>            return (
>                <div style={{backgroundColor: 'red'}}>
>                    <WrappedComponent {...this.props}/>
>                </div>
>            )
>        }
>    }
>}


4.高阶组件的参数传递  
```
HOC(...params)(WrappedComponent)
```
例如react-redux中的connect函数就是一个典型的例子。connect的简化定义如下：
```
connect(mapStateToProps, mapDispatchToProps)(WrappedComponent)
```
拆开来看
```
// connect 是一个函数，返回值enhance也是一个函数
const enhance = connect(mapStateToProps,mapDispatchToProps);
// enhance是一个高阶组件
const ConnectedComponentA = enhance(ComponentA);
```
高阶组件非常容易组合起来使用，因为当多个函数的输出和它的输入类型相同时，这些函数很容易组合到一起使用。例如，有f、g、h三个高阶组件，都只接收一个组件作为参数，于是我们可以很方便地嵌套使用它们：f( g( h(WrappedComponent) ) )

`调用compose(f, g, h)等价于 (...args) => f(g(h(...args)))。用compose函数可以把高阶组件嵌套的写法打平：`

## 四、React Router用于构建单页面应用的解决方案
1.基本知识  

(1).多页面应用和后端路由  
在传统的Web应用中，浏览器根据地址栏的URL向服务器发送一个
HTTP请求，服务器根据URL返回一个HTML页面。这种情况下，一个
URL对应一个HTML页面，一个Web应用包含很多HTML页面，这样的
应用就是`多页面应用`；在多页面应用中，页面路由的控制由服务器端负
责，这种路由方式称为`后端路由`。  

(2).单页面应用和前端路由  
URL的变化可以引起页面内容的变化，但不会向服务器发送新的请求，但这
只是逻辑上的多页面，实际上无论URL如何变化，对应的HTML文件都是同一个，就是`单页面应用`。“逻辑页面”（这个名称用来和真实的HTML页面区分）的路由只能由前端负责，这种路由方式称为`前端路由`。

2.路由器  

React Router 通过Router和Route两个组件完成路由功能。Router可
以理解成路由器，一个应用中只需要一个Router实例，所有的路由配置
组件Route都定义为Router的子组件。在Web应用中，我们一般会使用对
Router进行包装的BrowserRouter或HashRouter两个组件。  
BrowserRouter使用HTML 5的history API（pushState、replaceState等）实现应用的UI和URL的同步。  
HashRouter使用URL的hash实现应用的UI和URL的同步。  
http://example.com/some/path  
HashRouter创建的URL形式如下：  
http://example.com/#/some/path  

使用BrowserRouter时，一般还需要对服务器进行配置，让服务器能正确地处理所有可能的URL。例如，当浏览器发送http://example.com/some/pathhttp://example.com/some/path2两个请求时，服务器需要能返回正确的HTML页面（也就是单页面应用中唯一的
HTML页面）。  

使用HashRouter则不存在这个问题，因为hash部分的内
容会被服务器自动忽略，真正有效的信息是hash前面的部分，而对于单
页面应用来说，这部分内容是固定的。
Router会创建一个history对象，history用来跟踪URL，当URL发生
变化时，Router的后代组件会重新渲染。  
  
  React Router中提供的其他组件
可以通过context获取history对象，这也隐含说明了React Router中的其他组件必须作为Router组件的后代组件使用。

## 五、 React组件的性能优化
1.React-Redux的shouldComponentUpdata实现  
使用react-redux，一个典型的React组件代码文件最后一个语句代码是这样:
```
export default connect(mapStateToProps, mapDispatchToProps)(Foo)
```
虽然代码不可见，但是connect的过程中实际产生了一个无名的React组件类，这个类定制了shouldComponentUpdate函数的实现，实现逻辑是比对这次传递给内层视图组件的props和上一次的props，如果没有变化，shouldComponentUpdate就返回false，则不会再次渲染，否则就返回true。

需要特别注意shouldComponentUpdate函数做的是`浅比较`，所有不要在JSX里面写匿名函数或者对象
比如 JSX中使用组件Foo的时候给名为style的prop赋值，代码如下

><Foo style={{color: 'red'}} />

像这种使用方法，Foo组件利用react-redux提供的shouldComponentUpdata函数实现，每次render由于{color： 'red'}的对象引用值不一样，认为style这个props发生变化（实际里面的值是没有变化），导致每次都会触发渲染。

总之想让react-redux认为前后对象类型props是相同的，就必须保证props是指向同一个JavaScript对象。  

上面使用Foo组件的例子应该改进这样：
```
const footStyle = {color：'red'}; // 确保这个初始化只执行一次，不要放在render中
<Foo style={footStyle}>
```

2.给多个实例组件添加key值，key值要求唯一，并且稳定不变，切记不要将元素在数组中的下标值作为key，在数组发生变化的时候，同样一个组件实例在不同的更新过程中数组的下标完全可能不同，这会让React彻底乱套。

3.用reselect提高数据获取性能  

React和Redux都是通过数据驱动渲染过程，除了优化渲染过程，获取数据也是可以优化的点。

比如通过mapStateToProps调用selectVisibleTodos函数，从Redux Store提供的state中产生渲染需要的数据，代码如下：

```
const selectVisibleTodos = (todos,  filter) => {
    switch (filter) {
        case FilterTypes.ALL:
            return  todos;
        case FilterTypes.COMPLETED:
            return  todcs.filter(item => item.completed);
        case FilterTypes.UNCOMPPLETED:
            return  todos.filter(item => !item.completed);
        default :
            throw  new  Error  (’ unsupported  filter ’);
    }
}    

const mapStateToProps = (state) => {
    return {
        todos: selectVisibleTodos(state.todos, state.filter)
    };
}
```
当前显示的todos是要根据React Store状态树上的todos和filter两个字段的值计算出来，但是计算过程需要遍历todos字段上的数组，，当数组特别大时，相关组件每一次渲染时都要重新计算一遍，就会显得负担过重。

reselect库工作原理：只要相关状态没有发生改变，那就直接使用上一次的缓存结果。 
reselect认为一个`选择器`函数的工作可以分为两部分，把一个计算过程分为两个步骤：

第一步骤: 从输入参数state抽取第一层结果，和之前的结果进行比较，如果发现相同，就没必要就没必要进行第二步运算，直接把之前的第二步运算结果返回出去；  
第二步骤： 根据第一层结果计算出选择器需要返回的最终结果。  

显然，每次选择器函数被调用时，步骤一都会被执行，但步骤一的结果用力啊判断是否可以使用缓存的结果，所有并不是每次都会调用步骤二的运算。  

reselect提供了创造选择器的createSelector函数，这是一个高阶函数。

第一个参数是一个函数数组，每个元素代表了选择器一需要做的映射计算  
第二个参数代表步骤二的计算过程

使用release需要安装对应的npm包
npm install --save reselect
例如：
```
import { createSelector } from 'reselect'
import { FilterTypes } from '../constants.js'

export const selectVisibleTodos = createSelector(
    [getFileter, getTodos],
    (filter, todos) => {
        switch (filter) {
            case FilterTypes.All;
                return todos;
            case FilterTypes.COM;
                return todos.filter(item => item.completed);
            case FilterTypes.UNCOM;
                return todos.filter(item => !item.completed);
            default:
                throw new Error('error filter')    

        }
    }
)

const getFilter = (state) => state.filter;
const getTodos = (state) => state.todos;

```  

用新定义的选择器来重新mapStateToPros：
```
import { selectVisibleTodos } from '../selector.js
const mapStateToProps = (state) => {
    return {
        todos: selectVisibleTodos(state)
    }
}
```
只要React Store状态树上的filter和todos字段不变，无论怎么触发组件的渲染，都不会引发没有必要的遍历todos字段的计算，性能自然有所提升。