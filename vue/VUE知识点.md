[TOC]

## 原理

### 双向绑定原理

MVVM数据双向绑定：即数据变化更新视图，视图变化更新数据

- 通过`Object.defineProperty`方法实现了对`object`数据的可观测，并且封装了`Observer`类，让我们能够方便的把`object`数据中的所有属性（包括子属性）都转换成`getter/seter`的形式来侦测变化（实际就是通过对object数据进行遍历，一一实现数据可观测）
- 然后在`getter`中收集依赖，在`setter`中通知依赖更新，以及封装了依赖管理器`Dep`，用于存储收集到的依赖。
- 我们为每一个依赖都创建了一个`Watcher`实例，当数据发生变化时，通知`Watcher`实例，由`Watcher`实例去做真实的更新操作。

其整个流程大致如下：

1. **数据可观测**：`Data`通过`observer`转换成了`getter/setter`的形式来追踪变化（将数据变成可观测的）。

2. **依赖收集**：每个数据对应的订阅器Dep负责在getter收集订阅者Watcher。

   订阅者Watcher初始化的时候，先在自己缓存下来，然后通过调用属性触发getter，在getter中将缓存下来的watcher加到该数据对应的依赖收集器dep中，这时便已经将数据和依赖绑定了。典型的“发布订阅者”模式。

3. 依赖当数据发生了变化时，会触发`setter`，从而向`Dep`中的依赖（即Watcher）发送通知。

4. `Watcher`接收到通知后，会向外界发送通知，变化通知到外界后可能会触发视图更新，也有可能触发用户的某个回调函数等。

