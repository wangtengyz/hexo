---
title: 关于React你应该知道的知识点系列
# categories: 必须写
tags: React
description: React
date: 2020-01-05 22:21:12
---
### 目录：

### 一、 jsx语法你了解多少？

### 二、 React组件和元素的概念

### 三、 深入理解组件

****
<!-- more -->
## 一、jsx语法你了解多少？
1. JSX语法由来  

   * 大背景： UI和数据分离一直是前端领域的一个重要关注点，前端领域发明了模板，将`UI的定义放入模板文件`，将`数据的逻辑维护在JS代码`中，然后通过模板引擎，根据数据和模板文件渲染出最终的HTML文件或代码片段。  

   * 小背景： React致力于通过组件的概念将页面进行拆分并实现组件复用。React 认为，一个组件应该是具备UI描述和UI数据的完整体，不应该将它们分开处理，于是发明了JSX，作为`UI描述和UI数据之间的桥梁`。

2. JSX本质

    * JSX 是一种用于描述UI的JavaScript扩展法，React使用这种语法描述组件的UI。本质上仍然是JavaScript。

3. JSX不是必需的

    * JSX语法对使用React来说并不是必需的，实际上，JSX语法只是React.createElement (component, props, ...children)的语法糖，所有的JSX语法最终都会被转换成对这个方法的调用。
```
//JSX语法  
const element = <div className='foo'>Hello,React</div>

//转换后
const element = React.createElement('div', {className:
'foo'}, 'Hello, React')
```

## 二、 React组件和元素的概念 
1. 组件可以是类或者函数，他可以有state并将 props 作为输入然后返回一个元素的树形结构作为输出。

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
2. 元素是用来展现 DOM 节点或组件的一个普通 JavaScript 对象。元素是一个纯的，不需要进行状态变化且创建成本较低的对象。`并且元素也不是实际的组件实例`。这个对象通过DOM节点或React组件描述界面是什么样子的。你不能在元素上调用任何方法。它只是一个带有两个字段的不可变描述对象：type: (string | ReactClass) && props: Object1。JSX语法就是用来创建React元素的。
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