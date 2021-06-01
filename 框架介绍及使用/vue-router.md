[TOC]

将组件(components)映射到路由(routes)

### vue-router路由模式

```js
可以通过修改路由配置来切换，默认hash。
mode:"hash";  
mode:"history";
```

**hash模式**：

- 兼容所有浏览器，包括不支持 HTML5 History Api 的浏览器
- 例http://www.abc.com/#/index，hash值为#/index， hash的改变会触发hashchange事件，通过监听hashchange事件来完成操作实现前端路由
- hash值变化不会让浏览器向服务器请求

**history路由**

```
history.go(-2);//后退两次
history.go(2);//前进两次
history.back(); //后退
hsitory.forward(); //前进
```

- 兼容能支持 HTML5 History Api 的浏览器，依赖HTML5 History API来实现前端路由
- 没有`#`，路由地址跟正常的url一样
- 但是初次访问或者刷新都会向服务器请求，如果没有请求到对应的资源就会返回404，所以路由地址匹配不到任何静态资源，则应该返回同一个index.html 页面，需要在nginx中配置，或者进行重定向。

为了避免出现这种情况，所以这个实现需要服务器的支持，需要把所有路由都重定向到根页面或者返回同一个html页面

### 手动实现单页面  

- 通过addEventListener监听hashchange或者load事件来修改页面内容

```html
<ul>
    <li>
        <a href="#/">home</a>
    </li>
    <li>
        <a href="#/about">about</a>
    </li>
    <li>
        <a href="#/topics">topics</a>
    </li>
</ul>
<div id="content"></div>
<script>
    class Router {
        constructor() {
            this.routes = {};
            this.currentUrl = '';
        }
        route(path, callback) {
            this.routes[path] = callback || function () { };
        }
        updateView() {
            this.currentUrl = location.hash.slice(1) || '/';
            this.routes[this.currentUrl] && this.routes[this.currentUrl]();
        }
        init() {
            window.addEventListener('load', this.updateView.bind(this), false);
            window.addEventListener('hashchange', this.updateView.bind(this), false);
        }
    }
    const router = new Router();
    router.init();
    router.route('/', function () {
        document.getElementById('content').innerHTML = 'Home';
    });
    router.route('/about', function () {
        document.getElementById('content').innerHTML = 'About';
    });
    router.route('/topics', function () {
        document.getElementById('content').innerHTML = 'Topics';
    });
</script>
```

### 组件按需加载

把一些js模块给独立出一个个js文件，然后需要用到的时候，在创建一个script对象，加入到document.head对象中即可

- **vue异步组件技术**
  vue-router配置路由，使用vue的异步组件技术，可以实现按需加载。
  但是，这种情况下一个组件生成一个js文件。

```js
export default new Router({
    routes: [
        {
            path:'/grid',
            component:resolve=>require(['@/components/pages/Grid'],resolve),
            name:'Grid'
        },
        {
            path:'/accord',
            component:resolve=>require(['@/components/pages/Accord'],resolve),
            name:'Accord'
        }
    ]
})
```

- **ES6 的import**

  ```js
  
  // 下面2行代码，没有指定webpackChunkName，每个组件打包成一个js文件。
  const Grid = ()=>import('@/components/pages/Grid')
  const Accord = ()=>import('@/components/pages/Accord')
   
  // 下面2行代码，指定了相同的webpackChunkName，会合并打包成一个js文件。
  const Grid = () => import(/* webpackChunkName: 'ImportFuncs' */ '@/components/pages/Grid')
  const Accord = () => import(/* webpackChunkName: 'ImportFuncs' */ '@/components/pages/Accord')
   
  export default new Router({
      routes: [
          {
              path: '/grid',
              name: 'Grid',
              component: Grid
          },
          {
              path: '/accord',
              name: 'Accord',
              component: Accord
          }
      ]
  })
  ```

- **webpack提供的require.ensure()**

  ```js
  export default new Router({
      routes: [
          {
              path:'/grid',
              component: resolve => require.ensure([], () => resolve(require('@/components/pages/Grid')), 'ImportFuncs'),
              name:'Grid'
          },
          {
              path:'/accord',
              component: resolve => require.ensure([], () => resolve(require('@/components/pages/Accord')), 'ImportFuncs'),
              name:'Accord'
          }    
          ]
  })
  //都会打包到指定的chunkName。这里是“ImportFuncs”
  ```

### vue-router有几种钩子函数

- 全局前置守卫

  beforeEach、afterEach、beforeResolve 

```
const router = new VueRouter({})
router.beforeEach((to, from, next) = {
  // to do somethings
  next();
})
//* next 方法必须调用，否则钩子函数无法resolved

router.afterEach((to, from) = {
 // to do somethings
 //后置钩子并没有next函数，也不会改变导航本身
})

router.beforeResolve((to, from, next) = {
  // to do somethings
  next();
})
//在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用
```

