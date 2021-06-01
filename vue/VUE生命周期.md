### vue执行顺序图

### new Vue()  生命周期

- 初始化阶段
  - 把`Vue`实例赋值给变量`vm`，并且把用户传递的`options`选项与当前构造函数的`options`属性及其父级构造函数的`options`属性进行合并，得到一个新的`options`选项赋值给`$options`属性，并将`$options`属性挂载到`Vue`实例上。
  - 调用各种初始化函数 、数据观测 (data observer)

```javascript
initLifecycle(vm)       // 初始化生命周期  给实例初始化了一些属性，包括以$开头的供用户使用的外部属性，也包括以_开头的供内部使用的内部属性。
initEvents(vm)        // 初始化事件  用来初始化实例的事件系统
initRender(vm)         // 初始化渲染  
callHook(vm, 'beforeCreate')  // 调用生命周期钩子函数
initInjections(vm)   //初始化injections 初始化inject 
initState(vm)    // 初始化props,methods,data,computed,watch
initProvide(vm) // 初始化 provide
callHook(vm, 'created')  // 调用生命周期钩子函数
```

- 模版编译阶段。

  vm.$mount(),//获取到用户传入的模板内容并将其编译成渲染函数

- 挂载阶段

  - 将模板渲染到视图上（用vue实例替换`el`选项对应的`DOM`元素）
  - 开启对模板中数据（状态）的监控，当数据（状态）发生变化时通知其依赖进行视图更新

- 销毁阶段

  vm.$destory。将当前的`Vue`实例从其父级实例中删除，取消当前实例上的所有依赖追踪并且移除实例上的所有事件监听器

**Vue父子组件生命周期执行顺序**

- 加载渲染过程

  `父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted`

- 子组件更新过程

  `父beforeUpdate->子beforeUpdate->子updated->父updated`

- 父组件更新过程

  `父beforeUpdate->父updated`

- 销毁过程

  `父beforeDestroy->子beforeDestroy->子destroyed->父destroyed`

**父组件监听子组件生命周期**

```js
//1、使用on和emit
// Parent.vue
<Child @mounted="doSomething"/>
    
// Child.vue
mounted() {
  this.$emit("mounted");
}

//2、hook钩子函数
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>
doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
},
    
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},    
    
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...
```

<img src="https://gitee.com/trastor/picture/raw/master/20210405194833.png" alt="image-20210405194833232" style="zoom: 67%;" />

<img src="https://gitee.com/trastor/picture/raw/master/20210114135733.png" alt="image-20201020162350492" style="zoom:50%;" />