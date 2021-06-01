[TOC]

## promise

promise是异步编程的一种解决方案，比传统的解决方案回调函数更加合理和强大，能够解决“回调地狱”问题。

promise有三个状态，Pending(进行中)、Resolved(已完成)、Rejected(已拒绝、失败)，状态只能从进行中->已完成或者进行中->已拒绝，一旦变化将永远不能在改变。

### **特点**

- 将异步的操作以同步操作的流程表达出来，避免了层层嵌套的回调函数，流程更加清晰，代码更优雅
- Promise提供了统一的接口，使得异步操作更加容易

### **缺点**

- 无法取消promise，一单新建就会立即执行，无法中途取消
- 如果不设置回调函数，内部抛出错误不会反应到外部
- 当处于pending状态时，无法得知目前进展到哪一个阶段了

### **方法**

#### **all**  

- 传入promise数组
- 当所有的都是“fulfilled” 返回结果数组到then
- 有一个结果是“rejected”的时候，返回这个结果值给catch

#### **race**

- 传入promise数组，只要有一个有返回，则直接将返回值传递给then或者catch

#### **allSettled**

- 传入promise数组，只有所有的promise都成功返回了，才返回，返回一个对象数组

  ```js
  [
      { status: 'fulfilled', value: 42 },
      { status: 'rejected', reason: -1 }
  ]
  ```

#### **any**

- 只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态  并且传递该返回值给then
- 如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态 并且返回“AggregateError: All promises were rejected”给catch

#### **resolve**

将现有对象转为 Promise 对象，该实例的状态为resolve。

```javascript
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

#### **reject**

`Promise.reject(reason)`方法也会返回一个新的 Promise 实例，该实例的状态为rejected。

```javascript
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了
```

### **注意的点**

- .then 或者 .catch 的参数期望是函数，传入非函数则会发生值穿透。

  ```
  如果传过来是个非函数，然后当前then也是非函数，机会忽略掉当前then
  Promise.resolve(1)
    .then(2)//非函数，被忽略，1继续往下传
    .then(Promise.resolve(3))//非函数，被忽略，1继续往下传
    .then(console.log) //是函数， 传入1
  //1
  ```

- Promise 新建后就会立即执行

- promise内部执行了resolve或者reject后，后面的非resolve/reject内容还会继续执行，除非直接retrun

- `then`方法返回的是一个新的`Promise`实例

- `promise`抛出一个错误，就被`catch()`方法指定的回调函数捕获。等同于reject。（要注意是抛出 throw，如果是resolve(new Error("错误"))，那么依旧会去then）

- Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获

- Promise 会吃掉错误，如果promise内有错误（比如某变量未定义），浏览器会输出错误，但是不会中但程序，后面代码照样执行

- .finally(()=>{..})，无论成功与否都会执行

- ```javascript
  all：
  1、P1、P2、P3 如果不是promise对象，会自动调用Promise.resolve()转成promise对象
  2、有reject则会变成rejected，所有都resolve才会变成resolved
  Promise.all([p1,p2,p3]).then(function (posts) {
  }).catch(function(reason){
  });
  
  race：
  只要有某个状态改变，则该对象的状态也改变进入then
  Promise.race([p1,p2,p3]).then(function (posts) {
  }).catch(function(reason){
  });
  
  Promise.resolve('foo')
  // 等价于
  new Promise(resolve => resolve('foo'))
  
  const p = Promise.reject('出错了');
  // 等同于
  const p = new Promise((resolve, reject) => reject('出错了'))
  ```

### **示例**

```js
   new Promise(function (resolve, reject) {
        console.log("come p1");
        setTimeout(() => {
            console.log("come settimeout");
            reject("p1的reject");
        }, 2000);
    }).then((x) => {
        console.log("then:" + x);
    }, (x) => {
        console.log("catch:" + x);
    });
//come p1
//come settimeout
//catch:p1的reject
```

### 手写简单的promise

简单的promise，但是还不能够用连续的then。暂时只支持一个then。

```js
class myPromise {
    constructor(fn) {
        this.onResolveList = [];
        this.onRejectList = [];
        this.status = "pending";
        let resolve = (val) => {
            this.status = "fulfilled";
            this.value = val;
            this.onResolveList.forEach(x => x());
        };
        let reject = (val) => {
            this.status = "rejected";
            this.value = val;
            //将保存下来的数组执行
            this.onRejectList.forEach(x => x());
        };
        fn(resolve, reject);
    }
    then(onresolve, onReject) {
        if (this.status == "fulfilled") {
            onresolve(this.value);
        }
        else if (this.status == "rejected") {
            onReject(this.value);
        }
        else {
            //因为异步的原因，所以需要整个数组保存下来
            this.onResolveList.push(() => {
                onresolve(this.value);
            });
            this.onRejectList.push(() => {
                onReject(this.value);
            });
        }
    }
}
var myP = new myPromise(function (resolve, reject) {
    setTimeout(function () {
        resolve("成功的值");
    }, 2000)
}).then(function (value) {
    console.log("then:", value);
}, function (value) {
    console.log(value);
}) ;
```

### 实现all方法

```js
function promiseAll(promises) {
  return new Promise(function(resolve, reject) {
    if (!Array.isArray(promises)) {
      return reject(new TypeError('arguments must be an array'));
    }
    var resolvedCounter = 0;
    var promiseNum = promises.length;
    var resolvedValues = new Array(promiseNum);
    for (var i = 0; i < promiseNum; i++) {
      (function(i) {
        Promise.resolve(promises[i]).then(function(value) {
          resolvedCounter++
          resolvedValues[i] = value
          if (resolvedCounter == promiseNum) {
            return resolve(resolvedValues)
          }
        }, function(reason) {
          return reject(reason)
        })
      })(i)
    }
  })
} 
```

### 实现race方法

```js
Promise.newRace = function (promiseArr) {
  return new Promise((resolve, reject) => {
    let i = 0, n = 0;
    // 执行所有 Promise
    while (n < promiseArr.length) {
      promiseArr[n].then(res => {
        // 出现第一个被 resolve 的直接 resolve
        resolve(res);
      }).catch(err => {
        // 出现第一个被 reject 的直接 reject
        reject(err);
      });
      n++;
    }
  })
};
```
