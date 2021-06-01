[TOC]

## VUEX

### 推荐阅读

[五分钟搞懂Vuex - ArthurWong - 博客园 (cnblogs.com)](https://www.cnblogs.com/chinabin1993/p/9848720.html)

### [官方](https://vuex.vuejs.org/zh/ )介绍

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

### 个人理解

简单来说，就是“全局变量”的管理，该“全局变量”可以用于多个组件页面，每个页面都可以获取以及修改此变量。并且遵循flux模式，即单项数据流动。

组件页面之间的数据传递可以通过router-view中的参数传递，但并不是特别方便，因此有了vuex。

### 核心概念

- state ：存储状态（变量），可以理解成存储所有“全局变量”的大仓库

- Getters：可以理解成state的计算属性，例如对列表进行筛选后计数，使用方式：$store.getters.fun()  类似于vue的计算属性

- mutations：更改vuex中stae的值的唯一途径（也是通过此来保证数据的单项流动，flux），能记录每次状态改变。是同步的。使用方式：$store.commit('',params)。

- actions：action可以理解成在直接通过mutation来改变值的中间加了一个操作，这个操作可以做一些异步的操作，然后再在这操作中提交mutation

  ​			 Action 类似于 mutation，不同在于：

  - Action 提交的是 mutation，而不是直接变更状态
  - Action 可以包含任意异步操作
  - 通过$store.dispatch('increment')来出发action

- module：当大项目只用一个state时，会导致state变得臃肿，因此允许将state切割成多个模块（module），每个模块拥有自己的state、mutation、action、getter、甚至是嵌套module。

![image-20210114211010957](https://gitee.com/trastor/picture/raw/master/image-20210114211010957.png)

### 代码示例

```
npm install vuex --save   //安装
```

```js
//src\store.js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const state = {
  shopName: "",
};

const mutations = {
  mutationsChangeShopName(state, obj) {
    return state.shopName = obj;
  },
};
const actions = {
  incrementAsync (store) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
//定义一个全局实例对象
export default new Vuex.Store({
  state,
  mutations,
  actions
});
```

```js
//src\main.js
import store from './store'

new Vue({
  el: '#app',
  store,
  components: { App },
  template: '<App/>'
})
//将store作为Vue的选项，这样Vue会将store“注入”到每一个子组件中，也就是说每个组件都可以通过this.$store来访问全局单例对象store。
```

```js
//组件中使用
var shopName = this.$store.state.shopName;//获取值
this.$store.commit("mutationsChangeShopName","新的值");//赋值
this.$store.dispatch('incrementAsync');//通过action进行异步提交
```

### module的使用

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

### 扩展

**mapState**、**mapGetters**、**mapMutations**

mapState、mapGetters：vuex提供的方法，可以通过这两个方法批量的把state/getters传给计算属性computed。

```js
import { mapState } from 'vuex'//引入
//mapState
export default {
  // ...
  computed: mapState({
     //有以下三种写法
    // 箭头函数可使代码更简练
    count: state => state.count,
    // 传字符串参数 'count' 等同于 `state => state.count` ，这里可以重命名为countAlias
    countAlias: 'count',//count是对应store中action的一个方法名
    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
//如果是要和组件的自有的computed组合，则可以使用扩展符...
  computed: {
      myComputed():{return this.count;},//这是组件自己的
      ...mapState({//这是直接将vuex的state引入的
    	countAlias: 'count',//count是对应store中action的一个方法名
  		})
  }

```

```js
import { mapGetters } from 'vuex'//引入
//mapGetters
export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',//对应getters中的属性
      'anotherGetter',
      // ...
    ]),
     //还可以取个别名
      ...mapGetters({
          // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
          doneCount: 'doneTodosCount'
      })
  }
}
```

**mapMutations**：将组件中的 methods 映射为 `store.commit` 调用（需要在根节点注入 `store`），当前组件多次调用同一个mutation时，可以通过这样的方式简化写法。

```js
import { mapMutations } from 'vuex'
methods:{
    ...mapMutations({
        setNumber:'SET_NUMBER',
    })
}
//然后调用this.setNumber(10)相当调用this.$store.commit('SET_NUMBER',10)
```



### 查看

可以在谷歌浏览器上安装插件“Vue.js devtools”，安装后则可以直接在开发者工具中看见store对象的值

![image-20210123095940140](https://gitee.com/trastor/picture/raw/master/image-20210123095940140.png)

### 实现vue组件间的数据实时刷新

```
//这样的方式是不会让视图实时刷新的
data() {
     return {
      	userInfo: this.$store.state.userInfo;
     };
 }
 
 //方法一
 <template>
 	<div>{{$store.state.userInfo}}</div> 
</template>

//方法二 计算属性
 computed: {
     userInfo(){
     	 return this.$store.state.userInfo;
     }
 }
 
 //方法三  监听
 watch: {
     "this.$store.state.userInfo"() {
      	this.userInfo = this.$store.getters.getUserInfo; // 按照规范在这里应该去使用getters来获取数据
     }
 }
```

### 题目

#### vuex解决了什么问题

- 多个组件依赖于同一状态时，对于多层嵌套的组件的传参将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。
- 来自不同组件的行为需要变更同一状态。以往采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

#### 什么时候用vuex

- 多个组件依赖于同一状态时。
- 来自不同组件的行为需要变更同一状态。

#### vuex中的状态是对象时，需要注意什么？

因为对象是引用类型，复制后改变属性还是会影响原始数据，这样会改变state里面的状态，是不允许，所以先用深度克隆复制对象，再修改。

#### 怎么在组件中批量使用state、getter、mutation？

- 通过使用扩展函数mapState、mapGetters、mapMutations

#### action和mutation有什么异同？

区别

- action 提交的是 mutation，而不是直接变更状态。mutation可以直接变更状态。
- action 可以包含任意异步操作。mutation只能是同步操作。
- 提交方式不同，action 是用`this.$store.dispatch('ACTION_NAME',data)`来提交。mutation是用`this.$store.commit('SET_NUMBER',10)`来提交。
- 接收参数不同，mutation第一个参数是state，而action第一个参数是context(与 store 实例具有相同方法和属性的 context 对象)

相同点

- 第二参数都可以接收外部提交时传来的参数

#### action有时候提交时异步，怎么知道是否结束了？

action返回一个promise

```js
actions:{
    SET_NUMBER_A({commit},data){
        return new Promise((resolve,reject) =>{
            setTimeout(() =>{
                commit('SET_NUMBER',10);
                resolve();
            },2000)
        })
    }
}
this.$store.dispatch('SET_NUMBER_A').then(() => {
  // ...
})
```

#### Vuex中有两个action，分别是actionA和actionB，其内都是异步操作，在actionB要提交actionA，需在actionA处理结束再处理其它操作，怎么实现？

利用ES6的`async`和`await`来实现。

```js
actions:{
    async actionA({commit}){
        //...
    },
    async actionB({dispatch}){
        await dispatch ('actionA')//等待actionA完成
        // ... 
    }
}
```

#### 模块中，getter和mutation和action中怎么访问全局的state和getter？

- 在getter中可以通过第三个参数rootState访问到全局的state,可以通过第四个参数rootGetters访问到全局的getter。
- 在mutation中不可以访问全局的satat和getter，只能访问到局部的state。
- 在action中第一个参数context中的`context.rootState`访问到全局的state，`context.rootGetters`访问到全局的getter。

#### 其他题目可参考

[Vuex面试题汇总 (juejin.cn)](https://juejin.cn/post/6844903993374670855)