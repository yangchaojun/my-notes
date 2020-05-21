### Context

- Context的定义：Context提供了一种方式，能够让数据在组件树中直接传递而不必一级一级手动传递
- Context的结构
  - <Provide>
  - <Consumer>
- Context的API: createContext() // 创建context实例

```js
// 使用实例
import React, { Component, createContext } from 'react'

const BatteryContext = createContext(90) // 当没有provider时取90做默认值
const onLineContext = createContext()

class Leaf extends Component {
	render() {
		return (
			<BatteryContext.Consumer>
				{(battery) => (
					<onLineContext.Consumer>
						{(online) => (
							<h1>
								Battery: {battery}, Online: {online.toString()}
							</h1>
						)}
					</onLineContext.Consumer>
				)}
			</BatteryContext.Consumer>
		)
	}
}

class Middle extends Component {
	render() {
		return <Leaf />
	}
}
class App extends Component {
	state = {
		battery: 60,
		online: false,
	}

	render() {
		const { battery, online } = this.state
		return (
			<BatteryContext.Provider value={battery}>
				<onLineContext.Provider value={online}>
					<button onClick={() => this.setState({ battery: battery - 1 })}>
						Press
					</button>
					<button onClick={() => this.setState({ online: !online })}>
						Press
					</button>
					<Middle />
				</onLineContext.Provider>
			</BatteryContext.Provider>
		)
	}
}

export default App

```



### ContextType

静态属性ContextType访问跨层级组件的数据

当组件只有一个context实例时，使用contextType作为子组件的静态属性可以简化Consumer的操作，但必须只有一个context实例。

```js
// 用法
import React, { Component, createContext } from 'react'

const BatteryContext = createContext(90)

class Leaf extends Component {
	static contextType = BatteryContext // 静态变量
	render() {
		const battery = this.context
		return <h1>Battery: {battery}</h1>
	}
}

class Middle extends Component {
	render() {
		return <Leaf />
	}
}
class App extends Component {
	state = {
		battery: 60,
	}

	render() {
		const { battery } = this.state
		return (
			<BatteryContext.Provider value={battery}>
				<button onClick={() => this.setState({ battery: battery - 1 })}>
					Press
				</button>
				<Middle />
			</BatteryContext.Provider>
		)
	}
}

export default App

```



### lazy

### Suspense

### memo