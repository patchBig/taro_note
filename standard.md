# Taro 规范

## 项目组织

### 文件命名

Taro 中普通 JS/TS 文件以小写字母命名，多个单词以下划线连接，例如 util.js、util_helper.js

Taro 组件文件命名遵循 Pascal 命名法，例如 ReservationCard.jsx

## JavaScript 书写规范

### 点号操作符须与属性需在同一行

```js
console.log('hello')  // ✓ 正确

console.
  log('hello')  // ✗ 错误

console
  .log('hello') // ✓ 正确
```

### 每个 const/let 关键字单独声明一个变量

```js
// ✓ 正确
const silent = true
let verbose = true

// ✗ 错误
const silent = true, verbose = true

// ✗ 错误
let silent = true,
    verbose = true
```

### 不要省去小数点前面的 0

```js
const discount = .5      // ✗ 错误
const discount = 0.5     // ✓ 正确
```

### 不要解构空值

```js
const { a: {} } = foo         // ✗ 错误
const { a: { b } } = foo      // ✓ 正确
```

### 对象属性换行时注意统一代码风格

```js
const user = {
  name: 'Jane Doe', age: 30,
  username: 'jdoe86'            // ✗ 错误
}

const user = { name: 'Jane Doe', age: 30, username: 'jdoe86' }    // ✓ 正确

const user = {
  name: 'Jane Doe',
  age: 30,
  username: 'jdoe86'
}
```

### 子类的构造器中一定要调用 super

```js
class Dog {
  constructor () {
    super()   // ✗ 错误
  }
}

class Dog extends Mammal {
  constructor () {
    super()   // ✓ 正确
  }
}
```

### 使用 this 前请确保 super() 已调用

```js
class Dog extends Animal {
  constructor () {
    this.legs = 4     // ✗ 错误
    super()
  }
}
```

### 禁止多余的构造器

```js
class Car {
  constructor () {      // ✗ 错误
  }
}

class Car {
  constructor () {      // ✗ 错误
    super()
  }
}
```

### 对于三元运算符 ? 和 : 与他们所负责的代码处于同一行

```js
// ✓ 正确
const location = env.development ? 'localhost' : 'www.api.com'

// ✓ 正确
const location = env.development
  ? 'localhost'
  : 'www.api.com'

// ✗ 错误
const location = env.development ?
  'localhost' :
  'www.api.com'
```

### 不要丢掉异常处理中 err 参数

```js
// ✓ 正确
run(function (err) {
  if (err) throw err
  window.alert('done')
})
// ✗ 错误
run(function (err) {
  window.alert('done')
})
```

### 用 throw 抛错时，抛出 Error 对象而不是字符串

```js
throw 'error'               // ✗ 错误
throw new Error('error')    // ✓ 正确
```

### 使用 Promise 一定要捕捉错误

```js
asyncTask('google.com').catch(err => console.log(err))   // ✓ 正确
```

### 对齐方式

> 多个属性，多行书写，每个属性占用一行，标签结束另起一行

```js
// bad
<Foo superLongParam='bar'
     anotherSuperLongParam='baz' />

// good
<Foo
  superLongParam='bar'
  anotherSuperLongParam='baz'
/>

// 如果组件的属性可以放在一行就保持在当前一行中
<Foo bar='bar' />

// 多行属性采用缩进
<Foo
  superLongParam='bar'
  anotherSuperLongParam='baz'
>
  <Quux />
</Foo>
```

### 属性书写

> 属性名称始终使用驼峰命名法

```js
// bad
<Foo
  UserName='hello'
  phone_number={12345678}
/>

// good
<Foo
  userName='hello'
  phoneNumber={12345678}
/>
```

### JSX 与括号

> 用括号包裹多行 JSX 标签

```js
// bad
render () {
  return <MyComponent className='long body' foo='bar'>
           <MyChild />
         </MyComponent>
}

// good
render () {
  return (
    <MyComponent className='long body' foo='bar'>
      <MyChild />
    </MyComponent>
  );
}

// good
render () {
  const body = <div>hello</div>
  return <MyComponent>{body}</MyComponent>
}
```

### 标签

> 当标签没有子元素时，始终时候自闭合标签

```js
// bad
<Foo className='stuff'></Foo>

// good
<Foo className='stuff' />
```

### 生命周期书写顺序

在 Taro 组件中会包含类静态属性、类属性、生命周期等的类成员，其书写顺序最好遵循以下约定（顺序从上至下）

1. static 静态方法
2. constructor
3. componentWillMount
4. componentDidMount
5. componentWillReceiveProps
6. shouldComponentUpdate
7. componentWillUpdate
8. componentDidUpdate
9. componentWillUnmount
10. 点击回调或者事件回调 比如 onClickSubmit() 或者 onChangeDescription()
11. render

[通用约束与建议](https://nervjs.github.io/taro/docs/spec-for-taro.html#%E9%80%9A%E7%94%A8%E7%BA%A6%E6%9D%9F%E4%B8%8E%E5%BB%BA%E8%AE%AE)