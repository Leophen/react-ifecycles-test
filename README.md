# react-ifecycles-test

测试 react 生命周期执行顺序的 demo

下面根据父子组件 props 改变、父组件卸载、重新挂载子组件，子组件改变自身状态 state 这几个操作步骤，对其生命周期的执行顺序进行分析：

## 一、组件代码

父组件 Parent.js：

```js
import React, { Component } from 'react'
import { Button } from 'antd'
import Child from './child'

const parentStyle = {
  padding: 40,
  margin: 20,
  backgroundColor: 'LightCyan'
}

const NAME = 'Parent 组件：'

export default class Parent extends Component {
  constructor() {
    super()
    console.log(NAME, 'constructor')
    this.state = {
      count: 0,
      mountChild: true
    }
  }

  static getDerivedStateFromProps(nextProps, prevState) {
    console.log(NAME, 'getDerivedStateFromProps')
    return null
  }

  componentDidMount() {
    console.log(NAME, 'componentDidMount')
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log(NAME, 'shouldComponentUpdate')
    return true
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log(NAME, 'getSnapshotBeforeUpdate')
    return null
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log(NAME, 'componentDidUpdate')
  }

  componentWillUnmount() {
    console.log(NAME, 'componentWillUnmount')
  }

  /**
   * 修改传给子组件属性 count 的方法
   */
  changeNum = () => {
    let { count } = this.state
    this.setState({
      count: ++count
    })
  }

  /**
   * 切换子组件挂载和卸载的方法
   */
  toggleMountChild = () => {
    const { mountChild } = this.state
    this.setState({
      mountChild: !mountChild
    })
  }

  render() {
    console.log(NAME, 'render')
    const { count, mountChild } = this.state
    return (
      <div style={parentStyle}>
        <div>
          <h3>父组件</h3>
          <Button onClick={this.changeNum}>改变传给子组件的属性 count</Button>
          <br />
          <br />
          <Button onClick={this.toggleMountChild}>卸载 / 挂载子组件</Button>
        </div>
        {mountChild ? <Child count={count} /> : null}
      </div>
    )
  }
}
```

子组件 Child.js：

```js
import React, { Component } from 'react'
import { Button } from 'antd'

const childStyle = {
  padding: 20,
  margin: 20,
  backgroundColor: 'LightSkyBlue'
}

const NAME = 'Child 组件：'

export default class Child extends Component {
  constructor() {
    super()
    console.log(NAME, 'constructor')
    this.state = {
      counter: 0
    }
  }

  static getDerivedStateFromProps(nextProps, prevState) {
    console.log(NAME, 'getDerivedStateFromProps')
    return null
  }

  componentDidMount() {
    console.log(NAME, 'componentDidMount')
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log(NAME, 'shouldComponentUpdate')
    return true
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log(NAME, 'getSnapshotBeforeUpdate')
    return null
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log(NAME, 'componentDidUpdate')
  }

  componentWillUnmount() {
    console.log(NAME, 'componentWillUnmount')
  }

  changeCounter = () => {
    let { counter } = this.state
    this.setState({
      counter: ++counter
    })
  }

  render() {
    console.log(NAME, 'render')
    const { count } = this.props
    const { counter } = this.state
    return (
      <div style={childStyle}>
        <h3>子组件</h3>
        <p>父组件传过来的属性 count：{count}</p>
        <p>子组件自身状态 counter：{counter}</p>
        <Button onClick={this.changeCounter}>改变自身状态 counter</Button>
      </div>
    )
  }
}
```

## 二、组件初始化

父子组件第一次进行渲染加载时，控制台的打印顺序为：

- Parent 组件：*constructor()*
- Parent 组件：*getDerivedStateFromProps()*
- Parent 组件：*render()*
- Child 组件：*constructor()*
- Child 组件：*getDerivedStateFromProps()*
- Child 组件：*render()*
- Child 组件：*componentDidMount()*
- Parent 组件：*componentDidMount()*

## 三、子组件修改自身状态 state

点击子组件 [改变自身状态counter] 按钮，其 [自身状态counter] 值会 +1, 此时控制台的打印顺序为：

- Child 组件：*getDerivedStateFromProps()*
- Child 组件：*shouldComponentUpdate()*
- Child 组件：*render()*
- Child 组件：*getSnapshotBeforeUpdate()*
- Child 组件：*componentDidUpdate()*

## 四、修改父组件传入的 props

点击父组件中的 [改变传给子组件的属性 count] 按钮，则界面上 [父组件传过来的属性 count] 的值会 + 1，控制台的打印顺序为：

- Parent 组件：*getDerivedStateFromProps()*
- Parent 组件：*shouldComponentUpdate()*
- Parent 组件：*render()*
- Child 组件：*getDerivedStateFromProps()*
- Child 组件：*shouldComponentUpdate()*
- Child 组件：*render()*
- Child 组件：*getSnapshotBeforeUpdate()*
- Parent 组件：*getSnapshotBeforeUpdate()*
- Child 组件：*componentDidUpdate()*
- Parent 组件：*componentDidUpdate()*

## 五、卸载子组件

点击父组件中的 [卸载 / 挂载子组件] 按钮，则界面上子组件会消失，控制台的打印顺序为：

- Parent 组件：*getDerivedStateFromProps()*
- Parent 组件：*shouldComponentUpdate()*
- Parent 组件：*render()*
- Parent 组件：*getSnapshotBeforeUpdate()*
- Child 组件：*componentWillUnmount()*
- Parent 组件：*componentDidUpdate()*

## 六、重新挂载子组件

再次点击父组件中的 [卸载 / 挂载子组件] 按钮，则界面上子组件会重新渲染出来，控制台的打印顺序为：

- Parent 组件：*getDerivedStateFromProps()*
- Parent 组件：*shouldComponentUpdate()*
- Parent 组件：*render()*
- Child 组件：*constructor()*
- Child 组件：*getDerivedStateFromProps()*
- Child 组件：*render()*
- Parent 组件：*getSnapshotBeforeUpdate()*
- Child 组件：*componentDidMount()*
- Parent 组件：*componentDidUpdate()*