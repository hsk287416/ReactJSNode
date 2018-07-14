# 1. 中间件

示意图：


# 2. redux-thunk
## 2.1 下载依赖

```javascript
npm install --save redux-thunk
```

## 2.2 配置

在store文件中这样来写：
```javascript
import {createStore, applyMiddleware, compose} from 'redux';
import todoListReducer from './todoList.reducer';
import thunk from 'redux-thunk';

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
  ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ({})
  : compose;

const enhancer = composeEnhancers (applyMiddleware (thunk));

const store = createStore (
  todoListReducer,
  enhancer
);

export default store;
```

## 2.3 使用

在actionCreater文件中这样来写：
```javascript

// 初始化列表Action
export function getInitListAction(list) {
  return {
    type: INIT_LIST,
    list: list
  }
}

// 从服务器下载列表内容的Action
export function getLoadListAction() {
  // 执行该Action的时候，会被自动注入一个store.dispatch的方法
  return (dispatch) => {
    Axios.get('http://localhost:4800/todoList').then((response) => {
      const action = getInitListAction(response.data);
      dispatch(action);
    });
  }
}
```

在reducer文件中这样来写：
```javascript
import { INPUT_VALUE_CHANGE, LIST_ADD, LIST_REMOVE, INIT_LIST } from './todoList.action';

const defaultState = {
  inputValue: '',
  list: []
};

export default (state = defaultState, action) => {
  const newState = {...state};

  // 当获取到INIT_LIST的Action的时候，更新state中的list对象
  if (action.type === INIT_LIST) {
    newState.list = action.list;
  }

  return newState;
}
```

在组件文件中这样来写：
```javascript
// 在组件挂载完成的时候调用getLoadListAction
componentDidMount() {
  const action = getLoadListAction();
  store.dispatch(action);
}
```