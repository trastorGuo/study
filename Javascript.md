[TOC]

### 数据类型

**基本数据类型**：String、Number、undefined、Boolean、Null、Symbol(ES6新增的)。数据直接存储于**栈**的简单数据段，占据空间小，大小固定，属于被频繁使用的数据，因此存储于栈中。

**引用数据类型**：object类型： 对象、数组、函数。占据空间大、大小不固定，如果存储于栈中将会影响性能。实际是在栈中存储了指针，指针指向**堆**中该实体的实际地址。

### Symbol 

[Symbol - ECMAScript 6入门 (ruanyifeng.com)](https://es6.ruanyifeng.com/#docs/symbol)

Symbol 是ES6新增的数据类型， Symbol 值通过`Symbol`函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

```javascript
let s1 = Symbol('foo');
let s2 = Symbol('bar');

s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"

//表示对当前 Symbol 值的描述，因此相同参数的Symbol函数的返回值是不相等的
let s3 = Symbol();
let s4 = Symbol();
let s5 = Symbol('foo');
let s6 = Symbol('foo');

s3==s4;//false
s3===s4;//false
s5===s6;//false

//转字符串
let sym = Symbol('My symbol');
String(sym) // 'Symbol(My symbol)'
sym.toString() // 'Symbol(My symbol)'
Boolean(sym) // true
!sym  // false

//作为属性名
let mySymbol = Symbol();
// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';
// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};
// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
a.mySymbol//undefined  Symbol 值作为对象属性名时，不能用点运算符
```

### NaN

- NaN：Not a Number。表示存储的数据的数据类型，不是一个数值 

  - 非加法运算，只要有NaN参与，执行结果都是NaN

    ```js
    var str1 = '北京';
    var str2 = '上海';
    // 两个变量中存储的数据,都不是数值
    // 执行结果就是NaN
    var res1 = str1 - str2;
    console.log(res1);//NaN
    //非加法运算，有NaN参与,结果就是NaN
    var res2 = 100 - str1;
    console.log(res2);//NaN
    ```

  - 如果是 + 加号，两侧都是数值，执行加法运算；有一个是字符串，就执行字符串拼接操作；只要有NaN参与，执行结果都是NaN

  -  NaN == NaN 的结果是false，因为JavaScript规定，NaN表示的是非数字，但是这个非数字也是不同的，因此 NaN 不等于 NaN，两个NaN永远不可能相等

### let、var、const的区别及用法

都是用于定义变量

- const定义的变量不可以修改，并且必须初始化
- var定义的变量可以修改，如果未初始化则会输出“undefined”，不会报错，在当前作用域内有效
- let类似于var，但只在声明的代码块中有效，为ES6新增的
- let和const即使在全局定义，也不会挂在window上

特别要注意的一点是，es6规定，代码块中存在该变量的let定义，则该块就只能用这个局部变量例如：

```js
console.log(temp);//undefined
var temp = 1;

if(true){
    console.log(temp);//ReferenceError
    console.log(temp2);//undefined
    typeof temp;//ReferenceError
    temp = 2;//ReferenceError
    let temp = 3;//因为该if块中用let定义了temp（无论先后），块中使用的temp变量都为该let定义的局部变量，第5-8行被定义为“暂时性死区”，因为后面用let定义了该变量,只有等到改行出现才能获取和使用此变量
}
```

### 箭头函数

ES6允许使用""箭头""(=>)定义函数

格式：函数名=参数=>代码块

参数：一个参数可以直接写参数名，多个参数需要括号括起来，例如：(x,y)。无参数则直接括号：()。

代码块：如果只有一句返回语句可以直接写语句，例如=>x+y。多个语句则需要使用{}代码块括起来，并且必须有返回(return)语句。

```js
var f=v=>v;
//等价于
var f=function(v){return v;};

var f=()=>{return 1+1};
//等价于
var f = function(){return 1+1};

var f =(x,y)=>x+y;
//等价于
var f = function(x,y){
    return x+y;
};

var f=(x,y)=>{
    var a = x+y;
    return a*y;
}
//等价于
var f = function(x,y){
    var a = x+y;
    return a*y; 
}

//可以简化回调函数
[1,2,3].map(function(x){return x*x});//正常写法
[1,2,3].map(x=>x*x);//箭头函数写法

[1,2,3].sort(function(x,y){return x*x});//正常写法
[1,2,3].sort((x,y)=>x*x);//箭头函数写法
```

### 箭头函数和普通函数的区别

1. 写法不同
2. this值不同
   - 常规函数this指向调用该方法的对象，是动态的
   - 箭头函数的this值始终指向外部函数的this值（箭头函数上下文的this），是静态的
3. 箭头函数无法被当成构造函数，根据new的内容，需要改变函数的this指向，但是箭头函数无法改变（TypeError: Car is not a constructor ）
4. 箭头函数无arguments对象，可以使用剩余参数 (...args)
5. apply和call对箭头函数无效
6. 箭头函数如果没有花括号，并且只有一个表达式，则无需写return。（(num) => num + 1）
7. 箭头函数没有原型对象

### reflow repaint

首先我们要明白的是，页面的显示过程分为以下几个阶段：

1、生成DOM树(包括display:none的节点)

2、在DOM树的基础上根据节点的集合属性(margin,padding,width,height等)生成render树(不包括display:none，head节点，但是包括visibility:hidden的节点)

3、在render树的基础上继续渲染颜色背景色等样式

**reflow**：当render树的一部分或者全部因为大小边距等问题发生改变而需要重建的过程，叫做**回流**。

回流几乎是无法避免的。现在界面上流行的一些效果，比如树状目录的折叠、展开（实质上是元素的显 示与隐藏）等，都将引起浏览器的 reflow。鼠标滑过、点击……只要这些行为引起了页面上某些元素的占位面积、定位方式、边距等属性的变化，都会引起它内部、周围甚至整个页面的重新渲 染。通常我们都无法预估浏览器到底会 reflow哪一部分的代码，它们都彼此相互影响着。

reflow会影响到dom的结构渲染，同时会触发repaint，他会改变他本身与所有父辈元素(祖先)，这种开销是非常昂贵的，导致性能下降是必然的，页面元素越多效果越明显。

**repaint**：如果只是改变某个元素的背景色、文字颜色、边框颜色等等不影响它周围或内部布局的属性，将只会引起浏览器 repaint（重绘）。

repaint 的速度明显快于 reflow

**下面情况会导致reflow发生**

- 改变窗口大小
- 改变文字大小
- 添加或删除可见的 DOM 元素
- 元素位置改变
- 最初的页面渲染
- 内容的改变，如用户在输入框中敲字
- 激活伪类，如:hover
- 操作class属性
- 脚本操作DOM
- 设置style属性
- 获取最新的布局信息（offsetTop, offsetLeft, offsetWidth, offsetHeight ，scrollTop, scrollLeft, scrollWidth, scrollHeight，clientTop, clientLeft, clientWidth, clientHeight，getComputedStyle() （在 IE 中此函数称为 currentStyle））

**下面情况会导致repaint发生**

- 元素的背景颜色，字体颜色修改 （不会改变宽高）

**重绘不一定会导致回流，回流一定导致重绘**

 **如何避免**

- 集中修改样式

  - 尽可能少的修改元素style上的属性　　

  - 尽量通过修改className来修改样式　　

  - 通过cssText属性来设置样式值

    ```js
    var el = document.getElementById('mydiv');
    el.style.borderLeft = '1px';
    el.style.borderRight = '2px';
    el.style.padding = '5px';
    
    //应该优化成以下集中修改
    el.style.cssText = 'border-left: 1px; border-right: 2px; padding: 5px;';
    
    ```

  - 缓存Layout属性值

    ```js
    var left=elem.offsetLeft; //多次使用left也就产生一次reflow 　　
    ```

  - 设置元素的 position属性为absolute或fixed，回流的开销会比较小，因为不用考虑它对其他元素的影响。

    元素脱离标准流，也从DOM树结构中脱离出来，在需要reflow时只需要reflow自身与下级元素 　　

  - css里不要有表达式expression

    避免使用expression,他会每次调用都会重新计算一遍(包括加载页面)　

  - 尽量不要用table布局

    table元素一旦触发reflow就会导致table里所有的其它元素 reflow。在适合用table的场合，可以设置table-layout为auto或fixed，这样可以让table一行一行的渲染，这种做法也是为了限制reflow的影响范围

- 如果需要创建多个DOM节点，可以使用DocumentFragment创建完后一次性的加入document

```js
//也就是先在缓存中把所有要添加的整合好，最后一次性添加
var fragment = document.createDocumentFragment();
fragment.appendChild(document.createTextNode(‘keenboy test 111’));
fragment.appendChild(document.createElement(‘br’));
fragment.appendChild(document.createTextNode(‘keenboy test 222’));
document.body.appendChild(fragment);
```

### 函数表达式

```js
//函数声明,这里申明了一个函数foo，并且后面调用了他
var a = 2;
function foo(){
    var a = 3;
    console.log(a);//3
}
foo();
console.log(a);//2

//函数表达式，是立即执行函数表达式（IIFE）
var a = 2;
(function foo(){
    var a = 3;
    console.log(a);//3
})();//后面跟了个括号，所以会立即执行，并且外部无法访问foo
console.log(a);//2

//传参的函数表达式
var a = 2;
(function foo(i){
    console.log(i);//2
})(a);

//匿名函数表达式
setTimeout(function(){
    console.log("等一秒钟！");
},1000);

//具名函数表达式
setTimeout(function myName(){//看，我有名字了
    console.log("等一秒钟！");
},1000);

```

区分函数声明和表达式最简单的方法是看function关键字出现在声明中的位置（不仅仅是一行代码，而是整个声明中的位置）。如果function是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。

(function foo(){ .. })作为函数表达式意味着foo只能在．．所代表的位置中被访问，外部作用域则不行。foo变量名被隐藏在自身中意味着不会非必要地污染外部作用域。

### 提升

无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。可以将这个过程形象地想象成所有的声明（变量和函数）都会被“移动”到各自作用域的最顶端，这个过程被称为提升。声明本身会被提升，而包括函数表达式的赋值在内的赋值操作并不会提升。

var a = 2 我们习惯将var a = 2；看作一个声明，而实际上JavaScript引擎并不这么认为。它将var a和a = 2当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。

**函数提升优先级高于变量提升。变量提升不会覆盖（同名）函数提升，只有变量再次赋值时，才会被覆盖**

- 变量提升

  变量提升是指将变量申明提升到它所在的作用域的最开始部分

- 函数提升

  **而只有函数声明形式才有函数提升**

  函数创建有两种方式：

  1. 函数申明形式

  2. 函数字面量形式（即函数表达式）还有一种是方式：函数构造法：var a = new Fun()，技术角度来讲也是一个字面量形式。

     ```js
     // 声明式  这种可以提升
     function foo () {
         // to do...
     }
     
     // 函数字面量 这种没有函数提升，和变量提升一样
     var foo = function () {
         // to do...
     }
     ```


### 通过js判断设备

```js
// 判断苹果、安卓、pc
if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) { //判断iPhone|iPad|iPod|iOS
  window.location.href ="iPhone.html";
} else if (/(Android)/i.test(navigator.userAgent)) {  //判断Android
  window.location.href ="Android.html";
} else { //pc
  window.location.href ="pc.html";
};
```

### for与setTimeout 的小知识

```js
for(var i = 1; i <= 5; i++){
    setTimeout(function foo(){
        console.log(i);
    },1000)
}
//输出：66666

for(let i = 1; i <= 5; i++){
    setTimeout(function foo(){
        console.log(i);
    },1000)
}
//输出：12345  
//1、这里涉及到let定义，let是定义块级作用域
//2、从结果可以得出，每迭代一次，就会重新申明一次i（随后的每个迭代都会使用上一个迭代结束时的值来初始化这个变量），因此每一次的跌倒的i都是在自己的作用域中的，所以才会输出12345，也是为什么后面的例子会报错

let i = 1
for(; i <= 5; i++){
    setTimeout(function foo(){
        console.log(i);
    },1000)
}
//输出：报错，提示i已经定义了 SyntaxError: Identifier 'i' has already been declared
//1、let定义的变量不能重复定义
//2、for每迭代一次就会申明一次i

```

### 闭包 

**能够访问另一个函数作用域的变量的函数**

当函数a的内部函数b被函数a 外的一个变量引用的时候，就创建了一个闭包。

通俗来讲，JS所有的function都是一个闭包

```js
function f1(){
     var index=1;
     function f2(){
        alert(index);
        index++; 
     }
     return f2;//这样使得index变成了“全局变量”，因为f2被返回了，不会被回收掉，又使用了index，index也不会被回收
  }
  //调用
  var test=f1(); 
  test(); //1
  test(); //2
  test(); //3
```

闭包的作用

- 就是在a执行完并返回后，闭包使得Javascript的垃圾回收机制GC不会收回a所占用的资源，因为a的内部函数b的执行需要依赖a中的变量。
- 也是隐藏了这个局部变量，不让外部直接访问。（有些情况下需要这样）

**优点**

- 可以读取函数内部的变量
- 可以让这些局部变量保存在内存中，实现变量数据共享。

**缺点**

- 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
- 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

### DOM及基本操作

文档对象模型（DOM, Document Object Model）是语言中立的HTML和XML文档的API。DOMLevel 1将HTML和XML文档定义为一个节点的多层级结构，并暴露出JavaScript接口以操作文档的底层结构和外观。

![image-20210313104856705](https://gitee.com/trastor/picture/raw/master/20210313104857.png)

```js

//创建元素
document.createElement('div');//创建标签
document.createTextNode("123");//创建文本节点
document.createDocumentFragment();//创建虚拟节点，可以理解成一个数组，比如需要新增多个节点，可以先新增到这个变量上，然后直接把这个这个数组添加到某个节点，避免重复的进行dom操作
//查找
var moCode = document.getElementById("moCode");//返回第一个id为moCode的元素  区分大小写
var allEle = document.getElementsByTagName("*");//返回返回所有的标签（注意有s） 
var imgs =   document.getElementsByTagName("img");//返回标签名为"img"的元素列表（注意有s）  不区分大小写
var myImg =  imgs.namedItem("myImg");//从上面的元素列表中获取name为muImg元素
var myName = document.getElementsByName("divName");//返回name为divName的所有元素
var test  =  document.querySelectorAll(".example");//返回文档中匹配指定CSS选择器的所有元素，返回 NodeList 对象。括号内是传选择器的内容，比如“#id”,".class"等
//属性操作
moCode.getAttribute("id");  //获取元素属性值 属性名不区分大小写 常用于自定义属性，自带属性印版直接使用对象属性
moCode.setAttribute("id","newId");//设置元素属性值
moCode.hasAttribute("id");

//添加、移除、替换、插入
moCode.appendChild(node);//将元素添加到子元素末尾
removeChild(node);//移除某个子节点
replaceChild(new,old);//替换某个子节点
insertBefore(new,old);//在new之前插入某个子节点
```

**元素创建的三种方式**

```js
//1、对象.innerHTML="标签及代码";
document.getElementById("dv").innerHTML = "<p>天生我才必有用</p>";

//2、document.write("标签的代码及内容");
//缺陷:如果是在页面加载完毕后,此时通过这种方式创建元素,那么页面上存在的所有的内容全部被干掉
 document.write("<p>这是一个p</p>");

//3、document.createElement("标签的名字");
let div= document.createElement("p");
p.innerText = "这是插入的内容";
document.getElementById("myId").appendChild(p);
```

### MutationObserver 监控dom

使用MutationObserver 可以在dom被修改的时候异步执行回调，可以监控整个问的那个、dom树的一部分、某个元素、元素属性、子节点、文本等。

```js
let observer = new MutationObserver((MutationRecords) => console.log('<body> attributes changed'));
observer.observe(document.body, { attributes: true });
document.body.className = 'foo';
console.log('Changed body class');
// Changed body class
// <body> attributes changed
```

![image-20210313112045781](https://gitee.com/trastor/picture/raw/master/20210313112045.png)

### 浏览器对象模型bom

 BOM部分主要是针对浏览器的内容，其中常用的就是window对象和location，

- window是全局对象很多关于浏览器的脚本设置都是通过它。
- location则是与地址栏内容相关，比如想要跳转到某个页面，或者通过URL获取一定的内容。
- navigator中有很多浏览器相关的内容，通常判断浏览器类型都是通过这个对象。
- screen常常用来判断屏幕的高度宽度等。
- history不太常用，一般应该不会有写关于历史记录的脚本。

![image-20210317143257080](https://gitee.com/trastor/picture/raw/master/20210317143257.png)

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <script type="text/javascript">
            console.log("浏览器名称");
            console.log(navigator.appCodeName);
            console.log("次版本信息");
            console.log(navigator.appMinorVersion);
            console.log("完整的浏览器名称");
            console.log(navigator.appName);
            console.log("浏览器版本");
            console.log(navigator.appVersion);
            console.log("浏览器编译版本");
            console.log(navigator.buildID);
            console.log("是否启用cookie");
            console.log(navigator.cookieEnabled);
            console.log("客户端计算机CPU类型");
            console.log(navigator.cpuClass);
            console.log("浏览器是否启用java");
            console.log(navigator.javaEnabled());
            console.log("浏览器主语言");
            console.log(navigator.language);
            console.log("浏览器中注册的MIME类型数组");
            console.log(navigator.mimeTypes);
            console.log("是否连接到网络");
            console.log(navigator.onLine);
            console.log("客户端计算机操作系统或者CPU");
            console.log(navigator.oscpu);
            console.log("浏览器所在的系统平台");
            console.log(navigator.platform);
            console.log("浏览器中插件信息数组");
            console.log(navigator.plugins);
            console.log("用户的首选项");
            // console.log(navigator.preference());
            console.log("产品名称");
            console.log(navigator.product);
            console.log("产品的次要信息");
            console.log(navigator.productSub);
            console.log("操作系统的语言");
            console.log(navigator.systemLanguage);
            console.log("浏览器的用户代理字符串");
            console.log(navigator. userAgent);
            console.log("操作系统默认语言");
            console.log(navigator.userLanguage);
            console.log("用户个人信息对象");
            console.log(navigator.userProfile);
            console.log("浏览器品牌");
            console.log(navigator.vendor);
            console.log("浏览器供应商次要信息");
            console.log(navigator.vendorSub);
        </script>
    </body>
</html>
```

### with

一种简化对象的赋值的语法，会影响性能，不建议使用。

```js
var a = obj.a;
var b = obj.b;
var c = obj.c;
//可以简化成以下方式
with(obj){
    var a = a;
    var b = b;
    var c = c;
}

//但是如果是这样
obj.a = obj.b;
obj.c = obj.d;

with(obj) {
    a = b;
    c = d;
}
//解释器会首先判断obj.b和obj.d是否存在，如果不存在的话，再判断全局变量b和d是否存在。这样就导致了低效率，而且可能会导致意外，因此最好不要使用with语句。
```

### eval

将字符串作为js语句执行，极低的运算效率，是访问一个简单函数运算执行时间几百倍。eval内容越长，耗费越多时间，不建议使用

```js
eval("myValue = myObject." + myKey + ";");
//影响性能，其实也是可以直接用js语句的
myValue = myObject[myKey];
```

### js内置对象

js 中的内置对象主要指的是在程序执行前存在全局作用域里的由 js 定义的一些**全局值属性**、**函数**和**用来实例化其他对象的构造函数对象**。一般我们经常用到的如全局变量值 NaN、undefined，全局函数如 parseInt()、parseFloat() 用来实例化对象的构造函数如 Date、Object 等，还有提供数学计算的单体内置对象如 Math 对象。

### undefined 与 undeclared

已在作用域中声明但还没有赋值的变量，是 undefined 的。相反，还没有在作用域中声明过的变量，是 undeclared 的。

### arguments

js函数的实际参数会被保存在一个类数组对象arguments中，可以直接在函数内使用例如arguments[0]，则表示传入的第一个实参。

### 使用new时发生了什么

- 首先创建了一个新的空对象 
- 设置原型，将对象的原型设置为函数的 prototype 对象
- 让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）
- 判断函数的返回值类型，如果是值类型，返回创建的对象。如果是引用类型，就返回这个引用类型的对象。

### prototype 、__ proto __ 

**prototype**：原型对象，每个函数都有一个prototype属性，该属性是一个指针，指向一个对象。 而这个对象的用途是包含由特定类型的所有实例共享的属性和方法。使用这个对象的好处就是可以让所有实例对象共享它所拥有的属性和方法

**__ proto __**  ：每个实例对象都有一个__ proto __ 属性，用于指向构造函数的原型对象。 __ proto __属性是在调用构造函数创建实例对象时产生的。

调用构造函数创建的实例对象的__ proto __属性指向构造函数的prototype

**prototype 只有function有**，object以及实例化出来的对象是没有的，他们通过__ proto __来访问构造函数的prototype(原型对象)

![image-20210507110916978](https://gitee.com/trastor/picture/raw/master/20210507110917.png)

### 原型链

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 Object.prototype 所以这就是我们新建的对象为什么能够使用 toString() 等方法的原因。

```js
function Person() {}
var person = new Person();

console.log(person.__proto__ == Person.prototype) // true
console.log(Person.prototype.constructor == Person) // true
console.log(Person.prototype.__proto__ === Object.prototype) // true
console.log(Object.prototype.__proto__ === null) // true
console.log(Object.getPrototypeOf(person) === Person.prototype) // true
```

下图中的蓝色线就是原型链：

![image-20210507111118302](https://gitee.com/trastor/picture/raw/master/20210507111118.png)

### js 获取原型的方法

- p.__ proto __
- p.constructor.prototype
- Object.getPrototypeOf(p)

**instanceof**

instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。

比如：[1,2,3] instanceof Array     //输出true

### 作用域

- 全局作用域

  如果不用var，直接赋值，那么该变量就会直接定义在全局作用域内，如果用了var， 那么就定义在当前作用域内

- 函数作用域

  - 是新的作用域，即使**用var定义，也只是在此函数定义域内有效**
  - 可以通过return 访问函数内部变量，或者通过闭包
  - 立即执行函数(function())()也会形成一个单独的函数作用域

- 块级作用域

  - es6新有的，如果是var，还是在上面两个作用域内
  - 用let或者const可以定义只在块级作用域内有效的变量

- 词法作用域（静态作用域）

  - 静态作用域指的是一段代码，在它执行之前就已经确定了它的作用域，简单来说就是在执行之前就确定了它可以应用哪些地方的作用域(变量)。**例如函数定义在全局，无论在哪个作用域调用函数，它的外面一层作用域依旧是全局**
  - 当我们要使用声明的变量时：JS引擎总会从最近的一个域，向外层域查找
  - **js是采用静态作用域的**

- 动态作用域

  - 函数的作用域是在函数调用的时候才决定的
  - **在js中的仅存的应用动态作用域的地方：`this` 引用**

### js 执行机制

[这一次，彻底弄懂 JavaScript 执行机制 (juejin.cn)](https://juejin.cn/post/6844903512845860872)

javascript是一门单线程语言，不管是什么新框架新语法糖实现的所谓异步，其实都是用同步的方法去模拟的。

**执行过程理解**：执行js代码，如遇到异步代码（setTimeout，setInterval，Promise等）则将该回调函数放到event queue中，待执行完当前代码后，看异步event queue（又分为两种，宏任务event queue和微任务event queue，两者交替被获取来执行）中是否有可执行的任务，有则执行

**宏任务**：包括整体代码script，setTimeout，setInterval等

**微任务**：Promise，process.nextTick等

区分微任务和宏任务是为了将异步队列任务划分优先级，通俗的理解就是为了**插队**。

**事件循环（event loop）**：先执行宏任务 event queue的一个任务，后把所有可执行的微任务的event queue执行了，再执行宏任务，宏任务和微任务交替执行，直到无任务可执行了。

<img src="https://gitee.com/trastor/picture/raw/master/20210325140943.png" alt="image-20210325140943174" style="zoom: 50%;" />

<img src="https://gitee.com/trastor/picture/raw/master/20210325141130.png" alt="image-20210325141130166" style="zoom: 33%;" />

```js
setTimeout(function(){
    console.log('1')
});
new Promise(function(resolve){
    console.log('2');
    resolve();
}).then(function(){
    console.log('3')
});
console.log('4');
//2  4  3  1
```

示例解析：

1. 整体script作为一个宏任务进入主线程
   - 遇到setTimeout，其回调函数被分发到宏任务event queue 中
   - 遇到Promise，new Promise直接执行，输出2，碰到resolve，将then分发到微任务的event queue中
   - 遇到console，输出4
2. 前面执行了一个宏任务，那么现在开始查看微任务中是否有可执行的任务，微任务中只有then，执行，输出3
3. 查看是否有可执行的一个宏任务，有，setTimeout的回调函数，执行，输出1，
4. 因此输出的内容分别是：2  4  3  1

### generator yeild

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同

形式上，Generator 函数是一个普通函数，但是有两个特征:

- function关键字与函数名之间有一个星号
- 函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
hw.next()
// { value: 'hello', done: false }
hw.next()
// { value: 'world', done: false }
hw.next()
// { value: 'ending', done: true }
hw.next()
// { value: undefined, done: true }
```

**yield**

由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。遍历器对象的`next`方法的运行逻辑如下。

- 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
- 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
- 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
- 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

需要注意的是，yield表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

**next的参数**

`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

```
function* dataConsumer() {
  console.log('Started');
  console.log(`1. ${yield}`);
  console.log(`2. ${yield}`);
  return 'result';
}
let genObj = dataConsumer();
genObj.next();
// Started
genObj.next('a');
// 1. a
genObj.next('b');
// 2. b
```

### async/await

async和await是Generator和yield+自动执行器（执行next方法）的语法糖，是promise 的升级版本

在async函数中使用await，那么await这里的代码就会变成同步的了，意思就是说只有等await后面的Promise执行完成得到结果才会继续下去，await就是等待，这样虽然避免了异步，但是它也会阻塞代码，所以使用的时候要考虑周全。

- 凡是在前面添加了async的函数在执行后都会自动返回一个Promise对象
- await必须在async函数里使用，不能单独使用
- await后面需要跟Promise对象，不然就没有意义，而且await后面的Promise对象不必写then，因为await的作用之一就是获取后面Promise对象成功状态传递出来的参数
- 我们通过给包裹await的async函数添加then/catch方法来解决，因为根据规则1，async函数本身就会返回一个Promise对象

和promise的区别：

- promise 通过链式调用，直接在 then 中返回一个 promise 来进行成功之后的回调函数，用 catch 来做错误处理。
  async/await 则直接将其变成了同步的写法，既可以用.catch又可以用try-catch捕捉，简洁，可读性强，写法更加优雅 。
- 如果是三个步骤（三次异步），后面的步骤需要前面的结果，那么async可以很好地拿到前面的值，但是promise就没那么方便了（见下面代码1）
- promise.all里面的promise是一起执行的，不过返回数组结果是按照all里面的顺序。async是按照代码顺序执行的。

```js
//代码1
// async方式
async function doIt() {
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time1, time2);
    const result = await step3(time1, time2, time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}
doIt();
 // promise方式
function doIt() {
    const time1 = 300;
    step1(time1)
        .then(time2 => {
            return step2(time1, time2)
                .then(time3 => [time1, time2, time3]);
        })
        .then(times => {
            const [time1, time2, time3] = times;
            return step3(time1, time2, time3);
        })
        .then(result => {
            console.log(`result is ${result}`);
        });
}

```

```js
function test() {
    return new Promise((resolve) => {
        setTimeout(resolve, 1000);
    });
}
async function hah() {
    console.log(3);
    await test();
    console.log(4);
    return 5;
}
console.log(1);
hah().then(v=>console.log(v));
console.log(2)
//1 3 2 4 5
```

### apply 、call、bind 

用来修改函数中this的指向问题

call(this,param)、apply(this,[])、bind(this,param)(); //第一个参数传需要修改后的this对象，后面传参数。

apply、call、bind三者作用基本一样，只是参数的传递方式不同，不过bind是返回一个函数，并不会执行，因此后面需要跟()来执行此函数。

```js
var name = 'Evan';
var age = 20;
var person = {
    name: 'Hillary',
    age: 19,
    sayIntroduce: function () {
        return "Hello, My name is " + this.name + " and I'm " + this.age + ' years old.'
    },
    sayHobby: function (val1, val2) {
        return "I'm " + this.name + ", I like " + val1 + " and " + val2 + ".";
    }
}
var person1 = {
    name: 'Coy'
}
console.log(person.sayIntroduce()); // Hello, My name is Hillary and I'm 19 years old.
console.log(person.sayIntroduce.call(person1)); // Hello, My name is Coy and I'm undefined years old.
console.log(person.sayHobby.call(person1, 'swimming', 'hiking')); // I'm Coy, I like swimming and hiking.
console.log(person.sayHobby.apply(person1, ['swimming', 'hiking'])); // I'm Coy, I like swimming and hiking.
console.log(person.sayHobby.bind(person1, 'swimming', 'hiking')()); // I'm Coy, I like swimming and hiking.
```

### 防抖、节流

**防抖**：防止在一定时间内频繁操作，那么就通过防抖来解决，**只有在一定时间内无操作了才响应**。

![image-20210401151629847](https://gitee.com/trastor/picture/raw/master/20210401151630.png)

示例代码

```js
// 防抖
function debounce(fn, wait) {    
    var timeout = null;    
    return function() {        
        if(timeout !== null)   clearTimeout(timeout);        
        timeout = setTimeout(fn, wait);    
    }
}
// 处理函数
function handle() {    
    console.log(Math.random()); 
}
// 滚动事件
window.addEventListener('scroll', debounce(handle, 1000));
```

**节流**：当持续触发事件时，保证在一定时间段内只调用一次事件处理函数。类似于水龙头，关紧后，让它稳定的固定时间间隔的滴一滴水。

![image-20210401151931171](https://gitee.com/trastor/picture/raw/master/20210401151931.png)

```js
// 节流throttle代码（定时器）：
var throttle = function(func, delay) {            
    var timer = null;            
    return function() {                
        var context = this;               
        var args = arguments;                
        if (!timer) {                    
            timer = setTimeout(function() {                        
                func.apply(context, args);                        
                timer = null;                    
            }, delay);
        }            
    }        
}        
function handle() {            
    console.log(Math.random());        
}        
window.addEventListener('scroll', throttle(handle, 1000));
```

**总结**

**函数防抖**：将几次操作合并为一次操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

**函数节流**：使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

**区别**： 函数节流不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而函数防抖只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流技术来实现。

![image-20210423154622641](https://gitee.com/trastor/picture/raw/master/20210423154622.png)

### 垃圾回收、内存泄露

JavaScript垃圾回收的机制很简单：找出不再使用的变量，然后释放掉其占用的内存，但是这个过程不是实时的，因为其开销比较大，所以垃圾回收器会按照固定的时间间隔周期性的执行。

**垃圾回收机制**

- **标记清除**：最常用的方式，当变量进入执行环境时，就标记这个变量为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到他们。当变量离开环境时，则将其标记为“离开环境”。垃圾收集器完成内存清除工作，销毁那些带标记的值，并回收他们所占用的内存空间。
- **引用计数**：语言引擎有一张"引用表"，保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是0，就表示这个值不再用到了，因此可以将这块内存释放。但是如果一个值不再需要了，引用数却不为0，垃圾回收机制无法释放这块内存，从而导致内存泄漏。

**哪些情况回引起内存泄露**

- 意外的全局变量：没被申明，使用了，则会被定义成全局变量。可以加严格模式，这样就不会自动被定义成全局变量了
- 被遗忘的计时器或回调函数
- 闭包：闭包内引用的变量会一直在被引用，无法被释放。
- 没有清理的DOM元素引用：dom对象呗保存在json中，但当dom数呗移除后，json中的引用还存在

**内存泄露的识别方法**

新版本的chrome在 performance 中查看

**避免内存泄露的方式**

- 减少不必要的全局变量、或者生命周期较长的对象，及时对无用的数据进行垃圾回收

- 注意程序逻辑，避免“死循环”之类的

- 避免创建过多的对象（在程序逻辑ok的情况下，对象多复用）

  总而言之一句话：能少用对象则少用，不用了的东西记得及时还

### 数组方法大全

|   方法名    | 对应版本 | 原数组是否改变 | 使用方式                                                     | 描述                                                         |
| :---------: | :------: | :------------: | ------------------------------------------------------------ | :----------------------------------------------------------- |
|   concat    |   ES5-   |       n        | [1,2,3].concat([3,4,5])                                      | 合并数组，并返回合并之后的数据                               |
|    join     |   ES5-   |       n        | [1,2,3].join("-")                                            | 使用分隔符，将数组转为字符串并返回                           |
|     pop     |   ES5-   |       y        | arr.pop()                                                    | **删除**最后一位，并返回删除的数据                           |
|    shift    |   ES5-   |       y        | arr.shift()                                                  | 删除第一位，并返回删除的数据                                 |
|   unshift   |   ES5-   |       y        | [2,3,4].unshift(1)                                           | 在**第一位新增**一或多个数据，返回长度                       |
|    push     |   ES5-   |       y        | [2,3,4].push(1)                                              | 在最后一位新增一或多个数据，返回长度                         |
|   reverse   |   ES5-   |       y        | [1,2,3].reverse()                                            | **反转**数组，返回结果                                       |
|    slice    |   ES5-   |       n        | [1,2,3].slice(1,3)                                           | 截取指定位置的数组，slice(开始下标, 结束下标(不包含该下标))并返回 |
|    sort     |   ES5-   |       y        | [1,2,3].sort()                                               | 排序（字符规则,先tostring），返回结果                        |
|   splice    |   ES5-   |       y        | [1,2,3,4,5].splice(1,2,6);//[1,6,4,5]                        | **删除、插入**。splice(开始下标，数量，插入的数据)删除指定位置，并替换，返回删除的数据 |
|  toString   |   ES5-   |       n        | [1,2,3].toString()                                           | 直接转为字符串，逗号分隔，相当于没有参数的join，并返回       |
|   valueOf   |   ES5-   |       n        | str.valueOf()                                                | 返回数组对象的原始值                                         |
|   indexOf   |   ES5    |       n        | ["q","w","e","r"].indexOf("w")                               | 查询并返回数据的索引                                         |
| lastIndexOf |   ES5    |       n        | ["q","w","e","r"].lastIndexOf("w")                           | 反向查询并返回数据的索引                                     |
|   forEach   |   ES5    |       n        | [1,2,3].forEach((value,index,self)=>{})                      | 参数为回调函数，会遍历数组所有的项，回调函数接受三个参数，分别为value，index，self；forEach没有返回值 |
|     map     |   ES5    |       n        | var a = [1,2].map(function(value,index,self){return "hi:"+value;})//a = ["hi:1","hi:2"] | 同forEach，同时回调函数返回数据，组成新数组由map返回         |
|   filter    |   ES5    |       n        | var a = [1,2].filter(function(value,index,self){return value>1;})//a = [2] | 回调函数返回布尔值，为true的数据组成新数组由filter返回       |
|    fill     |   ES5    |       y        | [1,2,3].fill(6,1,3)//[1,6,6]                                 | fill() 方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引(值，开始索引(默认0)，结束索引(不包含，默认长度)) |
|    every    |   ES5    |       n        | var a = [1,2].every(function(value,index,self){return value<2;})//a = false | 回调函数返回布尔值，全部为true，由every返回true,碰到不符合的，直接返回false，不继续往后遍历了 |
|    some     |   ES5    |       n        | var a = [1,2].every(function(value,index,self){return value<2;})//a = true | 回调函数返回布尔值，只要由一个为true，由some返回true，只要碰到符合的，直接返回true，不继续往后遍历了 |
|   reduce    |   ES5    |       n        |                                                              | 归并，同forEach，迭代数组的所有项，并构建一个最终值，由reduce返回 |
| reduceRight |   ES5    |       n        |                                                              | 反向归并，同forEach，迭代数组的所有项，并构建一个最终值，由reduceRight返回 |

### 字符串常用的操作方法

charAt（）：获取字符串索引值处的字符，索引值从0开始
indexOf（）：返回指定字符第一次出现的索引值
lastIndexOf（）：返回指定字符最后一次出现的索引值
substring（）：根据索引值截取字符串，索引值只能为正数
slice（）：同样用于截止字符串，且可为负数
split（）：根据指定分隔符分割字符串
replace（）：将字符串中的某个字符串用特定字符串替换，只会替换第一个
concat（）：连接多个字符串
toUpperCase（）：将字符串中的所有字符转为大写字母
match（）：使用正则表达式搜索字符串，存在放回该字符串，不存在返回null
search（）：使用正则表达式搜索字符串，存在返回索引值，不存在返回-1

### reduce

reduce()方法对数组中的每个元素执行一个由您提供的**reducer**函数(升序执行)，将其结果汇总为单个返回值。

```js
//arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue]);
//Accumulator (acc) (累计器)
//Current Value (cur) (当前值)
//Current Index (idx) (当前索引)
//Source Array (src) (源数组)
//initialValue 初始值

//求数组中所有的和
var sum = [0, 1, 2, 3].reduce((accumulator, currentValue) =>accumulator + currentValue, 0);

//累加对象数组中的值
var sum = [{x: 1}, {x:2}, {x:3}].reduce((accumulator, currentValue) => accumulator + currentValue.x,0);

//将二维数组转换成一维数组
var flattened = [[0, 1], [2, 3], [4, 5]].reduce(
  function(a, b) {
    return a.concat(b);
  },[]);
//数组去重
let myArray = ['a', 'b', 'a', 'b', 'c', 'e', 'e', 'c', 'd', 'd', 'd', 'd']
let myOrderedArray = myArray.reduce(function (accumulator, currentValue) {
  if (accumulator.indexOf(currentValue) === -1) {
    accumulator.push(currentValue)
  }
  return accumulator
}, [])
```

### js常用方法

```js
parseInt("9.9", 10) //9 //字符串，进制（不是默认10，有问题，建议写上10）解析一个字符串并返回指定基数的整数
parseFloat('3.14');//3.14 返回一个浮点数

Boolean(value)//把给定的值转换成Boolean型；
Number(value)//把给定的值转换成数字（可以是整数或浮点数）；
String(value)//把给定的值转换成字符串。
```



### 数组扁平化

数组扁平化是指将一个多维数组变为一维数组

```
[1, [2, 3, [4, 5]]]  ------>    [1, 2, 3, 4, 5]
```

核心思想：遍历数组arr，若arr[i]为数组则递归遍历，直至arr[i]不为数组然后与之前的结果concat。

- toString/join + split

```
function flatten(arr) {
    return arr.toString().split(',').map(function(item) {
        return Number(item);
    })
} 
```

- 递归的遍历每一项，若为数组则继续遍历，否则concat

  ```js
  function flatten(arr) {
      var res = [];
      arr.map(item => {
          if(Array.isArray(item)) {
              res = res.concat(flatten(item));
          } else {
              res.push(item);
          }
      });
      return res;
  }
  ```

- 扩展运算符

  ```js
  [].concat(...[1, 2, 3, [4, 5]]);  // [1, 2, 3, 4, 5]
  
  function flatten(arr) {
      while(arr.some(item=>Array.isArray(item))) {
          arr = [].concat(...arr);
      }
      return arr;
  }
  ```

- flat

  flat() 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

  ```
  const arr1 = [0, 1, 2, [[[3, 4]]]];
  console.log(arr1.flat(3)); // 3 代表数组内最多嵌套层数
  // [0, 1, 2, 3, 4]
  ```


### 数组乱序

- 使用sort

  ```js
  const shuffle2 = () => {
      const arr = [0, 1, 2, 3, 4];
      arr.sort(() => Math.random() > .5);
      return arr;
  };
  ```

- 洗牌算法

  ```js
  /***
   * 1、 从数组末尾开始， 选取最后一个元素。  
   * 2、在数组所有位置中， 随机产生一个位置， 该位置元素与最后一个元素进行交换
   * 3、上一步中， 我们已经把数组末尾元素进行随机置换。接下来， 对数组倒数第二个元素动手。 在除去已经排好的最后一个元素位置以外的位置中， 随机产生一个位置， 该位置元素与倒数第二个元素进行交换。
   * 4、 理解了前两部， 接下来就是依次进行， 如此简单
   */
  arr = [0, 1, 2, 3, 4, 5];
  for (let i = arr.length; i > 0;) {
      let index = Math.floor(Math.random() * (i--));
      [arr[i], arr[index]] = [arr[index], arr[i]];
      console.log(i, index);
  }
  console.log(arr);
  ```


### 扩展运算符

对象中的扩展运算符(...)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中

```js
//可以用于对象的复制，不会被引用对象所影响
var arr1 = {a:1,b:2};
var arr2 = {...arr1};
arr2.a = 2;
//arr1 = {a:1,b:2}  arr2 = {a:2,b:2}

[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

### 事件机制

[JavaScript事件三部曲之事件机制的原理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/73091706)

- DOM Level 0（原始事件类型）  多个会发生覆盖

  ```js
  //1、html属性
  <input onclick="alert('xxx')"/>
  
  //2、js
  var btn = document.getElementById('btn');
   btn.onclick = function(){
       alert('xxx');
   }
   //或者
   window.onload = function(){
    document.write("Hello world!");
  };
  
  //解除事件
  btn.onclick = null
  ```

- DOM Level 2，通过 addEventListener 绑定事件   绑定多个会按照顺序执行，removeEventListener解除绑定

  ```js
  var btn = document.getElementById('btn');
  var fn = function(){};
  btn.addEventListener( "click" ,fn,false);
  btn.removeEventListener( "click" ,fn,false);
  ```

  第一个参数是事件名（如click）；第二个参数是事件处理程序函数；第三个参数如果是true的话表示在捕获阶段调用，为false的话表示在冒泡阶段调用，默认false

  | 事件类型    | 捕获过程 | 冒泡过程 |
  | ----------- | -------- | -------- |
  | DOM Level 0 | 不支持   | 支持     |
  | DOM Level 2 | 支持     | 支持     |
  | IE          | 不支持   | 支持     |

**冒泡和捕获**

```js
//阻止捕获和冒泡阶段中当前事件的进一步传播。
//但是，它不能防止任何默认行为的发生； 例如，对链接的点击仍会被处理。
event.stopPropagation();
```

当一个事件发生后，会在子元素和父元素之间传播（propagation）。这种传播分成三个阶段：

1. 捕获阶段：事件从window对象自上而下向目标节点传播的阶段(1~5)
2. 目标阶段：真正的目标节点正在处理事件的阶段(5~6)
3. 冒泡阶段：事件从目标节点自下而上向window对象传播的阶段(6~10)

![image-20210402110107190](https://gitee.com/trastor/picture/raw/master/20210402110107.png)

```js
<div>
  <div id="parent">
    父元素
    <div id="child">子元素</div>
  </div>
</div>
var parent = document.getElementById('parent');
var child = document.getElementById('child');
// 通过 addEventListener 指定事件的绑定
// 第三个参数 true / false 分別代表 捕获/ 冒泡 机制
parent.addEventListener('click', function () {
  console.log('Parent Capturing');
}, true);
parent.addEventListener('click', function () {
  console.log('Parent Bubbling');
}, false);
child.addEventListener('click', function () {
  console.log('Child Capturing');
}, true);
child.addEventListener('click', function () {
  console.log('Child Bubbling');
}, false);
//点击子元素后，输出内容为：
//Parent Capturing
//Child Capturing
//Child Bubbling
//Parent Bubbling

//注意，目标元素的捕获和冒泡事件执行的前后是取决于两者的代码顺序先后。
```

### 事件委托/事件代理

利用事件冒泡原理（即子元素被触发某个事件后，那么相对应所有的父元素的改事件都会被触发，一层一层的触发），如果需要对某些有特定标识的子元素添加时间监听，那么就可以直接给父元素添加，也就是把事件委托给父元素。

```js
<div id="box">
    <input type="button" id="add" value="添加" />
    <input type="button" id="remove" value="删除" />
    <input type="button" id="move" value="移动" />
    <input type="button" id="select" value="选择" />
</div>

//不用事件委托，那么就需要对每个子元素操作，多次的dom操作会影响性能。
window.onload = function(){
    var Add = document.getElementById("add");
    var Remove = document.getElementById("remove");
    var Move = document.getElementById("move");
    var Select = document.getElementById("select");
    Add.onclick = function(){
        alert('添加');
    };
    Remove.onclick = function(){
        alert('删除');
    };
    Move.onclick = function(){
        alert('移动');
    };
    Select.onclick = function(){
        alert('选择');
    }
}

//使用事件委托 则只需要对dom一次操作
window.onload = function(){
    var oBox = document.getElementById("box");
    oBox.onclick = function (ev) {
        var ev = ev || window.event;
        var target = ev.target || ev.srcElement;//标准浏览器用ev.target，IE浏览器用event.srcElement
        if(target.nodeName.toLocaleLowerCase() == 'input'){
            switch(target.id){
                case 'add' :
                    alert('添加');
                    break;
                case 'remove' :
                    alert('删除');
                    break;
                case 'move' :
                    alert('移动');
                    break;
                case 'select' :
                    alert('选择');
                    break;
            }
        }
    }
}
```

### token、session、cookie 

**token**：适用于项目级的前后端分离（前后端代码运行在不同的服务器下），请求登录时，token和sessionId原理相同，是对key和key对应的用户信息进行加密后的加密字符，登录成功后，会在响应主体中将{token：'字符串'}返回给客户端。客户端通过cookie、sessionStorage、localStorage都可以进行存储。再次请求时**不会默认携带**，需要在请求拦截器位置给请求头中添加认证字段Authorization携带token信息，服务器端就可以通过token信息查找用户登录状态。1，

**cookie**： 仅仅是浏览器实现的一种数据存储功，可以存在浏览器上（关掉浏览器则会被删除），也可以存在客户端本地文件中（可以设置有效时间），一般可以将服务器传来的sessionid存储在cookie中，cookie是根据域名分类的，只要该域名有http请求，那么就会自动将cookie放在请求头上。

**session**：在服务端，服务端可以根据请求中的sessionId在服务器上是有有效来判断是否登录或者得到相关信息，session默认呗存储在文件中，也可以存在数据库，或者内存（会导致当服务器重启时，所有session都会消失）。

session的运行依赖于cookie（是通过cookie存储在客户端的），传给浏览器后会自动存储在cookie中，因此如果浏览器禁用了cookie，同时 session 也会失效（但是可以通过其它方式实现，比如在 url 中传递 session_id）

### cookie、localStorage、sessionStorage

```js
localStorage.setItem(key,value);
localStorage.keyName = value;
localStorage.getItem(key);
localStorage.removeItem(key);
localStorage.clear();
localStorage.key(index);
//sessionStorage一样的
//当我们想在 localStorage 和 sessionStorage 中存储对象时，可以先用 JSON.stringify(obj) 方法将对象转换为字符串，然后进行存储。要想将值作为对象输出可以用  JSON.parse(str) 方法将字符串转换为对象。

document.cookie = "username=sam;"
```

共同点：

- 都是保存在本地浏览器端。

区别：

- cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递；cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。
- 存储大小限制不同：cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
- 数据有效期不同：sessionStorage仅在当前标签页关闭前有效，当该标签页关闭后数据也会被删除；localStorage：持久化的本地存储，保存的数据没有过期时间，直到手动去除；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
- 作用域不同：sessionStorage 仅在当前标签页有效，即使是在同一浏览器中新建一标签页打开同一网站也不会共享；localStorage 在同一浏览器中的同一网站上都是共享的；cookie 可以设置域名及路径，实现同一域名或者目录共享。三者都是在不同浏览器下保存的位置可能不一样，都不能跨浏览器共享。

![image-20210418214805000](https://gitee.com/trastor/picture/raw/master/20210418214805.png)

### setTimeout、setInterval

```js
var a= setTimeout(function, milliseconds, param1, param2, ...);//属于宏任务,返回值是个id
clearTimeout(a);
var b = setInterval(function, milliseconds, param1, param2, ...)//属于宏任务
clearInterval(b);
```

### ...  rest参数

解决函数参数个数不确定，rest参数是真实的数组实例。例如数组sort、map、forEach、pop的方法都可以直接使用。

```js
function f(...[a, b, c]) {
  return a + b + c;
}
f(1)          //NaN 因为只传递一个值，其实需要三个值
f(1, 2, 3)    // 6
f(1, 2, 3, 4) // 6 (第四值没有与之对应的变量名)

function fun1(...theArgs) {
  console.log(theArgs.length);
}
fun1();  // 0
fun1(5); // 1
fun1(5, 6, 7); // 3
```

### this

[Javascript 的 this 用法 - 阮一峰的网络日志 (ruanyifeng.com)](https://ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

函数的不同使用场合，`this`有不同的值。总的来说，`this`就是函数运行时所在的环境**对象**。下面分四种情况，详细讨论`this`的用法

- **纯粹的函数调用** 这时候this对应的是全局对象

  ```js
  var x = 1;
  function test() {
     console.log(this.x);
  }
  test();  // 1
  
  //setTimeOut和setInterval里的函数，因为是直接调用，所以也是指向全局
  function setTimeout(fn,delay) { //可以这样通过伪代码理解
  // 等待delay 毫秒
  fn(); // <-- 调用位置！ 直接全局调用，因此是指向全局的
  }
  ```

- 箭头函数的this

  ```js
  //也可以通过使用箭头函数来固定（它的指向是所在的上下文，固定的）
  //箭头函数没有自己的this对象，内部的this就是一个普通变量，指向定义时上层函数所在的对象
  //下面是 Babel 转箭头函数产生的 ES5 代码，就能清楚地说明this的指向。
  // ES6
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }
  // ES5
  function foo() {
    var _this = this;
  
    setTimeout(function () {
      console.log('id:', _this.id);
    }, 100);
  }
  
  
  //1、diameter是普通函数，里面的this指向直接调用它的对象obj。perimeter是箭头函数，this应该指向上下文函数this的指向，这里上下文没有函数对象，就默认为window，而window里面没有radius这个属性，就返回为NaN。
  const obj = {
    radius: 10,  
    diameter() {    
        return this.radius * 2
    },  
    perimeter: () => 2 * Math.PI * this.radius
  }
  console.log(obj.diameter())    // 20
  console.log(obj.perimeter())    // NaN
  
  //2、hello直接调用者是obj，第一个this指向obj，setTimeout箭头函数，this指向最近的函数的this指向，即也是obj
  const obj = {
      num: 10,
     hello: function () {
      console.log(this);    // obj
      let that = this;
      setTimeout(() => {
        console.log(this);    // obj
        console.log(that);    // 和上一行是一个意思
      });
     }    
  }
  obj.hello();
  ```

  

- **作为对象的方法的调用** 这时候this对应的就是该对象

  ```js
  function test() {
    console.log(this.x);
  }
  var x= 2;
  var obj = {};
  obj.x = 1;
  obj.m = test;
  
  obj.m(); // 1  this对应的是obj这个对象
  ```

- **作为构造函数调用** 这时候this指向这个新对象，因为new的时候会将this指向新对象

  ```js
  var x = 2;
  function test() {
    this.x = 1;
  }
  
  var obj = new test();
  x  // 2
  ```

- **apply/call的调用** this执行apply的第一个参数（不传默认全局）

  ```js
  var x = 0;
  function test() {
  　console.log(this.x);
  }
  
  var obj = {};
  obj.x = 1;
  obj.m = test;
  obj.m.apply() // 0
  obj.m.apply(obj); //1
  ```

### JS原始HTTP请求

```js
    
//get
var httpRequest = new XMLHttpRequest();//第一步：建立所需的对象
httpRequest.open('GET', 'url', true);//第二步：打开连接  将请求参数写在url中  ps:"./Ptest.php?name=test&nameone=testone"
httpRequest.send();//第三步：发送请求  将请求参数写在URL中
httpRequest.onreadystatechange = function () {
	if (httpRequest.readyState == 4 && httpRequest.status == 200) {
          var json = httpRequest.responseText;//获取到json字符串，还需解析
          console.log(json);
        }
 };
//post
 var httpRequest = new XMLHttpRequest();
    httpRequest.open("POST",url,true);
    httpRequest.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
    httpRequest.send("keyword="+keyword+"&name="+name);
    httpRequest.onreadystatechange = ()=>{
        if(httpRequest.readyState == 4 && httpRequest.status == 200){
            var data = JSON.parse(httpRequest.responseText);
            console.log(data);
        }
    } 

```

### set、map

set 类似于数组，只是成员的值都是唯一的，没有重复的值。

```js
const s = new Set();
[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
for (let i of s) {
  console.log(i);
}
// 2 3 5 4

const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size; // 5
items.has(1);
items.delete(2);
items.clear();
Array.from(items)  或者  [...items]  //转数组

let set = new Set(['red', 'green', 'blue']);
for (let item of set ) {
  console.log(item);
}
// red
// green
// blue

// 去除数组的重复成员
[...new Set([1, 2, 3, 4, 5, 5, 5, 5])]  或者 Array.from(new Set([1, 2, 3, 4, 5, 5, 5, 5]));
// 去除重复的字符
[...new Set('ababbc')].join('')
```

map是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，Map 是一种更完善的 Hash 结构实现

```
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"


const map = new Map();
map.set(1, 'aaa');
map.get(1);
```

### js 异步加载

defer、async

- defer  ：异步请求js，当有返回后，浏览器**不会暂停html的解析**，解析完成后再执行返回的js代码。同时也会按照定义的defer 顺序请求js，不会破坏js的依赖

```
<script type="text/javascript" defer src="test.js"></script>
```

- async：异步请求js，如果js有返回，**会中断**html的解析后来执行js代码，执行完成后再继续解析html，不会按照代码的顺序请求js。

```
<script type="text/javascript" async src="test.js"></script>
```

### requestAnimationFrame

在Web应用中，实现动画效果的方法比较多**，Javascript 中可以通过定时器 setTimeout 来实现，css3 可以使用 transition 和 animation 来实现，html5 中的 canvas 也可以实现。除此之外，html5 还提供一个专门用于请求动画的API，那就是 requestAnimationFrame**

requestAnimationFrame的用法与settimeout很相似，只是不需要设置时间间隔而已。requestAnimationFrame使用一个回调函数作为参数，这个回调函数会在浏览器重绘之前调用。它返回一个整数，表示定时器的编号，这个值可以传递给**cancelAnimationFrame**用于取消这个函数的执行。

requestAnimationFrame采用系统时间间隔，保持最佳绘制效率，不会因为间隔时间过短，造成过度绘制，增加开销；也不会因为间隔时间太长，使用动画卡顿不流畅，让各种网页动画效果能够有一个统一的刷新机制，从而节省系统资源，提高系统性能，改善视觉效果

- requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率
- 在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的CPU、GPU和内存使用量
- requestAnimationFrame是由浏览器专门为动画提供的API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了CPU开销

```
//控制台输出1和0
var timer = requestAnimationFrame(function(){
    console.log(0);
}); 
console.log(timer);//1
```

现在分别使用setInterval、setTimeout和requestAnimationFrame这三个方法制作一个简单的进度条效果

```html
<div id="myDiv" style="background-color: lightblue;width: 0;height: 20px;line-height: 20px;">0%</div>
<button id="btn">run</button>

<script>
var timer;
btn.onclick = function(){
    clearInterval(timer);
    myDiv.style.width = '0';
    timer = setInterval(function(){
        if(parseInt(myDiv.style.width) < 500){
            myDiv.style.width = parseInt(myDiv.style.width) + 5 + 'px';
            myDiv.innerHTML =     parseInt(myDiv.style.width)/5 + '%';    
        }else{
            clearInterval(timer);
        }        
    },16);
}
</script>

<script>
var timer;
btn.onclick = function(){
    clearTimeout(timer);
    myDiv.style.width = '0';
    timer = setTimeout(function fn(){
        if(parseInt(myDiv.style.width) < 500){
            myDiv.style.width = parseInt(myDiv.style.width) + 5 + 'px';
            myDiv.innerHTML =     parseInt(myDiv.style.width)/5 + '%';
            timer = setTimeout(fn,16);
        }else{
            clearTimeout(timer);
        }    
    },16);
}
</script>

<script>
var timer;
btn.onclick = function(){
    myDiv.style.width = '0';
    cancelAnimationFrame(timer);
    timer = requestAnimationFrame(function fn(){
        if(parseInt(myDiv.style.width) < 500){
            myDiv.style.width = parseInt(myDiv.style.width) + 5 + 'px';
            myDiv.innerHTML =     parseInt(myDiv.style.width)/5 + '%';
            timer = requestAnimationFrame(fn);
        }else{
            cancelAnimationFrame(timer);
        }    
    });
}
</script>
```

### 柯里化

柯里化（currying）指的是将一个多参数的函数拆分成一系列函数，每个拆分后的函数都只接受一个参数（unary）。

```js
function fn(...args){
    if(args.length ==0) return;
    function add(arr){
        return arr.reduce((pre,cur)=>pre+cur);
    }   
    let total = add(args);
    function k(...args){
        total += add(args);
        return k;
    }
    k.toString = ()=>total;//console.log一个函数的引用是会用函数的.toString() 来得到函数的字符串定义
    return k;
}
console.log(fn(1,2,3,4));
console.log(fn(1,2)(2)(5));
console.log(fn(1,2,3,4)(6));
```

### 继承

[JavaScript深入之继承的多种方式和优缺点 · Issue #16 · mqyqingfeng/Blog (github.com)](https://github.com/mqyqingfeng/Blog/issues/16)