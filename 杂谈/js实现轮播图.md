### 用js实现轮播图

思路

- 最外面一层设置个固定宽高的div1，作为视窗。 设置定位为相对定位relative（为了让div2的绝对定位基于div1），超出内容隐藏
- 里面设置同高，宽度很宽（根据里面的元素决定）的div 2。设置定位为绝对定位absolute（相对于非static的父级元素）
- div2里放多个img，每个img的宽高和div1的宽高相同
- 通过控制.list的left的偏移量即可实现图片的切换（如下，可以设置每次加或者减600px即可）
- 如果要实现自动切换，只需要加计时器自动加偏移量600px即可

```html
<style>
    .box{
        height: 300px;
        width: 600px;
        position: relative;
        overflow: hidden;
    }
    .list{
        height: 300px;
        width: 3000px;
        position: absolute;
    }
    img{
        height: 300px;
        width: 600px;
        float: left;
    }
</style>

  <div class="box">
        <div class="list">
            <img src="../1617687139.png" alt="1">
            <img src="../1617687147.jpg" alt="2">
            <img src="../1617687173.jpg" alt="3">
            <img src="../5085c47b95.jpg" alt="4">
            <img src="../111.jpg" alt="5">
        </div>
    </div>
```

