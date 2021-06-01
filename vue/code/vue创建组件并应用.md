### Vue创建组件并应用

#### 1、写组件

```vue
//components/common/food/food.vue
<template>
    <div>
        这是food组件
    </div>
</template>
```

#### 2、组件独立的调用文件

```js
//components/common/food/index.js
import foodComponent from './food.vue'
const Food =  {
    install:function(Vue){
        Vue.component('Food-Test',foodComponent);//'Food-Test'则是之后组件的使用名称
    }
}
export default Food;
```

#### 3、引入和注册组件

```js
//main.js
import Food from './components/common/food';
Vue.use(Food);
```

#### 4、使用组件

```vue
<template>
    <div>
        <Food-Test> </Food-Test>
    </div>
</template>
```
