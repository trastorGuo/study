### axios

#### 官方文档

[axios中文文档|axios中文网 | axios (axios-js.com)](http://www.axios-js.com/zh-cn/docs/)

### 封装

axios的post 参数名称为data，get参数名称为params，因此小小封装下，统一传参 

```js
//src\http\http.js
//finally() 方法返回一个Promise。在promise结束时，无论结果是fulfilled或者是rejected，都会执行指定的回调函数。这为在Promise是否成功完成后都需要执行的代码提供了一种方式。这避免了同样的语句需要在then()和catch()中各写一次的情况。
require('promise.prototype.finally').shim();
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
  return config;
}, function (error) {
  return Promise.reject(error);
});

//添加响应拦截器
axios.interceptors.response.use(function (response) {
  return response;
}, function (error) {
  return Promise.reject(error);
});

/**
 * 封装axios的通用请求
 * @param  {string}   method     get\post\put\delete
 * @param  {string}   url       请求的接口URL
 * @param  {object}   param     传的参数，没有则传空对象
 */
function http(method, url, param) {
  param = param && typeof param === 'object' ? param : {};
  const config = {
    url: url,
    method: method,
    baseURL: '...',
    transformRequest: [function (param) {
      return Qs.stringify(param);//将参数序列化
    }],
    headers: {
      'X-Requested-With': 'XMLHttpRequest',
      "accessToken": token == null ? "" : token,
    },
    timeout: 30000,
  };
  // post请求时需要设定Content-Type
  if (method === 'post') {
    config.headers['Content-Type'] = 'application/x-www-form-urlencoded; charset=UTF-8';
    config.data = param;
  } else if (method === 'get') {
    config.params = param;
  }
  return axios(config);
}
export {
  http
};
```

```js
//src\main.js
import { http } from './http/http';
Vue.prototype.$http = http;
```

```js
//使用
this.$http("get", "/api/product/GetProductList")
    .then((response) => {
    console.log(response);
})
    .catch((err) => {
    console.log(err.message);
})
    .finally(() => {
    console.log("无论成功与否都会执行");
});
```