![image-20210405150316427](https://gitee.com/trastor/picture/raw/master/20210405150316.png)

**Object.defineProperty的缺点**

- 无法检测到对象属性的新增或者删除（vue可以用过set方式将属性变成可响应式的）
- 无法监听数组的变化（是通过重写数组的部分方法来实现，'pop','shift','unshift','sort','reverse','splice', 'push'）

### 模版编译

流程：

1. 解析器（模板字符串转换成AST）通过使用一个栈来维护节点，每从模板字符串中截取一个节点字符串，就将其推入栈中，同时构建一个AST节点，一直到结束节点在将其推出栈，如此循环最后构建出一套带有结构的AST对象。解析器有文本解析器，HTML解析器。
2. 优化器（标记静态节点）是通过遍历AST节点，对其中的静态节点做标记，同时最后标记处根静态节点，节省部分不必要的性能消耗。
3. 代码生成器（将AST装换成带结构的代码字符串）也是通过遍历去拼出一个渲染函数执行的代码字符串，遍历的过程根据不同的节点类型type调用不同的生成字符串方法，最后拼出一个完整的 render 函数需要的代码字符串。
4. 渲染函数通过前面的代码字符串生成VNode（虚拟dom）（类中包含了描述一个真实DOM节点所需要的一系列属性），会被缓存下来。
5. 根据虚拟dom生成真实的dom节点
6. 如果是数据修改，会新生成一个虚拟dom，将新的虚拟dom和之前缓存下来的虚拟dom进行比较（dom-diff算法），找出有差异的节点，并将差异的节点更新到真实dom中

<img src="https://gitee.com/trastor/picture/raw/master/20210405152607.png" alt="image-20210405152607722"  />

### 虚拟DOM

[Vue中的虚拟DOM | Vue源码系列-Vue中文社区 (vue-js.com)](https://vue-js.com/learn-vue/virtualDOM/#_1-前言)

所谓虚拟DOM，就是用一个`JS`对象来描述一个`DOM`节点，像如下示例：

```js
<div class="a" id="b">我是内容</div>
//以下就是上面的dom节点对应的虚拟dom
{
  tag:'div',        // 元素标签
  attrs:{           // 属性
    class:'a',
    id:'b'
  },
  text:'我是内容',  // 文本内容
  children:[]       // 子元素
}
```

vue是数据驱动的，数据发生变化后就会驱动视图变化，那么在更新视图的时候就需要更新dom，因为浏览器的标准，实际dom涉及的非常复杂，因此操作真实的dom是耗时的，通过使用虚拟dom，能够尽可能只更新数据变化的dom的内容，也就是用js的计算性能来换取操作真实dom消耗的性能。

当数据发生变化时，对比变化前后的虚拟dom节点，通过DOM-Diff算法计算出需要更新的地方，然后去更新需要更新的视图。

![image-20210311103120418](https://gitee.com/trastor/picture/raw/master/20210311103120.png)

虚拟dom的优势：

- 具备跨平台的优势

  由于 Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。

- 操作 DOM 慢，js运行效率高。我们可以将DOM对比操作放在JS层，提高效率

  因为DOM操作的执行速度远不如Javascript的运算速度快，因此，把大量的DOM操作搬运到Javascript中，运用patching算法来计算出真正需要更新的节点，最大限度地减少DOM操作，从而显著提高性能。

- 提升渲染性能

  Virtual DOM的优势不在于单次的操作，而是在大量、频繁的数据更新下，能够对视图进行合理、高效的更新。

### DOM-Diff算法

<img src="https://gitee.com/trastor/picture/raw/master/20210311104124.png" alt="image-20210311104124667" style="zoom: 67%;" />

对于更新节点中，如果新旧VNode里都包含了子节点，我们就需要细致的去更新子节点，优化更新算法：[优化更新子节点 | Vue源码系列-Vue中文社区 (vue-js.com)](https://vue-js.com/learn-vue/virtualDOM/optimizeUpdataChildren.html#_1-前言)

## 组件间的通信 

[vue组件间通信六种方式（完整版） - SegmentFault 思否](https://segmentfault.com/a/1190000019208626)

- prop/$emit    父组件A通过props的方式向子组件B传递，B to A 通过在 B 组件中 $emit, A 组件中 v-on 的方式实现。
- vuex
- Vuex和localStorage：vuex刷新界面后数据就没了，可以存在localStorage 中，刷新之后获取一遍。（localStorage 只支持字符串，需要json转换）
- $attrs/$listeners
- provide、inject：只要在上一层级的声明的provide，那么下一层级无论多深都能够通过inject来访问到provide的数据
- $parent/ $children与 ref

**1、组件=>子组件**

- props
- $children     直接在父组件中通过 this.$children[0].msg获取子组件中定义的msg值
- ref    通过在父组件时，给子组件设置ref，再通过this.$refs.child获取ref=child的对象

**2、子组件=>父组件**

- $parent 直接在子组件中通过this.$parent.msg获取父组件中的msg的值
- $emit 使用$emit传递事件给父组件，父组件监听该事件

```
//子组件
<button @click="pushMsg()"></button>
methods: {
    pushMsg() {
      this.$emit("showMsg", "这是子组件===>父组件的值");
    }
  },
  
  //父组件
<m-child v-bind:msg="p2C" @showMsg='getChild' ref='child'></m-child>
methods: {
  getChild(val) {
  		this.msg=val
  },
}
```

**3、非父子之间的传值**

- vuex

- provide、inject

- $attrs/$listeners  $attrs（爷爷=>孙子）、$listeners+$emit（孙子=>爷爷）

  ```js
  //爷组件
  <Child @busFn="testFn2" toChiled2Val="这是爷爷给你的值"  name="trastor" age="24"></Child>
   testFn2: function (val) {
        console.log(val);//这里接收孙组件传过来的值
   },
  //子组件 Child
  //v-bind="$attrs"  v-on="$listeners" 添加这两个才可以让孙子组件拿到事件、数据
   <Child2 v-bind="$attrs"  v-on="$listeners"></Child2>
  inheritAttrs:true,//并且设置vue实例参数,意思是父组件传过来的值，如果没有被定义在props里，也会保存下来
      
  //孙组件
   <button @click="testFn">孙子组件按钮</button>
   props: {
      toChiled2Val:{ type: String,}//这里可以接收到爷组件传过来的值
    },
   methods: {
      testFn: function () {
        this.$emit("busFn","这是孙子的值");//这里将值传给爷组件
      },
    },
  ```
  
- 通过公共js(事件总线)  +$emit

  ```js
  //bus.js
  import Vue from 'vue'
  export default new Vue
  
  //子组件
  import bus from './util/bus'
  methods: {
    passMsg () {
      bus.$emit('msg', 'i am from app')
    }
  },
  //父组件
  mounted () {
      bus.$on('msg', (val) => {
        this.childMsg = val
      });
  }
  ```

  

### provide、inject

孙子组件，使用provide、inject（也可以使用vuex，但是有的小项目可能并不需要用到vuex，影响性能）。只要在上一层级的声明的provide，那么下一层级无论多深都能够通过inject来访问到provide的数据

```js
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {//重点在这
    user: 'John Doe'
  },
  template: `
    <div>
      {{ todos.length }}
      <!-- 模板的其余部分 -->
    </div>
  `
})
//孙子组件
app.component('todo-list-statistics', {
  inject: ['user'],//重点在这
  created() {
    console.log(`Injected property: ${this.user}`) // > 注入 property: John Doe
  }
})
```

### ref

官方解释：ref 是被用来给元素或子组件注册引用信息的。引用信息将会注册在父组件的 $refs 对象上

- 如果ref用在子组件上，指向的是组件实例，可以理解为对子组件的索引，**通过$ref可获取到在子组件里定义的属性和方法**。
- 如果ref在普通的 DOM 元素上使用，引用指向的就是 DOM 元素，**通过$ref可能获取到该DOM 的属性集合**，轻松访问到DOM元素，作用与JQ选择器类似。

**也可以使用ref来将数据从父组件传到子组件**：先在子组件写好一个方法来修改自身的数据，然后在父组件通过ref获取到子组件的属性及方法后，调用子组件方法来修改数据。

```vue
<!-- 父组件 -->
<template>
 <div>
 <h1>我是父组件！</h1>
 <child ref="msg"></child>
 </div>
</template>
<script>
 import Child from '../components/child.vue'
 export default {
 components: {Child},
 mounted: function () {
  console.log( this.$refs.msg);
  this.$refs.msg.getMessage('子组件的数据已经被我修改啦~');//在此处调用子组件方法来修改数据
 }
 }
</script>

<!-- 子组件 -->
<template>
 <h3>{{message}}</h3>
</template>
<script>
 export default {
 data(){
  return{
  message:''
  }
 },
 methods:{
  getMessage(m){
  this.message=m;
  }
 }
 }
</script>
```

### props

[官方API](https://cn.vuejs.org/v2/api/?#props)

子组件的props选项能够接收来自父组件数据。仅仅只能接收，props是**单向绑定**的，即只能父组件向子组件传递，不能反向。

```vue
//prop定义方式
<script>
 export default {
 props: ['message'] //1、直接声明变量数组
 }
</script>

<script>
 export default {
 props: {
   // 2、检测类型
    message: Number,
    // 3、检测类型 + 其他验证
    age: {
      type: Number,
      default: 0,
      required: true,
      validator: function (value) {
        return value >= 0
      }
    }
 } 
 }
</script>
```

```vue
<!-- 父组件 -->
<template>
 <div>
 <h1>我是父组件！</h1>
 <child message="我是子组件一！"></child> //通过自定义属性传递数据
 </div>
</template>
<script>
import Child from '../components/child.vue'
export default {
 components: {Child},
}
</script>


<!-- 子组件 -->
<template>
 <h3>{{message}}</h3>
</template>
<script>
 export default {
 props: ['message'] //声明一个自定义的属性
 }
</script>

```

### emit

```js
vm.$emit( event, arg )
```

$emit 绑定一个自定义事件event，当这个这个语句被执行到的时候，就会将参数arg传递给父组件，父组件通过@event监听并接收参数。

```vue
<!-- 父组件 -->
<template>
 <div>
 <h1>{{title}}</h1>
 <child @getMessage="showMsg" @clickBtn="clickButton(true,$event)" @clickBtn2="clickButton2(arguments)"></child>
 </div>
</template>
<script>
 import Child from '../components/child.vue'
 export default {
 components: {Child},
 data(){
  return{
  title:''
  }
 },
 methods:{
     showMsg(title){
         this.title=title;
     },
     clickButton(istrue,title){
         this.title=title;
     },
     clickButton2(arguments){
         this.title=arguments[0];
     }
 }
 }
</script>

<!-- 子组件 -->
<template>
 	<h3>我是子组件！</h3>
    <button @click="$emit('clickBtn','你点我啦')" :class="type">点击</button>
<button @click="$emit('clickBtn2','你点我啦','电死你')" :class="type">传递多个参数</button><!-- 通过arguments接收 -->
</template>
<script>
 export default {
 mounted: function () {
  this.$emit('getMessage', '这是子组件传过来的数据')
 }
 }
</script>
```

## 其他

### .native

当我们在父组件中使用子组件时可以给子组件上注册一些事件，这些事件即包括使用`v-on`或`@`注册的自定义事件，也包括注册的浏览器原生事件（需要加 `.native` 修饰符）

```html
<child @select="selectHandler" 	@click.native="clickHandler"></child>
```

父组件既可以给子组件上绑定自定义事件，也可以绑定浏览器原生事件。这两种事件有着不同的处理时机，浏览器原生事件是由父组件处理，而自定义事件是在子组件初始化的时候由父组件传给子组件，再由子组件注册到实例的事件系统中。

### vue和mvvm

mvvm  = mv + vm 
mv 就是model to view  对应vue里的 数据监听，依赖收集 及触发UI更新
vm  view to model  对应vue里的 页面注册事件监听， 比如页面输入，监听输入事件，设置data 数据 

## vue过滤器

分为全局过滤器和局部过滤器

```js
//用法：
{{ message | filterName }}//在双花括号中
<div v-bind:id="rawId | formatId"></div>	// 在 `v-bind` 中
{{ message | filterName |filterName2}}  //使用多个过滤器，先走filterName，将其返回值传入filterName2中再处理。
{{ message | filterName("a","b")}}  //也可以传参，message的值会作为第一个参数传入

//定义
//局部过滤器  
new Vue({
	filters:{
		filterName:(value)=>{
			if(!value) return "";
			return value+"$";
		}
	}
});

//全局过滤器  定义在创建Vue实例前
Vue.filter("filterName",(value)=>{
    if(!value) return "";
    return value+"$";
});
new Vue({});
```

## API

### 全局配置 （Vue.config）

```
Vue.config.silent = true;//取消所有的日志与警告
Vue.config.devtools = true;//配置是否允许 vue-devtools 检查代码。开发版本默认为 true，生产版本默认为 false。生产版本设为 true 可以启用检查。
Vue.config.keyCodes={f1: 112}//给 v-on 自定义键盘事件的键名修饰符
Vue.config.productionTip = true;//显示或者阻止 vue 启动时的提示
```

### 全局API

```js
Vue.extend({})//要去实践、可以选择做一个消息提示框
Vue.set(traget, key, value);//向响应式对象中添加一个 property，并确保这个新 property 同样是响应式的，且触发视图更新
Vue.delete(target, key);//和set对应，删除对象的属性，如果是响应式的，确保删除能触发更新视图
Vue.nextTick( [callback, context] );//类似于setTimeout，在下次 DOM 更新循环结束之后执行延迟回调，可以在这里面操作更新后的dom
Vue.use(Vuex);//安装 Vue 插件, 插件需要提供 install 方法，如果插件是一个函数，它会被作为install方法。调用install方法时，会将Vue作为参数传入。install方法被同一个插件多次调用时，插件也只会被安装一次。
vue mixin //是针对组件间功能共享来做的。可以对组件的任意部分（生命周期， data等）进行mixin，但不同的 mixin 之后的合并策略不同
Vue.observable//VUE自己的状态管理 小型vuex
```

#### mixin

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。**可以用来将相同逻辑进行抽离。**

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

- data	数据对象在内部会进行递归合并，并在发生冲突时以**组件数据优先**
- 钩子函数  同名钩子函数将合并为一个数组，因此都**将被调用**。另外，混入对象的钩子将在组件自身钩子**之前**调用
- 对象的选项，例如 `methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象键名冲突时，取**组件对象的键值对**

总结：只有钩子函数才会将组件和混入对象内容进行合并，其他情况如果冲突就会有限组件内容。

```js
//混入
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],//重点在这
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})

//全局混入
// 为自定义的选项 'myOption' 注入一个处理器。
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})
```

#### 自定义组件-directive

除去内置指令，例如v-show、v-model、v-if等，可以自定义组件，分为全局指令和局部指令。

实用的自定义指令：按钮防抖（直接传入一个方法）、按钮权限等。[8个非常实用的Vue自定义指令 - a权 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lzq035/p/14183553.html)

有以下**钩子函数**：

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`：所在组件的 VNode 更新时调用，**但是可能发生在其子 VNode 更新之前**。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。

- `componentUpdated`：指令所在组件的 VNode **及其子 VNode** 全部更新后调用。
- `unbind`：只调用一次，指令与元素解绑时调用。

指令钩子函数会被传入**以下参数**：

- `el`：指令所绑定的元素，可以用来直接操作 DOM。
- binding：一个对象，包含以下 property：
  - `name`：指令名，不包括 `v-` 前缀。
  - `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。
- `vnode`：Vue 编译生成的虚拟节点
- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。

```js
//注册全局指令
Vue.directive('focus', {
  inserted: function (el) {
    el.focus();
    console.log("focus",el);
  }
})

//用法
<input placeholder="输入" v-model="my.name" v-focus/>
```

```js
<input placeholder="输入" v-color="color" v-test:foo.a.b="color"/>  
//注册局部指令
directives: {
    test: {
      bind: function (el, binding, vNode, oldVnode) {
        console.log(binding);
        // {
        //     "name": "test",	//指令名，不包括 v- 前缀。
        //     "rawName": "v-test:[param].foo.a.b",//完整名字
        //     "value": "red",	//指令的绑定值
        //     "expression": "color",	//字符串形式的指令表达式
        //     "arg": "left",	//传给指令的参数  []中括号传过来的值 动态参数
        //     "modifiers": { 	//一个包含修饰符的对象
        //         "foo": true,
        //         "a": true,
        //         "b": true
        //     },
        //     "def": {}
        // }
      },
    },
    color: {
      inserted: function (el, binding, vNode, oldVnode) {
        el.style.backgroundColor = binding.value;
      },
    },
  },
  data() {
    return {
      color: "red",
      param:"left",
    };
  },
```

### 选项/数据

**watch**

```js
new Vue({
  el: '#root',
  data: {
    cityName: {id: 1, name: 'shanghai'}
  },
  watch: {
    //写法一
    cityName：(newName,oldName)=>{}
    //写法二
    cityName: 'someMethod',//方法名
    //写法三 
    cityName: {
      handler(newName, oldName) {},
      deep: true,//深度监听，则可以监听对象内属性的变化
      immediate: true,//第一次绑定的时候也执行函数
    }
     //写法四 只监听对象的某个属性
     "cityName.name": {
      handler(newName, oldName) {},
      deep: true,//深度监听，则可以监听对象内属性的变化
      immediate: true,//第一次绑定的时候也执行函数
    }
  } 
})

//如果是在方法里面修改监控的值，那么只会等方法执行完成后，才会去到watch
```

computed

里面的属性不应该是data中定义了的，如果有对data中定义的响应式数据的使用，那么当这些数据变化后，就会重新计算该值，不然就不会计算，使用上一次计算的值（有缓存）。

```js
computed: {
    // 仅读取
    aDouble: function () {
      return this.a * 2
    },
    // 读取和设置  set、get
    aPlus: {
      get: function () {
        return this.a + 1
      },
      set: function (v) {
        this.a = v - 1
      }
    }
  }
```

## 其他

### vue修改未在data中定义的值

```js
 data() {
    return {
      obj: { a: 1, b: 2 },
      arr: [1, 2, 3],
    };
  },
   mounted(){
       this.arr[0] = Math.random();
       this.obj.c = 222;
       //如果只执行以上两句话
       //1、因为数组的双向绑定特殊性，只有通过重写了的push、splice等方式去修改数组数组才会是响应式的，通过下标的方式修改值时，值修改成功了，但是无法在界面上渲染成最新的值。
       //2、因为对象obj中的c未在data中定义，所以c也不是可响应的，因此obj会有c值，但是界面上不会渲染出来。
       
       //可以通过添加下面的任何一句话，上面的两个值的修改变就可以界面上被渲染出来了（1、修改其他可响应的数据，然界面重新获取数据并渲染一次，2、将新增的属性值变成可响应的）
       this.obj.a = 111;//因为obj.a是可响应的，因此a的值改变就会触发set，通知对应的所以依赖，生成Vnode后会访问到data中的属性，因此c值也会被访问到，从而让c值也在界面上被渲染出来了。（缺点：obj.c依旧不是可响应的，之后如果某个方法只修改obj.c值，依旧无法渲染出来）
       this.$forceUpdate();//强制vue实例重新渲染（缺点同上）
       this.$set(this.obj, "c", 222);//直接将obj.c变成可响应的，那么以后单独修改obj.c值便可以自动渲染。
  },
```

### updated和watch的区别

watch

- 仅仅是数据发生改变的时候会侦听到
- 只是会检测到你写在watch里的那些属性,没写的就不会触发

updated

- 执行到它的时候时候是数据发生变化且界面更新完毕
- 所有的数据发生变化都会调用
- 每次触发的代码都是同一个

### keep-alive

**原理**：[通俗易懂了解Vue内置组件keep-alive内部原理 - 难凉热血，码梦为生！ - 博客园 (cnblogs.com)](https://www.cnblogs.com/wangjiachen666/p/11497200.html)

当它包裹动态组件时，会缓存这个组件实例，而不是销毁他，这样就防止重复渲染dom，减少加载时间以及性能损耗，提高体验。

- 在created里会创建一个cache对象，作为缓存容器，将该组件的vnode节点保存
- 在render渲染的时候会去cache缓存容器里看当前key值是否有被缓存，有的话那就直接取出来用。
- 在destroyed则会清除cache缓存的所有组件实例

属性有：

- include：字符串或者正则表达式，任何名称符合条件的才缓存
- exclude：字符串或者正则表达式，任何名称符合条件的不缓存
- max：最多可以缓存多少组件实例

生命周期函数：  被包含在keep-alive的组件实例会多出以下两个生命周期函数

- activated：组件被激活时调用  徐如果需要重新获取数据可以在该阶段获取。
- deactivated：组件停用时调用

```vue
<template>
  <div id="app">
  	// 1. 将缓存 name 为 test 的组件
  	<keep-alive include='test'>
      <router-view/>
    </keep-alive>
	
	// 2. 将缓存 name 为 a 或者 b 的组件，结合动态组件使用
	<keep-alive include='a,b'>
  	  <router-view/>
	</keep-alive>
	
	// 3. 使用正则表达式，需使用 v-bind
	<keep-alive :include='/a|b/'>
  	  <router-view/>
	</keep-alive>	
	
	// 5.动态判断
	<keep-alive :include='includedComponents'>
  	  <router-view/>
	</keep-alive>
	
	// 5. 将不缓存 name 为 test 的组件
	<keep-alive exclude='test'>
  	  <router-view/>
	</keep-alive>
  </div>
</template>
```

**清除缓存**

```js
beforeRouteLeave(to, from, next) {
  if (to.name === 'login') {
    this.$vnode.parent.componentInstance.cache = {}
    this.$vnode.parent.componentInstance.keys = []
  }
  next();
},
```

### v-model的原理

可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。v-model` 本质上不过是语法糖。它负责监听用户的输入事件以更新数据。

`v-model` 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

- text 和 textarea 元素使用 `value` property 和 `input` 事件；
- checkbox 和 radio 使用 `checked` property 和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

v-model底层原理实际上是分别利用了v-bind用来绑定value的值，用v-on去绑定input标准事件，这是事件用来监听当输入域内容发生变化的时候来执行一些事情。具体做的事情就是通过$event这个事件对象获取到最新的输入域的值，然后把最新的值赋值给旧的值，从而进行数据的更新。这样的话就完成了双向数据绑定。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id = "app">
        <input type="text" v-bind:value = 'msg' v-on:input = 'handle'>
        <input type="text" v-bind:value = 'msg' v-on:input = 'msg = $event.target.value'>
        <input type="text" v-model = "msg">
    </div>
    <script src="vue.js"></script>
    <script>
    /*v-model 指令的本质*/ 
     var vm = new Vue({
         el:'#app',
         data:{
            msg:'hello'
         },
         methods:{
            handle: function(event){
                // 使用输入域中最新的数据覆盖原来的数据
                this.msg = event.target.value;
            }
         }
     });
    </script>
</body>
</html>
```

### key的作用

如果没有这个属性的时候vue应用 in-place patch（就地复用）策略。列表里的顺序发生改变的时候比如shuffle（列表打乱）的时候，vue为了提升性能，不会移动dom元素，只是更新相应元素的内容节点。

这个默认的模式是高效的，但是只适用于不依赖子组件状态或临时 DOM 状态 (例如：表单输入值) 的列表渲染输出。也就是说如果v-for中有子组件，那么子组件是不会修改的。

**关键**：`key` 的特殊 attribute 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

有相同父元素的子元素必须有**独特的 key**。重复的 key 会造成渲染错误。

### vue常用的修饰符

.stop：等同于JavaScript中的event.stopPropagation()，防止事件冒泡；
.prevent：等同于JavaScript中的event.preventDefault()，防止执行预设的行为（如果事件可取消，则取消该事件，而不停止事件的进一步传播）；
.capture：与事件冒泡的方向相反，事件捕获由外到内；
.self：只会触发自己范围内的事件，不包含子元素；
.once：只会触发一次

### $emit 、$on 、$once 、$off理解？

$emit	触发当前实例上的自定义事件（并将附加参数都传给监听器回调）。一般用于子组件传数据给父组件。

$on	监听实例上自定义事件并调用回调函数，监听emit触发的事件。用于在父组件接收子组件传来的数据。

$once	监听一个自定义事件，但是只触发一次，在第一次触发之后移除监听器

$off	用来移除自定义事件监听器。如果没有提供参数，则移除所有的事件监听器；如果只提供了事件，则移除该事件所有的监听器；如果同时提供了事件与回调，则只移除这个回调的监听器。

这四个方法的实现原理是：通过对vue实例挂载，然后分别使用对象存储数组对应的函数事件，其中emit通过循环查找存储的数组中对应的函数进行调用，once只匹配一次就就结束，on是将对应的函数存储到数组中，off是删除数组中指定的元素或者所有的元素事件。