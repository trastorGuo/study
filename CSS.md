[TOC]



### 定位（position）

#### 总概

static：position默认值，根据代码顺序显示，top等属性无效。

relative：定位基点是元素的默认位置（原始位置），通过top等属性指定偏移方向。

absolute：定位基点是**父元素**（不能是static定位，否则定位基点就会变成整个网页的根元素html）的默认位置（原始位置），通过top等属性指定偏移方向，**不占文档流**。

fixed：定位基点是浏览器窗口，通过top等属性指定偏移方向。

inherit：继承父元素的定位属性值。

#### static 

是position的默认值，浏览器会按照源码的顺序，决定每个元素的位置，这称为"正常的页面流"（normal flow）。每个块级元素占据自己的区块（block），元素与元素之间不产生重叠，这个位置就是元素的默认位置

注意，static定位所导致的元素位置，是浏览器自主决定的，所以这时top、bottom、left、right这四个属性无效

relative、absolute、fixed这三个属性值有一个共同点，都是相对于某个基点的定位，不同之处仅仅在于基点不同

#### relative

相对于默认位置（即static时的位置）进行偏移，即**定位基点是元素的默认位置**

它必须搭配top、bottom、left、right这四个属性一起使用，用来指定偏移的方向和距离

```css
div {
  position: relative;
  top: 20px;
}
```

#### absolute

相对于上级元素（一般是父元素）进行偏移，即**定位基点是父元素**。

相对于 static 定位以外的第一个父元素进行定位。另外，absolute定位也必须搭配top、bottom、left、right这四个属性一起使用。

absolute定位的元素会被"正常页面流"忽略，即在"正常页面流"中，**该元素所占空间为零**，周边元素不受影响。

#### fixed

fixed表示，相对于视口（viewport，浏览器窗口）进行偏移，即**定位基点是浏览器窗口**。这会导致元素的位置不随页面滚动而变化，好像固定在网页上一样。

它如果搭配top、bottom、left、right这四个属性一起使用，表示元素的初始位置是基于视口计算的，否则初始位置就是元素的默认位置。

#### sticky

sticky跟前面四个属性值都不一样，它会产生动态效果，很像relative和fixed的结合：一些时候是relative定位（定位基点是自身默认位置），另一些时候自动变成fixed定位（定位基点是视口）。

因此，它能够形成"动态固定"的效果。比如，网页的搜索工具栏，初始加载时在自己的默认位置（relative定位）。页面向下滚动时，工具栏变成固定位置，始终停留在页面头部（fixed定位）。等到页面重新向上滚动回到原位，工具栏也会回到默认位置。

sticky生效的前提是，必须搭配top、bottom、left、right这四个属性一起使用，不能省略，否则等同于relative定位，不产生"动态固定"的效果。原因是这四个属性用来定义"偏移距离"，浏览器把它当作sticky的生效门槛。

它的具体规则是，当页面滚动，父元素开始脱离视口时（即部分不可见），只要与sticky元素的距离达到生效门槛，relative定位自动切换为fixed定位；等到父元素完全脱离视口时（即完全不可见），fixed定位自动切换回relative定位。

```css
.item {
    position: sticky;
    top: 0px;
    height: 200px;
}
```

#### inherit

继承父元素的定位属性值。

### 布局（display）

#### grid布局（网格布局）

学习教程：[CSS Grid 网格布局教程 - 阮一峰的网络日志 (ruanyifeng.com)](http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)

基础示例：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <title>JS Bin</title>
</head>
<style>
    #container {
        /* 指定布局方式 */
        display: grid;
        /* 指定有列的数量及每一列的列宽*/
        grid-template-columns: 100px 100px;
        /*  grid-template-columns: 50% 50%;  指定有列的数量及每一列的列宽
            grid-template-columns: repeat(3, 33.33%); repeat(重复的数量, 重复的值)
            grid-template-columns: repeat(auto-fill, 100px); 根据外部容易大小，自动填充列
            grid-template-columns: 1fr 2fr; 倍数
            ...
            grid-template-columns: [c1] 100px [c2] 100px [c3] 100px [c4]; 给网格线命名
          */
        /* 指定有行的数量及每一行的行高*/
        grid-template-rows: 100px 100px;
        /* 设置行/列间距  下面的设置也等于： 
         grid-gap: 20px 20px;
         */
        grid-row-gap: 20px;
        grid-column-gap: 20px;
        /* 定义子元素放置的顺序是row：“先行后列”还是column：“先列后行”，默认是row*/
        grid-auto-flow: column;  /*column dense; ：表示"先列后行"，并且尽可能紧密填满，尽量不出现空格*/
        /* 等 */
    }
    .item {
        font-size: 4em;
        text-align: center;
        border: 1px solid #e5e4e9;
    }
    .item-1 {
        background-color: #ef342a;
    }
    .item-2 {
        background-color: #f68f26;
    }
    .item-3 {
        background-color: #4ba946;
    }
    .item-4 {
        background-color: #0376c2;
    }
