# 1. redux的工作流程

![avatar](https://raw.githubusercontent.com/hsk287416/ReactJSNote/master/imgs/2018-07-11_205456.png)

redux是一个数据层的框架，它将所有的数据都放在Store中。


# 2. 下载依赖
```typescript
npm install --save react-redux
```

# 3. 编写store文件

文件名：**store/index.jsx**

```javascript
import { createStore } from 'redux';
import todoListReducer from './TodoList.reducer'; // 之后编写

const store = createStore (
  todoListReducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);

export default store;
```

# 4. 编写action文件

文件名：**store/TodoList.action.jsx**

```javascript
export const INPUT_VALUE_CHANGE = 'input_value_change';
export const INIT_LIST = 'init_list';
export const LIST_ITEM_ADD = 'list_item_add';
export const LIST_ITEM_REMOVE = 'list_item_remove';

export const inputValueChangeAction = (value) => {
  return {
    type: INPUT_VALUE_CHANGE,
    payload: value
  }
}

export const initListAction = (list) => {
  return {
    type: INIT_LIST,
    payload: list
  }
}

export const listItemAdd = () => {
  return {
    type: LIST_ITEM_ADD
  }
}

export const listItemRemove = (index) => {
  return {
    type: LIST_ITEM_REMOVE,
    payload: index
  }
}
```

# 5. 编写reducer文件

文件名：**store/TodoList.reducer.jsx**

```javascript
import { INPUT_VALUE_CHANGE, LIST_ITEM_ADD, LIST_ITEM_REMOVE, INIT_LIST } from './TodoList.action';

const defaultState = {
  inputValue: '',
  list: []
}

function todoListReducer(state = defaultState, action) {
  const newState = {...state};

  if (action.type === INPUT_VALUE_CHANGE) {
    newState.inputValue = action.payload;
  }

  if (action.type === LIST_ITEM_ADD) {
    newState.list.push(newState.inputValue);
    newState.inputValue = '';
  }

  if (action.type === LIST_ITEM_REMOVE) {
    newState.list = newState.list.filter((item, index) => index !== action.payload);
  }

  if (action.type === INIT_LIST) {
    newState.list = action.payload;
  }

  return newState;
}

export default todoListReducer;
```

# 6. 在组件中使用
```javascript
import React, {Component} from 'react';
import {connect} from 'react-redux';
import {
  inputValueChangeAction,
  listItemAdd,
  listItemRemove
} from '../store/TodoList.action';

class TodoList extends Component {

  render () {
    const {inputValue, list, changeInputValue, listItemAdd, listItemRemove} = this.props;
    return (
      <div>
        <input type="text" id="itemInput" value={inputValue} onChange={changeInputValue} />
        <button onClick={listItemAdd}>提交</button>
        <ul>
          {
            list.map((item, index) => (
              <li key={item + index} onClick={() => {listItemRemove(index)}}>{item}</li>
            ))
          }
        </ul>
      </div>
    );
  }

  componentDidMount() {
    this.props.loadList();
  }
}

// 把store中的数据映射到组件的props
const mapStateToProps = state => {
  return {
    inputValue: state.inputValue,
    list: state.list,
  };
};

// 把action映射到组件的props
const mapDispatchToProps = dispatch => {
  return {
    changeInputValue: e => {
      const action = inputValueChangeAction (e.target.value);
      dispatch (action);
    },
    listItemAdd: () => {
      const action = listItemAdd ();
      dispatch (action);
    },
    listItemRemove: (index) => {
      const action = listItemRemove (index);
      dispatch (action);
    }
  };
};

export default connect (mapStateToProps, mapDispatchToProps) (TodoList);
```

# 7. 如下修改index.js文件

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import TodoList from './components/TodoList.component';
import registerServiceWorker from './registerServiceWorker';
import { Provider } from 'react-redux';
import store from './store';

const App = (
  <Provider store={store}>
    <TodoList />
  </Provider>
)

ReactDOM.render(App, document.getElementById('root'));
registerServiceWorker();
```