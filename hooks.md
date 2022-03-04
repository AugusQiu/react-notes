## 什么是hooks?
hooks其实是以use开头的一些react api，react提倡使用函数组件，但是函数组件不像类组件便于做一些state的状态管理  
## 类组件不足之处
### 状态逻辑难复用
### HOC(Higher Order Components）
高阶组件：是一种设计模式，借鉴了高阶函数的思想，接受组件类型的参数，返回一个组件(component,...args) => component)  
为了方便组合，推荐偏函数来传入其他参数   
````js
const connentComponent= connent(dispatchProps,dispatchAction)(component);
````
````js
/*
  假设需求如下：
  v1.0 页面上显示'hello world!'
  v2.0 页面上显示'HELLO WORLD!'
  思路：
  1. 先封装一个组件，显示小写的hello world!
  2. 在第一个组件的基础上包装一下，将第一个组件返回的数据转成大写
*/

// v1.0的组件
class HelloWorld extends React.component{
    render(){
        return "hello,world!"
    }
}
ReactDOM.render(<HelloWorld/>, document.querySelector('#root'))

````
````js
// v2.0的组件 HOC函数实现
export const toUpperCaseHoc = function(WrappedComponent){
    return class Hoc extends React.Component{
        render(){
            const { text } = this.props
            const text2Upper = text.toUpperCase()
            return <WrappedComponent text={text2Upper} />
        }
    }
}

// v1.0版本实现的组件
export class HelloWorld extends React.Component{
    render(){
        return this.props.text
    }
}

// 用HOC包装后生成的新的组件，符合v2.0版本的需求，同时包含了v1.0的其它功能
const HelloWorld2Upper = toUpperCaseHoc(HelloWorld);

ReactDOM.render(<HelloWorld2Upper text="hello,world!" />, document.querySelector('#root'));
````
### Render Props
首先本质上还是一个prop，不过传递的值是一个函数  
````js
<DataProvider render={data => (
    <h1>Hello, {data.target}</h1>
)} />

class DataProvider extends React.Component{
    state = {
        data:{
            target: 'World'
        }
    }
    render(){
        return this.props.render(this.state)
    }
}
/*
  这样DataProvider组件渲染的结果就是<h1>Hello World</h1>
  为什么不直接把h1元素直接写在DataProvider的render函数里，考虑到代码的可复用性，如果下次我们希望渲染的结果是<span>Hello World</span>，我们就不用直接修改原组件，通过render props动态修改
*/
````
### 类组件中到处都是对状态的访问和处理，这样导致很难拆分成更小的组件
### this指向问题
父组件给子组件传递函数时，必须绑定this
````js
class App extends React.Component {
    // 事件函数
    fn1(){
        console.log('fn1')
        console.log(this) // undefined
    }
    render(){
        <button onClick={this.fn1}>按钮1</button>
    }
}
````
````js
// this指向undefined?
class Star {
    thisDirection(){
        console.log(this)
    }
}
var star = new Star();
star.thisDirection(); // Star{}
const x = star.thisDirection; // undefined
x() // 直接调用
// 类中所有的方法，在局部默认开启了严格模式，它不敢指向 window 所以指向了 undefined
````

