# 1. 下载依赖
```text
npm install react-transition-group --save
```

# 2. 使用方法

我们有以下一个例子：

```javascript
import React, {Component} from 'react';
class AnimateTest extends Component {
  render () {
    return (
      <div style={{marginTop: '10px', textAlign: 'center'}}>
        <button>Click</button>
        <h2>Hello world</h2>
      </div>
    );
  }
}

export default AnimateTest;
```
这个例子中有一个按钮和一段文字。我们要做的是通过点击按钮，来控制文字的显示和隐藏，并使其带有动画效果。

我们需要这样来做：
1. 首先，需要给组件的state添加一个布尔变量，用于控制文字的显示与隐藏
2. 然后，给按钮添加一个点击方法，用于控制布尔变量的值
3. 使用 **<CSSTransition>** 标签将文字包裹
4. 为 **<CSSTransition>** 标签添加属性
5. 制作CSS文件

代码如下：
组件：
```javascript
import React, {Component} from 'react';
import {CSSTransition} from 'react-transition-group';
import './Animate.style.css';
class AnimateTest extends Component {

  constructor(props) {
    super(props)
    this.state = {  // show为true的时候显示，false的时候隐藏
      show: true
    }
    this.btnClick = this.btnClick.bind(this);
  }

  render () {
    return (
      <div style={{marginTop: '10px', textAlign: 'center'}}>
        <button onClick={this.btnClick}>Click</button>
        <CSSTransition
          in={this.state.show}
          timeout={1000}
          classNames="hello"  // 此处的[hello]是CSS类的前缀
        >
          <h2>Hello world</h2>
        </CSSTransition>
      </div>
    );
  }

  btnClick() {
    this.setState(() => {
      return {
        show: this.state.show ? false : true
      }
    });
  }
}
export default AnimateTest;
```

css文件（Animate.style.css）：
```css
/* 入场动画刚要执行的那一瞬间 */
.hello-enter{
  opacity: 0;
} 

/* 从入场动画开始执行到入场动画执行完毕之前的一段时间 */
.hello-enter-active{
  opacity: 1;
  transition: opacity 1s ease-in-out;
} 

/* 入场动画执行完毕之后 */
.hello-enter-done {
  opacity: 1;
}

/* 出场动画刚要执行的那一瞬间 */
.hello-exit{
  opacity: 1;
} 

/* 从出场动画开始执行到出场动画执行完毕之前的一段时间 */
.hello-exit-active{
  opacity: 0;
  transition: opacity 1s ease-in-out;
} 

/* 出场动画执行完毕之后 */
.hello-exit-done{
  opacity: 0;
}
```

# 3. 生命周期钩子

React Transition Group为我们准备了生命周期钩子函数，用于在动画执行的某一个时间点自动触发指定方法：

具体如下：
```javascript
<CSSTransition
  in={this.state.show}
  timeout={1000}
  classNames="hello"
  onEnter={() => {console.log('入场动画刚要执行的那一瞬间')}}
  onEntering={() => {console.log('从入场动画开始执行到入场动画执行完毕之前的一段时间')}}
  onEntered={() => {console.log('入场动画执行完毕之后')}}
  onExit={() => {console.log('出场动画刚要执行的那一瞬间')}}
  onExiting={() => {console.log('从出场动画开始执行到出场动画执行完毕之前的一段时间')}}
  onExited={() => {console.log('出场动画执行完毕之后')}}
>
  <h2>Hello world</h2>
</CSSTransition>
```

而且，所有的生命周期函数钩子还可以接受一个参数：

```javascript
// node: HtmlElement
onEnter={node => { /*对node进行操作*/ }} 
```

# 4. 动画组

如果我们想要给一组元素添加动画，那么就需要这样来做了：
```javascript
import React, {Component} from 'react';
import {CSSTransition, TransitionGroup} from 'react-transition-group';
import './Animate.style.css';
class AnimateTest extends Component {
  constructor (props) {
    super (props);
    this.state = {
      list: [],
    };
    this.btnClick = this.btnClick.bind (this);
  }

  render () {
    return (
      <div style={{marginTop: '10px', textAlign: 'center'}}>
        <button onClick={this.btnClick}>Click</button>
        <ul>
          <TransitionGroup> {/*动画组*/}
            {this.state.list.map ((item, index) => {
              return (
                <CSSTransition {/*为每一个元素添加动画*/}
                  key={item + index}
                  timeout={1000}
                  classNames="hello"
                >
                  <li>{item}</li>
                </CSSTransition>
              )
            })}
          </TransitionGroup>
        </ul>
      </div>
    );
  }

  btnClick () {
    this.setState (prevState => {
      return {
        list: [...prevState.list, 'hello'],
      };
    });
  }
}

export default AnimateTest;
```