</style>
<body>
    <div id="container">
        <div class="item item-1">1</div>
        <div class="item item-2">2</div>
        <div class="item item-3">3</div>
        <div class="item item-4">4</div>
    </div>
</body>
</html>
```

#### flex布局（弹性布局）

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。

- 容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。（**其实就是水平方向和垂直方向**）

![img](http://cdn.trastor.cn/image-20210227170756255.png)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <title>JS Bin</title>
</head>
<style>
    /*容器属性*/
    #container {
        display: flex;
        /*flex-direction属性： 主轴的方向，也就是内部元素的排序方向  row（从左到右） | row-reverse （从由到左） | column（从上到下）  | column-reverse;（从下到上） */
        flex-direction: row;
        /*flex-wrap属性：如果一条轴线排不下，如何换行 nowrap（不换行） | wrap（第一行在上方） | wrap-reverse（第一行在下方） */
        flex-wrap: nowrap;
        /* justify-content属性：定义了项目在主轴上的对齐方式 flex-start（左对奇） | flex-end（右对齐）  | center（居中） | space-between（两端对齐） | space-aroun（每个项目两侧的间隔相等） */
        justify-content:  center;
        /* align-items属性定义项目在交叉轴上如何对齐：flex-start | flex-end | center | baseline（项目的第一行文字的基线对齐） | stretch （默认值，如果项目未设置高度或设为auto，将占满整个容器的高度）*/
        align-items:  center;
    }
    /*项目属性*/
    .item{
        order:0;/*规定项目的排序，数字越大越后*/
        flex-grow:0;/*放大比例,默认0，如果都是1则均分，如果是1和2，则分别是1/3、2/3*/
        flex-shrink:1;/*缩小比例，默认为1，如果都为1，则空间不足时，都缩小，为0的不缩小*/
        flex-basis:<length> | auto; /*属性定义了在分配多余空间之前，项目占据的主轴空间*/
        flex:none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ];/*该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)*/
        align-self:auto | flex-start | flex-end | center | baseline | stretch;/*属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性*/
    }
    .item {
        font-size: 4em;
        text-align: center;
        border: 1px solid #e5e4e9;
    }
    .item-1 {
        background-color: #ef342a;
    }
    .item-2 {
        background-color: #f68f26;
    }
    .item-3 {
        background-color: #4ba946;
    }
    .item-4 {
        background-color: #0376c2;
    }
</style>
<body>
    <div id="container">
        <div class="item item-1">1</div>
        <div class="item item-2">2</div>
        <div class="item item-3">3</div>
        <div class="item item-4">4</div>
    </div>
</body>

</html>
```

### 居中

