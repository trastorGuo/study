[TOC]

### 同源政策

一个域下的 js 脚本在未经允许的情况下，不能够访问另一个域的内容。这里的同源的指的是两个域的协议、域名、端口号必须相同，否则则不属于同一个域。
同源政策主要限制了三个方面

- 当前域下的 js 脚本不能够访问其他域下的 cookie、localStorage 和 indexDB。
- 当前域下的 js 脚本不能够操作访问操作其他域下的 DOM。
- 当前域下 ajax 无法发送跨域请求。

同源政策的目的主要是为了保证用户的信息安全，它只是对 js 脚本的一种限制

### 跨域

浏览器从一个域名的网页去请求另一个域名的资源时，二级域名、域名、端口、协议任一不同，都是跨域

[九种跨域方式实现原理（完整版） (juejin.cn)](https://juejin.cn/post/6844903767226351623)

**解决跨域问题**

- jsonp跨域

  在html页面中通过相应的标签从不同域名下加载静态资源文件是被浏览器允许的，所以我们可以通过这个“犯罪漏洞”来进行跨域。一般，我们可以动态的创建script标签，再去请求一个带参网址来实现跨域通信

  JSONP只支持GET请求

  ```js
  //原生的实现方式
  let script = document.createElement('script');
  script.src = 'http://www.nealyang.cn/login?username=Nealyang&callback=callback';
  document.body.appendChild(script);
  function callback(res) {
    console.log(res);
  }
  ```

- document.domain + iframe跨域

- window.name + iframe跨域

- location.hash + iframe跨域

- postMessage跨域

  window.postMessage(message,targetOrigin) 方法是html5新引进的特性，可以使用它来向其它的window对象发送消息，无论这个window对象是属于同源或不同源

  示例：`http://localhost:3000/a.html`页面向`http://localhost:4000/b.html`传递“我爱你”,然后后者传回"我不爱你"。

  ```
  // a.html
    <iframe src="http://localhost:4000/b.html" frameborder="0" id="frame" onload="load()"></iframe> //等它加载完触发一个事件
    //内嵌在http://localhost:3000/a.html
      <script>
        function load() {
          let frame = document.getElementById('frame')
          frame.contentWindow.postMessage('我爱你', 'http://localhost:4000') //发送数据
          window.onmessage = function(e) { //接受返回数据
            console.log(e.data) //我不爱你
          }
        }
      </script>
   
   // b.html
    window.onmessage = function(e) {
      console.log(e.data) //我爱你
      e.source.postMessage('我不爱你', e.origin)
   }
  ```

- 跨域资源共享（CORS）

  简单请求

  ```
  (1) 请求方法是以下三种方法之一：
       HEAD
       GET
       POST
  （2）HTTP的头信息不超出以下几种字段：
       Accept
       Accept-Language
       Content-Language
       Last-Event-ID
       Content-Type：
       application/x-www-form-urlencoded、 multipart/form-data、text/plain
  ```

  - 会在Request Header中添加 **Origin** **（协议 + 域名 + 端口）**字段
  - CORS接收到此次请求后 ， 首先会判断**Origin**是否在**允许源**（由服务端决定）范围之内，如果验证通过，服务端会在Response Header 添加 Access-Control-Allow-Origin、Access-Control-Allow-Credentials等字段
  - 浏览器收到Respnose后会判断自己的源是否存在 Access-Control-Allow-Origin允许源中，如果不存在，会抛出“同源检测异常”

  非简单请求

  进行非简单请求时候 ， 浏览器会首先发出类型为OPTIONS的“预检请求”，请求地址相同 ，
  CORS服务端对“预检请求”处理，并对Response Header添加验证字段，客户端接受到预检请求的返回值进行一次请求预判断，验证通过后，主请求发起。

- nginx代理跨域

- WebSocket协议跨域

### HTTP状态码

HTTP状态码分类

| 分类 | 分类描述                                       |
| :--- | :--------------------------------------------- |
| 1**  | 信息，服务器收到请求，需要请求者继续执行操作   |
| 2**  | 成功，操作被成功接收并处理                     |
| 3**  | 重定向，需要进一步的操作以完成请求             |
| 4**  | 客户端错误，请求包含语法错误或无法完成请求     |
| 5**  | 服务器错误，服务器在处理请求的过程中发生了错误 |

- 301：永久重定向；302：临时重定向；304：缓存是否有效
- 403，理解但拒绝请求，一般是文件未授权；404：资源不存在

### HTTP Referer

"引荐人"，http请求头一个字段，提供访问来源的信息，比如a标签跳转，那么Referer则会显示当前页面url。

[HTTP Referer 教程 - 阮一峰的网络日志 (ruanyifeng.com)](http://www.ruanyifeng.com/blog/2019/06/http-referer.html)

### http和https的区别

超文本传输协议http是明文传输，所以当请求被拦截时，用户输入的信息可能会被泄露，比如说银行卡号和密码；

安全套接字层超文本传输协议为了保证数据安全的传输，https在http的基础上加了**SSL/TLS**的协议，SSL/TLS依靠证书验证服务器的身份，并未浏览器和服务器之间的通信加密；

https的**作用**主要有两种：

- 建立一个信息安全通道，保证数据的安全性，
- 确认网站的真实性；

客户端在使用HTTPS与web服务器之间**通信的步骤**：

- 用户使用https的URL与web服务器建立SSL连接；
- web服务器接收到证书的请求后会将我们网站的证书信息发送给浏览器；
- 浏览器接与web服务器开始协商ssl安全等级
- 按照约定好的安全等级，建立会话秘钥，然后利用网站的公钥信息加密会话，服务器利用私钥信息解密会话；

### https握手过程

1. 浏览器将自己支持的一套加密规则发送给网站
2. 网站从中选出一组加密算法与HASH算法，并将自己的身份信息以证书的形式发回给浏览器。证书里面包含了网站地址，加密公钥，以及证书的颁发机构等信息
3. 浏览器获得网站证书之后浏览器要做以下工作：
   - 验证证书的合法性（颁发证书的机构是否合法，证书中包含的网站地址是否与正在访问的地址一致等），如果证书受信任，则浏览器栏里面会显示一个小锁头，否则会给出证书不受信的提示
   - 如果证书受信任，或者是用户接受了不受信的证书，浏览器会生成一串随机数的密码，并用证书中提供的公钥加密
   - 使用约定好的HASH算法计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给网站。
4. 网站接收浏览器发来的数据之后要做以下的操作：
   -  使用自己的私钥将信息解密取出密码，使用密码解密浏览器发来的握手消息，并验证HASH是否与浏览器发来的一致
   -  使用密码加密一段握手消息，发送给浏览器
   -  浏览器解密并计算握手消息的HASH，如果与服务端发来的HASH一致，此时握手过程结束，之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密

![image-20210511104515306](https://gitee.com/trastor/picture/raw/master/20210511104515.png)

### GET、POST的区别

- get用来获取数据，post用来提交数据
- get参数有长度限制（受限于url长度，具体的数值取决于浏览器和服务器的限制，最长2048字节），而post无限制。
- get请求的数据会附加在url之 ，以 " ？ "分割url和传输数据，多个参数用 "&"连接，而post请求会把请求的数据放在http请求体中。
- get是明文传输，post是放在请求体中，但是开发者可以通过抓包工具看到，也相当于是明文的。
- get请求会保存在浏览器历史记录中，还可能保存在web服务器的日志中
- GET请求只会产生一个TCP包，也就是说浏览器会把所有的请求数据（header和data）一起发出去，服务器响应200时成功
  POST 请求“可能” 会产生两个TCP包，第一次浏览器会先发送header，服务器响应100后，再次发送data

### HTTP1.0 和HTTP1.1的区别

- **带宽优化**：HTTP1.0中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，HTTP1.1则在请求头引入了range头域，它允许只请求资源的某个部分，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接

- **缓存处理**：在HTTP1.0中主要使用header里的If-Modified-Since,Expires来做为缓存判断的标准，HTTP1.1则引入了更多的缓存控制策略来控制

  ```
  Entity tag：资源的匹配信息
  If-Unmodified-Since：比较资源最后的更新时间是否不一致
  If-Match：比较 ETag 是否一致
  If-None-Match：比较 ETag 是否不一致
  ```

- **Host头处理**：在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址，因此，请求消息中的URL并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且它们共享一个IP地址。HTTP1.1的请求消息和响应消息都应支持Host头域，且请求消息中如果没有Host头域会报告一个错误（400 Bad Request）

- **长连接**：HTTP 1.1支持长连接（PersistentConnection）和请求的流水线（Pipelining）处理，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟，在HTTP1.1中默认开启Connection： keep-alive，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点

- **错误通知的管理**：在 HTTP1.1 中新增了 24 个错误状态响应码，如 409（Conflict）表示请求的资源与资源的当前状态发生冲突；410（Gone）表示服务器上的某个资源被永久性的删除。

- **新增请求方式**

  ```
  OPTIONS：请求查询服务器的性能，或者查询与资源相关的选项和需求
  CONNECT：保留请求以供将来使用
  TRACE：请求服务器回送收到的请求信息，主要用于测试或诊断
  ```

### HTTP1.x的现存的问题

- HTTP1.x在传输数据时，每次都需要重新建立连接，无疑增加了大量的延迟时间，特别是在移动端更为突出。
- HTTP1.x在传输数据时，所有传输的内容都是明文，客户端和服务器端都无法验证对方的身份，这在一定程度上无法保证数据的安全性。
- HTTP1.x在使用时，header里携带的内容过大，在一定程度上增加了传输的成本，并且每次请求header基本不怎么变化，尤其在移动端增加用户流量。
- 虽然HTTP1.x支持了keep-alive，来弥补多次创建连接产生的延迟，但是keep-alive使用多了同样会给服务端带来大量的性能压力，并且对于单个文件被不断请求的服务(例如图片存放网站)，keep-alive可能会极大的影响性能，因为它在文件被请求之后还保持了不必要的连接很长时间。

### HTTP1.x 和HTTP2.0的区别

- **二进制分帧**

  HTTP1.x 的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认 0 和 1 的组合。基于这种考虑 HTTP2.0 的协议解析决定采用二进制格式，实现方便且健壮。

  HTTP2.0 在 应用层(HTTP2.0)和传输层(TCP/UDP)之间增加一个二进制分帧层。在不改动 HTTP1.X 的语义、方法、状态码、URI 以及首部字段的情况下, 解决了 HTTP1.1 的性能限制，改进传输性能，实现低延迟和高吞吐量。在二进制分帧层中，HTTP2.0 会将所有传输的信息分割为更小的消息和帧（frame）,并对它们采用二进制格式的编码 ，其中 HTTP1.X 的首部信息会被封装到 HEADER frame，而相应的 Request Body 则封装到 DATA frame 里面。

  - 帧：HTTP2.0 数据通信的最小单位消息：指 HTTP2.0 中逻辑上的 HTTP 消息。例如请求和响应等，消息由一个或多个帧组成。
  - 流：存在于连接中的一个虚拟通道。流可以承载双向消息，每个流都有一个唯一的整数 ID

- **多路复用（MultiPlexing）**

  即连接共享，即每一个request都是是用作连接共享机制的。一个request对应一个id，这样一个连接上可以有多个request，每个连接的request可以随机的混杂在一起，接收方可以根据request的 id将request再归属到各自不同的服务端请求里面

  HTTP2.0 可以很容易的去实现多流并行而不用依赖建立多个 TCP 连接，同个域名只需要占用一个 TCP 连接，消除了因多个 TCP 连接而带来的延时和内存消耗。HTTP2.0 把 HTTP 协议通信的基本单位缩小为一个一个的帧，这些帧对应着逻辑流中的消息。并行地在同一个 TCP 连接上双向交换消息。

- **header 压缩**

  HTTP1.x 的 header 带有大量信息，而且每次都要重复发送，HTTP2.0 使用 HPACK 算法对 header 的数据进行压缩，减少需要传输的 header 大小，通讯双方各自 cache 一份 header fields 表，差量更新 HTTP 头部，既避免了重复 header 的传输，又减小了需要传输的大小。

  header 采取的压缩策略：
  - HTTP2.0 在客户端和服务器端使用“首部表”来跟踪和存储之前发送的键－值对，对于相同的数据，不再通过每次请求和响应发送
  - 首部表在 HTTP2.0 的连接存续期内始终存在，由客户端和服务器共同渐进地更新
  - 每个新的首部键－值对要么被追加到当前表的末尾，要么替换表中之前的值

- **服务端推送（server push）**

  服务端推送是一种在客户端请求之前发送数据的机制

  服务端可以在发送页面 HTML 时主动推送其它资源，而不用等到浏览器解析到相应位置，发起请求再响应。例如服务端可以主动把 JS 和 CSS 文件推送给客户端，而不需要客户端解析 HTML 时再发送这些请求

  服务器端推送的这些资源其实存在客户端的某处地方，客户端直接从本地加载这些资源就可以了，不用走网络，速度自然是快很多的

### XSS攻击

攻击，是一种代码注入攻击。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等，进而危害数据安全。其重点是『**跨域**』和『**客户端执行**』。利用script标签的jsonp原理，就可以加载攻击者自己的js文件，从而获取网页关键信息。

解决方案

- 将内容进行转义，将<script>标签中的左右括号进行转义

- `HttpOnly` 最早是由微软提出，并在 `IE 6` 中实现的，至今已经逐渐成为一个标准，各大浏览器都支持此标准。具体含义就是，如果某个 `Cookie` 带有 `HttpOnly` 属性，那么这一条 `Cookie` 将被禁止读取，也就是说，`JavaScript` 读取不到此条 `Cookie`，不过在与服务端交互的时候，`Http Request` 包中仍然会带上这个 `Cookie` 信息，即我们的正常交互不受影响。

  