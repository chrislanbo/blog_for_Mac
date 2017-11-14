---
title: react-native生命周期
date: 2017-11-14 16:24:23
tags: [react-native, 生命周期]
categories: [react-native]
---
RN的组件是一个状态机。它接收两个输入参数：props和state，返回一个Virtual DOM。和Native一样，RN也为我们提供相应的钩子函数。RN的状态变化取决于props和state。

* 是组件第一次绘制阶段，如图中的上面虚线框内，在这里完成了组件的加载和初始化；
* 是组件在运行和交互阶段，如图中左下角虚线框，这个阶段组件可以处理用户交互，或者接收事件更新界面；
* 是组件卸载消亡的阶段，如图中右下角的虚线框中，这里做一些组件的清理工作。

![](http://7rf9ir.com1.z0.glb.clouddn.com/3-3-component-lifecycle.jpg)

#####初始化

`getDefaultProps`
在组件创建之前调用，多个实例间共享引用，这是全局调用一次，严格地来说，这不是组件的生命周期的一部分。在组件被创建并加载候，首先调用 getInitialState()，来初始化组件的状态。
>如果父组件传递过来的Props和你在该函数中定义的Props的key一样，将会被覆盖

`getInitalState`
组件示例创建的时候调用的第一个函数。主要用于初始化state。注意：为了在使用中不出现空值，建议初始化state的时候尽可能给每一个可能用到的值都赋一个初始值。

`componentWillMount`
这个函数调用时机是在组件创建，并初始化了状态之后，在第一次绘制 render() 之前。getInitalState之后，可以在这里改变state，做一些业务初始化操作，也可以设置组件状态。这个函数在整个生命周期中只被调用一次。

`render`
组件渲染函数，会返回一个Virtual DOM，只允许返回一个最外层容器组件。render函数尽量保持纯净，只渲染组件，不修改状态，不执行副操作（比如计时器）。

`componentDidMount`
在组件第一次绘制（render渲染）之后调用，通知组件已经加载完成。React会根据Virtual DOM来生成真实DOM，生成完毕后会调用该函数。在浏览器端（React），我们可以通过this.getDOMNode()来拿到相应的DOM节点。然而我们在RN中并用不到，在RN中主要在该函数中执行网络请求，定时器开启等相关操作。
这个函数调用的时候，其虚拟 DOM 已经构建完成，你可以在这个函数开始获取其中的元素或者子组件了。需要注意的是，RN 框架是先调用子组件的 componentDidMount()，然后调用父组件的函数。从这个函数开始，就可以和 JS 其他框架交互了，例如设置计时 setTimeout 或者 setInterval，或者发起网络请求。这个函数也是只被调用一次。这个函数之后，就进入了稳定运行状态，等待事件触发。


#####运行中

`componentWillReceiveProps`
如果组件收到新的属性（props），就会调用 componentWillReceiveProps()，其原型如下：

```javaScript
void componentWillReceiveProps(  
  object nextProps
)
```
输入参数 nextProps 是即将被设置的属性，旧的属性还是可以通过 this.props 来获取。在这个回调函数里面，你可以根据属性的变化，通过调用 this.setState() 来更新你的组件状态，这里调用更新状态是安全的，并不会触发额外的 render() 调用。props改变（父容器来更改或是redux），将会调用该函数。新的props将会作为参数传递进来，老的props可以根据this.props来获取。我们可以在该函数中对state作一些处理。注意：在该函数中更新state不会引起二次渲染。如下：

```java
componentWillReceiveProps: function(nextProps) {  
  this.setState({
    likesIncreasing: nextProps.likeCount > this.props.likeCount
  });
}
```

`shouldComponentUpdate`
当组件接收到新的属性和状态改变的话，都会触发调用 shouldComponentUpdate(...)，函数原型如下：

```java
boolean shouldComponentUpdate(object nextProps, object nextState)
```
输入参数 nextProps 和上面的 componentWillReceiveProps 函数一样，nextState 表示组件即将更新的状态值。这个函数的返回值决定是否需要更新组件，如果 true 表示需要更新，继续走后面的更新流程。否者，则不更新，直接进入等待状态。
默认情况下，这个函数永远返回 true 用来保证数据变化的时候 UI 能够同步更新。在大型项目中，你可以自己重载这个函数，通过检查变化前后属性和状态，来决定 UI 是否需要更新，能有效提高应用性能。该函数传递过来两个参数，新的state和新的props。state和props的改变都会调到该函数。该函数主要对传递过来的nextProps和nextState作判断。如果返回true则重新渲染，如果返回false则不重新渲染。在某些特定条件下，我们可以根据传递过来的props和state来选择更新或者不更新，从而提高效率。

`componentWillUpdate(object nextProps, object nextState)`
如果组件状态或者属性改变，并且上面的 shouldComponentUpdate(...) 返回为 true，就会开始准更新组件，并调用 componentWillUpdate()
输入参数与 shouldComponentUpdate 一样，在这个回调中，可以做一些在更新界面之前要做的事情。需要特别注意的是，在这个函数里面，你就不能使用 this.setState 来修改状态。这个函数调用之后，就会把 nextProps 和 nextState 分别设置到 this.props 和 this.state 中。紧接着这个函数，就会调用 render() 来更新界面了。

`componentDidUpdate(object prevProps,object prevState)`
调用了 render() 更新完成界面之后，会调用 componentDidUpdate() 来得到通知，
因为到这里已经完成了属性和状态的更新了，此函数的输入参数变成了 prevProps 和 prevState。和初始化时期的componentDidMount类似，在render之后，真实DOM生成之后调用该函数。传递过来的是当前的props和state。在该函数中同样可以使用this.getDOMNode()来拿到相应的DOM节点。如果你需要在运行中执行某些副操作，请在该函数中完成。

#####销毁
`componentWillUnmount`
当组件要被从界面上移除的时候，就会调用
在这个函数中，可以做一些组件相关的清理工作，例如取消计时器、网络请求等。


生命周期	调用次数	能否使用 setSate()
getDefaultProps	1(全局调用一次)	N
getInitialState	1	N
componentWillMount	1	Y
render	>=1	N
componentDidMount	1	Y
componentWillReceiveProps	>=0	Y
shouldComponentUpdate	>=0	N
componentWillUpdate	>=0	N
componentDidUpdate	>=0	N
componentWillUnmount	1	N


###props和state


上面讲完了生命周期，我们对props和state的不同点以及相同点作一个总结，加深大家理解。

相同点

1.不管是props还是state的改变，都会引发render的重新渲染。

2.都能由自身组件的相应初始化函数设定初始值。

不同点

1.初始值来源：state的初始值来自于自身的getInitalState（constructor）函数；props来自于父组件或者自身getDefaultProps（若key相同前者可覆盖后者）。

2.修改方式：state只能在自身组件中setState，不能由父组件修改；props只能由父组件修改，不能在自身组件修改。

3.对子组件：props是一个父组件传递给子组件的数据流，这个数据流可以一直传递到子孙组件；state代表的是一个组件内部自身的状态，只能在自身组件中存在。

