## Flux

简单介绍：FLus是facebook使用的一套前端应用的架构模式，核心理念是**单向数据流**，是一种模式而非框架

![image-20210113162942448](https://gitee.com/trastor/picture/raw/master/20210114135723.png)

flux主要是三个部分：**dispatcher、store、view**

- dispatcher负责分发事件
- store负责保存数据，同时响应事件并更新数据
- view负责订阅store中的数据，并使用这些数据渲染相应的页面

前提：

- store告诉dispatcher，只要有action产生就通知它
- view从store中获取最新的state
- 当view接收到来自store的state时，会将其传递给子view去渲染
- view让store在state改变时通知它
- 只能通过dispatcher来修改store的state
- state不会暴露setter

数据流动

1. 用户在界面进行交互，做了一些操作，产生了一个action
2. dispatcher收到action，并且按照先后顺序将action传递给store
3. 每一个store都会收到所有的action通知，然后自行判断是否需要对此action做出响应，更新state
4. 一旦store更新完毕state，就会告知订阅了该state的view
5. view向store请求更新的state，并就最新的state传递给对应的子view
6. 子view渲染最新的state

因此便形成了数据和逻辑一直是单向数据流动的，避免了混乱的数据流动

**Flux的不足**

虽然Flux的中心化控制十分优雅。但是它最大的问题就是Flux的冗余代码太多。虽然Flux源码中几乎只有dispatcher的实现，但是在每个应用中都需要手动创建一个dispatcher的实例，而且在一个应用中含有多个store。
