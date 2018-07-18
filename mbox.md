# 1. 下载依赖

```text
npm install --save mobox
```

# 2. 关键API

## 2.1 创建可观察数据

```javascript
import { observable } from 'mobx';

class Store {
  @observable array = [];
  @observable obj = {};
  @observable map = new Map();
  @observable string = 'hello';
  @observable number = 10;
  @observable bool = true;
}
```

## 2.2 computed

computed函数，可以将多个可观察数据组合成一个可观察数据。

```javascript
import { observable, computed, observe } from 'mobx';

let store = new Store();

// 将store.string和store.number组合成一个数据[foo]
let foo = computed(() => {
  return store.string + '/' + store.number;
})

// 对数据[foo]实施监听，当store.string或store.number发生变化的时候，就会自动执行
foo.observe(({newValue, oldValue}) => {
  console.log(`从【${oldValue}】变成【${newValue}】`);
})

// 改变可观察数据
store.number = 99;
store.string = 'world';
```

控制台的输出如下：
```text
从【hello/10】变成【hello/99】
从【hello/99】变成【world/99】
```

## 2.3 autorun

我们也可以把 **computed** 的代码移动到store中。
```javascript
import { observable, computed, autorun } from 'mobx';
class Store {
  @observable array = [];
  @observable obj = {};
  @observable map = new Map();
  @observable string = 'hello';
  @observable number = 10;
  @observable bool = true;

  @computed get mixedNumerString() {
    return this.string + '/' + this.number;
  }
}

let store = new Store();
// autorun可以自动检测可观察数据的变化，不需要手动调用observe
autorun(() => {
  console.log(store.mixedNumerString);
})

// 改变可观察数据
store.number = 99;
store.string = 'world';
```

控制台的输出如下：
```text
hello/10
hello/99
world/99
```

可以看出，**在可观察数据改变之前，原有的数据会输出一次！**