to:Route,代表要进入的目标，它是一个路由对象。

from:Route,代表当前正要离开的路由，也是一个路由对象

next:Function,必须需要调用的方法，具体的执行效果则依赖next方法调用的参数

1. next():进入管道中的下一个钩子，如果全部的钩子执行完了，则导航的状态就是comfirmed（确认的）
2. next(false):终端当前的导航。如浏览器URL改变，那么URL会充值到from路由对应的地址。
3. next('/')||next({path:'/'}):跳转到一个不同的地址。当前导航终端，执行新的导航。

- 路由独享的钩子

  ```
  cont router = new VueRouter({
      routes: [
          {
              path: '/file',
              component: File,
              beforeEnter: (to, from ,next) => { 
                  // do someting 
              }
          }
      ]
  });
  ```

- 组件内的导航钩子

  ```js
  //参数或查询的改变并不会触发进入/离开的导航守卫,会进入beforeRouteUpdate
  const File = {
      template: `<div>This is file</div>`,
      beforeRouteEnter(to, from, next) {
          // do someting
          // 在渲染该组件的对应路由被 confirm 前调用
      },
      beforeRouteUpdate(to, from, next) {
          // do someting
          // 在当前路由改变，但是依然渲染该组件是调用 
      }，
      beforeRouteLeave(to, from ,next) {
          // do someting
          // 导航离开该组件的对应路由时被调用
          //这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消。
      }
}
  ```

  注意：beforeRouteEnter 不能获取组件实例 this，因为当守卫执行前，组件实例被没有被创建出来，剩下两个钩子则可以正常获取组件实例 this

  但是并不意味着在 beforeRouteEnter 中无法访问组件实例，我们可以通过给 next 传入一个回调来访问组件实例。在导航被确认时，会执行这个回调，这时就可以访问组件实例了

  最后是完整的导航解析流程：
  
  1. 导航被触发
  2. 在失活的组件里调用离开守卫beforeRouteLeave
  3. 调用全局的 beforeEach 守卫
  4. 在重用的组件里调用 beforeRouteUpdate 守卫（参数或查询的改变才会进来）
  5. 在路由配置里调用 beforEnter
  6. 解析异步路由组件
  7. 在被激活的组件里调用 beforeRouteEnter
  8. 调用全局的 beforeResolve 守卫
  9. 导航被确认
  10. 调用全局的 afterEach 钩子
  11. 触发 DOM 更新
  12. 在创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数

### 题目

#### 如何实现404页面配置

- ‘*’ 为通配符，匹配所有路径。redirect:重定向到404页面
- 必须将此路由配置放到最后，否则无法实现该功能。

```js
{
    name:'404',
    path:'/404.html',
    component: resolve => require(['../page/NotFound/view.vue'], resolve),
},
{
     path:'*',
     redirect:{
         name:"404"
     }
}
```

通配符还可以这么用

```js
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}

//当使用一个通配符时，$route.params 内会自动添加一个名为 pathMatch 参数。它包含了 URL 通过通配符被匹配的部分：
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```

#### 获取路由传过来的参数

路由有三种传参方式，获取方式各不相同。

- meta：路由元信息，写在routes配置文件中。

  ```
  {
      path: '/home',
      name: 'home',
      component: load('home'),
      meta: {
          title: '首页'
      },
  },
  ```

  获取方式	this.$route.meta.title

- query

  ```
  this.$route.push({
      path:'/home',
      query:{
          userId:123
      }
  })
  ```

  浏览器地址：`http://localhost:8036/home?userId=123`

  获取方式：this.$route.query.userId

- params：这种方式比较麻烦

  - 首先要在地址上做配置

    ```
    {
        path: '/home/:userId',
        name: 'home',
        component: load('home'),
        meta: {
            title: '首页'
        },
    },
    ```

  - 访问传参

    ```
    const userId = '123'
    this.$router.push({ name: 'home', params: { userId } })
    ```

    注意用params传参，只能用命名的路由（用name访问），如果用path，params不起作用。

    ```
    this.$router.push({ path: '/home', params: { userId }})//不生效。
    ```

  - 浏览器地址：`http://localhost:8036/home/123`

  - 获取方式：`this.$route.params.userId`

#### 	回到上一个路由的原先滚动的位置

- 可以在路由的meta中加入参数scrollTop
- 然后在全局守卫beforeEach中设置`from.meta.scrollTop = document.documentElement.scrollTop || documnet.body.scrollHeight`
- 记录下来后下次回到该页面就可以访问到了