[CSS实现水平垂直居中的10种方式 (yanhaijing.com)](https://yanhaijing.com/css/2018/01/17/horizontal-vertical-center/)

仅居中元素定宽高适用

- absolute + 负margin
- absolute + margin auto
- absolute + calc函数

居中元素不定宽高

- absolute + transform
- lineheight
- writing-mode
- table
- css-table
- flex
- grid

下面对比下各个方式的优缺，简单总结下

- PC端有兼容性要求，宽高固定，推荐absolute + 负margin
- PC端有兼容要求，宽高不固定，推荐css-table
- PC端无兼容性要求，推荐flex
- 移动端推荐使用flex

```js
<div class="wp">
    <div class="box size">123123</div>
</div>
.wp {
    border: 1px solid red;
    width: 300px;
    height: 300px;
}

.box {
    background: green;    
}

.box.size{
    width: 100px;
    height: 100px;
}

/* absolute + 负margin    缺点：需要知道子元素宽高*/
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
}
/* absolute + margin auto  缺点：需要知道子元素宽高
绝对定位元素可以在其包含块内上下左右移动，指定其距离包含块上下左右的距离都为零时，子元素将填充其包含块所有的可用空间，所以 margin 在水平和垂直方向上都有了可分配的空间，此时使用自动外边距就可使子元素居中 */
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
/* absolute + calc函数 缺点：需要知道子元素宽高 */
.wp {
    position: relative;
}
.box {
    position: absolute;;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}

/* absolute + transform  无需知道子元素宽高，但这种方法兼容性依赖translate2d的兼容性 */
.wp {
    position: relative;
}
.box {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

/* lineheight 把box设置为行内元素，通过text-align就可以做到水平居中， 通过vertical-align 设置在垂直方向居中 */
.wp {
    line-height: 300px;
    text-align: center;
    font-size: 0px;
}
.box {
    font-size: 16px;
    display: inline-block;
    vertical-align: middle;
    line-height: initial;
    text-align: left; /* 修正文字 */
}

/* flex 移动端已经完全可以使用flex了，PC端需要看自己业务的兼容性情况 */
.wp {
    display: flex;
    justify-content: center;
    align-items: center;
}

/* grid 兼容性不如flex，不推荐使用 */
.wp {
    display: grid;
}
.box {
    align-self: center;
    justify-self: center;
}

```



- div居中

  ```css
  /* 先为该容器设置一个明确宽度，然后将margin的水平值设为auto即可。*/
  width: 100px;
  margin: 0 auto;
  ```

- 文字的水平居中

  将一段文字置于容器的水平中点，只要设置text-align属性即可：

  ```css
  text-align:center;
  ```

- 文字的垂直居中

  单行文字的垂直居中，只要将行高与容器高设为相等即可。

  ```css
  height: 35px; 
  line-height: 35px;
  ```

- 通过flex布局居中

```html
<style>
    #container {
        height: 500px;
        display: flex;
        /* justify-content属性：定义了项目在主轴上的对齐方式 flex-start（左对奇） | flex-end（右对齐）  | center（居中） | space-between（两端对齐） | space-aroun（每个项目两侧的间隔相等） */
        justify-content:  center;
        /* align-items属性定义项目在交叉轴上如何对齐：flex-start | flex-end | center | baseline | stretch */
        align-items:  center;
    }
    .item {
        font-size: 4em;
        text-align: center;
        border: 1px solid #e5e4e9;
         background-color: #ef342a;
    }
</style>
<body>
    <div id="container">
        <div class="item">1</div>
    </div>
</body>
```



### 盒子模型

一个盒子中主要的属性就5个：width、height、padding、border、margin。如下：

- width和height：内容的宽度、高度（不是盒子的宽度、高度）。
- padding：内边距。
- border：边框。
- margin：外边距。

![image-20210317134155584](https://gitee.com/trastor/picture/raw/master/20210317134155.png)

**CSS盒模型和IE盒模型的区别**

- 在 标准盒子模型中，width 和 height 指的是内容区域的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。

- IE盒子模型中，width 和 height 指的是内容区域+border+padding的宽度和高度。

- 通过属性box-sizing来设置使用哪种

  ```
  box-sizing: content-box|border-box|inherit;  //标准盒模型、IE盒模型、继承父级
  ```

  

![image-20210317134728113](https://gitee.com/trastor/picture/raw/master/20210317134728.png)

### 动画 (animation)

```css
/*定义关键帧 */
@keyframes drive {
    0% {
        transform: translateX(0);
    }

    100% {
        transform: translateX(300px);
    }
}
  
.car {
    animation-duration: 3s;/*整个动画执行的时间*/
    animation-name: drive;/*使用的关键帧的名字*/
    animation-iteration-count:infinite;/* 动画执行的次数， 可以是1、2...*/
    animation-delay: 1s;/* 多久才开始动画*/
    animation-fill-mode: forwards;/*最终停留的位置*/
    animation-direction: alternate;/*设置关键帧执行的顺序*/
    animation-timing-function: ease-in-out	; /*设置动画的速度*/
    /* 简写模式：animation: name duration timing-function delay iteration-count direction fill-mode;*/
    /* animation: drive 3s ease-in-out 1s infinite alternate forwards;   */
} 

<img src="https://www.w3cplus.com/sites/default/files/blogs/2015/1506/car.png" class="car" width="220" />

```

### 浮动和清除浮动

**浮动定义**

设置浮动会让元素脱离标准流，后面的元素会顶上来，并且会被浮动元素遮挡住

**清除浮动**

问题的产生：如果父级元素高度是auto的，然后子级元素都是浮动的，那么就会导致父级元素丢失高度（高度成0）

解决方案

- 给父级元素添加属性“overflow:auto”或者“overflow:hidden”，浏览器会自动检查浮动区域的高度
- 在所有子元素的最后添加一个空div（也可以通过伪元素after生成一个隐藏的div），给其设置属性“clear:both”，clear的意思是他的左边(left)、右边(right)、左右(both)不能有浮动元素，清楚了浮动，那么父级元素就能自动获取到高度了

替代方案：不使用浮动，使用"display:inline-block"，既具有 block 元素可以设置宽高的特性，同时又具有 inline 元素默认不换行的特性，也可以设置 vertical-align（因为这个垂直对齐属性只对设置了**inline-block**的元素有效）

### 块级元素 行内元素

![image-20210320101925233](https://gitee.com/trastor/picture/raw/master/20210320101925.png)

**块级元素**：占据一行（宽度默认为本身父元素的100%，与内容无关），新的块级元素从新行开始，垂直排列。可以包含行内元素。

**行内元素**：根据实际内容宽度来决定本身宽度（无法手动设置width、height，可以设置line-height，margin上下无效，padding上下无效），后面新的行内元素会尾随其后，水平方向排列，

**两者元素互换**

- display:block;  设置成块级元素
- display:inline;  设置成行内元素
- display:inline-block;  既具有 block 元素可以设置宽高的特性，同时又具有 inline 元素默认不换行的特性，也可以设置 vertical-align（因为这个垂直对齐属性只对设置了**inline-block**的元素有效）

### 自适应网页设计

[自适应网页设计（Responsive Web Design） - 阮一峰的网络日志 (ruanyifeng.com)](http://www.ruanyifeng.com/blog/2012/05/responsive_web_design.html)

- 不使用绝对宽度，使用百分比宽度或者auto
- 相对大小的字体，使用em（1.5em，则是指大小是页面默认文字大小的1.5倍）
- 流动布局，各个位置都是浮动的，不是固定不变的了，可以使用float，这样如果宽度太小，浮动的元素会自动滚到前面元素的下方，不会在水平方向溢出
- 选择加载针对不同分辨率的css
  - 通过link的media属性配置，加载不同的css文件
  - 通过css内的@media规则使用不同的css片段
- 图片自适应，可以设置图片的"max-width:100%"等方法

### border 

border:border-width border-style border-color;  或者 inherit;

三个属性顺序随意，为了可读性使用上面顺序。

border-width:thin(细边框) medium(默认 中等边框) thick(粗边框) 10px(具体值);

border-style:dotted(点状) solid(实线) double(双线) dashed(虚线) inherit(继承父元素边框宽度)；

###  css选择器

```css
优先级：!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性
//基本选择器  优先级：id > class > 元素
span{color:red;font-size: 20px}//元素选择器
#div1{background-color: red}//id选择器
.style2{background-color: pink}//class选择器

//属性选择器	格式：基本选择器[属性 = '属性值']{ }
input[type = 'text'] {background-color: red}

//分组选择器   选择多个标签以设置相同样式，逗号隔开
p,a,li{color: blue;}

//后代（层级）选择器 用空格隔开
#d1 .dd2 span{color:red}

//兄弟选择器 同级的，加号分隔
p+a{color: green;}

//直接父级选择器
div>p {color: red;}

//伪类选择器 
静态伪类：（只能用于a标签）
a:link {color: red;}/*让超链接点击之前是红色*/
div:visited {color: orange;}/*让超链接点击之后是橙色*/
动态伪类：（所有标签都适用）
div:hover {color: green;}/*鼠标悬停，放到标签上的时候是绿色*/
div:active {color: black;}/*鼠标点击链接，但是不松手的时候*/
div:focus {color: black;}/*某个标签获得焦点时的样式*/
```

### css伪元素选择器

| 选择器         | 例子            | 例子描述                      |
| :------------- | :-------------- | :---------------------------- |
| ::after        | p::after        | 在每个 <p> 元素之后插入内容。 |
| ::before       | p::before       | 在每个 <p> 元素之前插入内容。 |
| ::first-letter | p::first-letter | 选择每个 <p> 元素的首字母。   |
| ::first-line   | p::first-line   | 选择每个 <p> 元素的首行。     |
| ::selection    | p::selection    | 选择用户选择的元素部分。      |

### css伪类选择器

| 选择器                 | 例子                  | 例子描述                                                     |
| :--------------------- | :-------------------- | :----------------------------------------------------------- |
| **:active**            | a:active              | 选择活动的链接。                                             |
| :checked               | input:checked         | 选择每个被选中的 <input> 元素。                              |
| :disabled              | input:disabled        | 选择每个被禁用的 <input> 元素。                              |
| :empty                 | p:empty               | 选择没有子元素的每个 <p> 元素。                              |
| :enabled               | input:enabled         | 选择每个已启用的 <input> 元素。                              |
| :first-child           | p:first-child         | 选择作为其父的首个子元素的每个 <p> 元素。                    |
| :first-of-type         | p:first-of-type       | 选择作为其父的首个 <p> 元素的每个 <p> 元素。                 |
| **:focus**             | input:focus           | 选择获得焦点的 <input> 元素。                                |
| :**hover**             | a:hover               | 选择鼠标悬停其上的链接。                                     |
| :in-range              | input:in-range        | 选择具有指定范围内的值的 <input> 元素。                      |
| :invalid               | input:invalid         | 选择所有具有无效值的 <input> 元素。                          |
| :lang(*language*)      | p:lang(it)            | 选择每个 lang 属性值以 "it" 开头的 <p> 元素。                |
| :last-child            | p:last-child          | 选择作为其父的最后一个子元素的每个 <p> 元素。                |
| :last-of-type          | p:last-of-type        | 选择作为其父的最后一个 <p> 元素的每个 <p> 元素。             |
| :**link**              | a:link                | 选择所有未被访问的链接。                                     |
| :not(*selector*)       | :not(p)               | 选择每个非 <p> 元素的元素。                                  |
| :nth-child(*n*)        | p:nth-child(2)        | 选择作为其父的第二个子元素的每个 <p> 元素。                  |
| :nth-last-child(*n*)   | p:nth-last-child(2)   | 选择作为父的第二个子元素的每个<p>元素，从最后一个子元素计数。 |
| :nth-last-of-type(*n*) | p:nth-last-of-type(2) | 选择作为父的第二个<p>元素的每个<p>元素，从最后一个子元素计数 |
| :nth-of-type(*n*)      | p:nth-of-type(2)      | 选择作为其父的第二个 <p> 元素的每个 <p> 元素。               |
| :only-of-type          | p:only-of-type        | 选择作为其父的唯一 <p> 元素的每个 <p> 元素。                 |
| :only-child            | p:only-child          | 选择作为其父的唯一子元素的 <p> 元素。                        |
| :optional              | input:optional        | 选择不带 "required" 属性的 <input> 元素。                    |
| :out-of-range          | input:out-of-range    | 选择值在指定范围之外的 <input> 元素。                        |
| :read-only             | input:read-only       | 选择指定了 "readonly" 属性的 <input> 元素。                  |
| :read-write            | input:read-write      | 选择不带 "readonly" 属性的 <input> 元素。                    |
| :required              | input:required        | 选择指定了 "required" 属性的 <input> 元素。                  |
| :root                  | root                  | 选择元素的根元素。                                           |
| :target                | #news:target          | 选择当前活动的 #news 元素（单击包含该锚名称的 URL）。        |
| :valid                 | input:valid           | 选择所有具有有效值的 <input> 元素。                          |
| :**visited**           | a:visited             | 选择所有已访问的链接。                                       |

### 三栏布局

```html
方法一：左右两块设置宽高度，分别左浮动、右浮动，中间设置高度，以及左右margin值为左右块的宽度。
<div>
    <div class="left"></div>
    <div class="right"></div>
    <div class="main"></div>
</div>

<style>
    .left {
        float: left;
        height: 100px;
        width: 100px;
        background-color: brown;
    }

    .right {
        float: right;
        height: 100px;
        width: 100px;
        background-color: yellow;
    }

    .main {
        height: 200px;
        margin: 0 100px 0 100px;
        background-color: green;
    }
</style>

方法二：利用-margin，三块都设置浮动，中间块100%宽度，左右margin是左右块的宽度，left的margin则设置为-100%，让他浮动到最左边
<div class="content">
    <div class="main"></div>
</div>
<div class="left"></div>
<div class="right"></div>

<style>
    .content {
        width: 100%;
        float: left;
    }

    .main {
        height: 200px;
        margin: 0 100px;
        background-color: green;
    }

    .left {
        float: left;
        height: 200px;
        width: 100px;
        margin-left: -100%;
        background-color: red;
    }

    .right {
        float: right;
        height: 200px;
        width: 100px;
        margin-left: -100px;
        background-color: blue;
    }
</style>

方法三：利用flex布局，重点是flex-grow，如果存在剩余空间，设置为1则填充。

<style>
    .container {
        display: flex;
    }
    .main {
        flex-grow: 1;
        height: 300px;
        background-color: red;
    }
    .left {
        order: -1;
        flex: 0 1 200px;
        margin-right: 20px;
        height: 300px;
        background-color: blue;
    }
    .right {
        flex: 0 1 100px;
        margin-left: 20px;
        height: 300px;
        background-color: green;
    }
</style>

<div class="container">
    <div class="main"></div>
    <div class="left"></div>
    <div class="right"></div>
</div>

方法四：利用tabel，外面div宽度100%，里面左右两块固定宽度，则自动将剩余宽度分配给中间块
<style>
    .container {
        display: table;
        width: 100%;
    }
    .left,
    .main,
    .right {
        display: table-cell;
    }
    .left {
        width: 200px;
        height: 300px;
        background-color: red;
    }
    .main {
        background-color: blue;
    }
    .right {
        width: 100px;
        height: 300px;
        background-color: green;
    }
</style>
<div class="container">
    <div class="left"></div>
    <div class="main"></div>
    <div class="right"></div>
</div>
```

### 画线

```css
直接划线（通过border划线，只显示一边，可以做到垂直的线或者水平的）
height: 1px;
border-top: solid #C0CBE9 1px;

画0.5px的线（在上面的基础上，加个缩放）
transform: scaleY(0.5);

文字下划线
text-decoration: underline;
或者border-bottom: 1px solid black;//如果需要边距可以加上pandding-bottom

文字下方三条线（边框双实线+文字下划线）
border-bottom: 4px double;
text-decoration: underline;

画三角形
1、通过边框的特性，div块宽高设置为0
width: 0;
height: 0;
border-color: orange transparent transparent transparent;  一边有颜色，其他透明，就是一个三角形了
border: 100px solid;

2、伪元素+border
.demo:after{
    content: '';
    display: inline-block;
    width: 0;
    height: 0;
    border: 8px solid transparent;
    border-left: 8px solid #AFABAB;
    position: relative;
    top: 2px;
    left: 10px;
}

空心三角形（两个三角形叠加，通过border和伪元素分别画两个三角形，伪元素的三角形颜色设置成白色，大小比border的小一些，覆盖在上面，就可以实现了）
.border {
    width: 0;
    height: 0;
    border-style:solid;
    border-width: 0 50px 50px;
    border-color: transparent transparent #d9534f;
    position: relative;
}
.border:after {
    content: '';
    border-style: solid;
    border-width: 0 40px 40px;
    border-color: transparent transparent #96ceb4;
    position: absolute;
    top: 6px;
    left: -40px;
}

一个箭头（在上面空心三角形的基础上，让两个三角形一样大，然后第二个三角形往下移点就好了）


```

### transform

属性允许你旋转，缩放，倾斜或平移给定元素。这是通过修改CSS视觉格式化模型的坐标空间来实现的。

```
transform: matrix(1, 2, 3, 4, 5, 6);//定义 2D 转换，使用六个值的矩阵
transform: translate(120px, 50%);//位置偏移
transform: scale(2, 0.5);//缩放，x,y
transform: rotate(0.5turn);//旋转，示例是旋转50%
transform: skew(30deg, 20deg);//x,y倾斜
transform: scale(0.5) translate(-100%, -100%);//偏移的同时也缩放
```

### BFC

Formatting context(格式化上下文) 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。它属于普通流。具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

触发 BFC，只要元素满足下面任一条件即可触发 BFC 特性：

- html根元素
- 浮动元素：float 除 none 以外的值
- 绝对定位元素：position (absolute、fixed)
- display 为 inline-block、table-cells、flex
- overflow  visible 以外的值 (hidden、auto、scroll)

BFC 特性及应用

- 同一个 BFC 下外边距会发生折叠。如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中
- BFC 可以包含浮动的元素（清除浮动）
- BFC 可以阻止元素被浮动元素覆盖

面试题目

