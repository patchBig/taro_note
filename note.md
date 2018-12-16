Taro 在编译时提供了一些内置的环境变量帮助用户做一些特殊处理。

#### process.env.TARO_ENV

用于判断当前编译类型，目前有 weapp/swan/h5/rn 四个取值，可以通过这个变量来书写对应不同环境下的代码。

#### 微信小程序原生作用域获取

在 Taro 的页面和组件类中， this 指向的是 Taro 页面或组件的实例：

```
import Taro, { Component } from '@tarojs/taro'
import { View } from '@taro/components'

export default class Menu extends Component {
	static defaultProps = {
		data: []
	}

	constructor(props) {
		super(props)
		this.state = {
			checked: props.checked
		}
	}

	componentWillMount () {
		console.log(this) 	// this => 组件　 Menu 的实例
	}

	render () {
		return <View />
	}
}
```
一般获取 Taro 的页面和组件所对应的小程序原生页面和组件的实例，可以通过 **this.$scope** 访问到他们。

#### 组件的外部样式和全局样式

- 组件和引用组件的页面中使用后代选择器 (.a .b) 在一些极端情况下，会有非预期的表现。
- 继承样式，如 font、color，会从组件外（父组件）继承到组件内，但是引用组件时在组件节点上书写的 className 无效

```
\#a { } 	/* id 选择器 组件中不能使用 */
[a] { } 	 	/*  属性选择器 组件中不能使用 */
button { } 	/*  标签名选择器 组件中不能使用 */
.a > .b { } 	/* 除非 .a 是 view 组件节点，否则不一定会生效 */
```

##### 外部样式类

如果想传递样式给应用的自定义组件，以下写法（直接传递 className）不可行：

```
/* CustomComp.js */
export default CustomComp extends Component {
	static defaultProps = {
		className: ''
	}
	render () {
		return <View className={this.props.className}>样式不会由组件外的 class 决定</View>
	}
}

/* MyPage.js */

export default MyPage extends Component {
	render () {
		return <CustomComp className='red-text' />
	}
}
```

需要利用 externalClasses 定义若干个外部样式类。（从小程序基础库版本 1.9.90 开始支持）

```
/* CustomComp.js */
export default CustomComp extends Component {
	static externalClasses = ['my-class']

	render() {
		return <View className='my-class'>这段样式由外部决定</View>
	}
}

/* Mypage.js */
export default MyPage extends Component {
	render () {
		return <CustomComp my-class='red-text' />
	}
}
```

> externalClasses 需要使用 短横线命名法（kebab-case），而不是 React 惯用的 驼峰命名法（camelCase)。否则无效。

#### 全局样式类

使用外部演示类可以让组件使用指定的组件外样式类，如果希望组件外样式类能够完全影响组件内部，可以将组件构造器中的 options.addGlobalClass 字段置为 true。这个特性从小程序基础库版本 2.2.3 开始支持。

```
export default CustomComp extends Component {
	static options = {
		addGlobalClass: true
	}

	render () {
		return <View className="red-text">这段文本的颜色由组件外的 Class 决定 </View>
	}
}
```

##### 关于 JSX 支持程度

- 不能在包含 JSX 元素的 map 循环中使用 if 表达式
- 不能使用 Array#map 之外的方法操作 JSX 数组
- 不能在 JSX 参数中使用匿名函数
- 暂不支持在render() 之外的方法定义 JSX
- 不允许在 JSX 参数(props)中传入 JSX 元素
- 不能在 JSX 参数中使用对象展开符
- 不支持无状态组件

在微信小程序端的自定义组件中，只有在 properties 中指定的属性才能在福组件传入并接收

```
Component({
	properties: {
		myProperty: {
			type: String, // 必填
			value: '',	// 可选
			observer: function(newVal, oldVal, changedPath){
				
			}
		},
		myProperty2: String // 简化定义方式
	}
})
```

可能会有某一属性没有使用而是直接传递给子组件的情况，这种情况是编译时无论如何也处理不了的，这时候需要在编码的时候给组件设置 defaultProps 来解决了。
里面所有的属性都会被设置到 properties 中初始化组件，正确设置 defaultProps 可以避免很多异常的情况的出现。

#### 组件传递属性名以 on 开头

在 Taro 中，父组件要往子组件传递函数，属性名必须以 on 开头

```
class Parent extends Component {
	handleEvent () {

	}
	render() {
		return (
			<Custom onTrigger={this,handleEvent}></Custom>
		)
	}
}
```

小程序端不要在组件中打印传入的函数；可以通过 this.props.onXXX && this.props.onXXX() 这种判断函数是否传入来进行调用的写法是完全支持的。

#### 小程序中页面生命周期 componentWillMount 不一致的问题

由于微信小程序里页面在 onLoad 时才能拿到页面的路由参数，而页面 onLoad 前组件已经 attached 了。因此页面的 componentWillMount 可能会与预期不太一致。

```
// 错误写法
render () {
	// 在 willMount 之前无法拿到路由参数
	const abc = this.$router.params.abc
	return <Custom abc={abc} />
}

// 正确写法
componentWillMount () {
	const abc = this.$router.params.abc
	this.setState({
		abc
	})
}
render() {
	// 增加一个兼容判断
	return this.state.abc && <Custom abc={abc} />
}
```

对于不需要等到页面 willMount 之后取路由参数的页面则没有任何影响

#### 组件的 constructor 与 render 提前调用

在 Taro 编译到小程序端后，组件的 constructor 与 render 默认会多调用一次。

这是因为 Taro 组件编译后就是小程序的自定义组件，而小程序的自定义组件的初始化时是可以指定 data 来让组件拥有初始化数据的，开发者一般会在组件的 constructor 中设置一些初始化的 state，同时也会在 render 中处理 state 和 props 产生的新的数据，在 Trao 中多出来的这一次提前调用，就是为了收集组件的初始化数据，而自定义组件体验生成 data，以保证组件初始化时能带有数据，让组件初次渲染正常

#### JSX 编码必须用 单引号

如果出现双引号可能会导致编译错误

##### 预加载

在微信小程序中，从调用 Taro.navigateTo、Taro.redirectTo 或 Taro.switchTab 后，到页面触发 componentWillMount 会有一定延时，因此一些网络请求可以提前到发起跳转前一刻去请求。

Taro 提供了 componentWillPreload 钩子，他接收页面跳转的参数作为参数，可以把需要预加载的内容通过 return 返回，然后在组件触发 componentWillMount 后即可通过 this.$preloadData 获取到预加载的内容。

```
class Index extends Component {
	componentWillMount () {
		console.log('isFetching: ', this.isFetching)
		this.$preloadData
			.then(res => {
				console.log('res: ', res);
				this.isFetching = false
			})
	}

	componentWillPreload (params) {
		return this.fetchData(params.url)
	}

	fetchData() {
		this.isFetching = true;
		...
	}
}
```

#### 全局变量

在 Taro 中推荐使用 Redux 来进行全局变量的管理。
使用全局变量：

1. 自行命名一个 JS 文件，例如 global_data.js

```
const globalData = {}

export function set(key, val) {
	globalData[key] = val
}

export function get(key) {
	return globalData[key]
}
```
随后可以在任意位置进行使用啦

```
import { set as setGlobalData, get as GetGlobalData} from './path_to/globalData';

setGlobalData('test', 1)
getGlobalData('test')
```

