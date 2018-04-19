# 动态REM解决手机端适配

###### REM：相对单位，始终是基于根元素HTML的，代表倍数。

---

注：更多px、rm、rem的区别[请看我的另一篇文章！](https://www.jianshu.com/p/1f0550c66c2a)


你要知道

1、页面上所以的字体大小是16px

2、Chome默认最小字体大小是12px

注意：
```
//html
<p>你好啊</p>

p{
  font-size:2rem;
}

//相当于
p{
  font-size:32px;
}
```

```
//html
<p>你好啊</p>

body{
  font-size: 20px
}
p{
  font-size:2rem;
}

//p的值还是. 因为rem是基于html的font-size
p{
  font-size: 32px;
}
```

------

#### 设计稿

![大概的设计稿是这样的](https://upload-images.jianshu.io/upload_images/5691870-7761f11390605397.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)

##### 手机端方案特点
- 1rem == html font-size的宽度 ==   viewport width （视窗的宽度）

- 所以的手机显示界面都是一样的比例。

##### 用js生成style标签

```
<script>
  document.write('<style>html{color:red}</style>')
</script>

------------


// 注意要加meta
 <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">

<script>
  var pageWidth = window.innerWidth //浏览器视口
  document.write('<style>html{font-size:' + pageWidth + 'px}</style>')
</script>

```

#### 实现

- html
```
  <div class="box clearfix">
    <div class="content">动态REM</div>
    <div class="content">动态REM</div>
    <div class="content">动态REM</div>
    <div class="content">动态REM</div>
  </div>

```
- css
```
* {
  margin: 0;
  padding: 0;
}
body {
  font-size: 16px
}
.content {
  background: #ddd;
  margin: 10px 0;
}
.content {
  float: left;
  width: 0.4rem;            /* 128(div的宽度) / 320(设计稿屏幕的宽度) = 0.4rem */
  height: 0.2rem;           /* 0.4rem的一半 = 0.2rem  =  设计稿的 64px */
  margin: 0.05rem 0.05rem;  /* 0.2rem 的一半为 0.1rem => 32px */
                            /*因为设计稿为16px，所以再 0.1rem的一半 0.05rem*/
}
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}
```
- js
```
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">

<script>
  var pageWidth = window.innerWidth
  document.write('<style>html{font-size:' + pageWidth + 'px}</style>')
</script>
```

- 效果



![效果图](https://upload-images.jianshu.io/upload_images/5691870-4ce067113b720bd1.gif?imageMogr2/auto-orient/strip)


####  px变成rem
因为每个长度都要去计算，如果css多的话那么工作量就会很大，所以就由以下的方法来解决。

##### 首先安装scss
```
// 使用淘宝镜像
$ npm config set registry https://registry.npm.taobao.org/   

$ touch ~/.bashrc

$ echo 'export SASS_BINARY_SITE="https://npm.taobao.org/mirrors/node-sass"' >> ~/.bashrc

$ source ~/.bashrc

$ npm i -g node-sass
```
Mac用户完成以上5部即可安装成功

##### 使用[PX2REM](https://github.com/imochen/luna/blob/master/root/src/px2rem.scss)
```
//在当前文件新建scss和css文件夹
$ mkdir scss css  

//新建style.scss 文件
$ touch scss/style.scss 

//重点
$ node-sass -wr scss -o css

//在style.scss写入 

//重点
@function px($px) {
  @return $px / $designWidth * 10 + rem;
  //注意这里的10是为了省小数点，对应script
}

$designWidth: 320;   //设计师给你设计稿的宽度

* {
  margin: 0;
  padding: 0;
}

body {
  font-size: 16px
}
.content {
  background: #ddd;
  margin: 10px 0;
}
.content {
  float: left;
  width: px(128);  // 128 / 320 * 10 = 4rem
  height: px(64);
  margin: px(16);
}
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}
```
js
```
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">

<script>
  var pageWidth = window.innerWidth
  document.write('<style>html{font-size:' + pageWidth/10 + 'px}</style>')
</script>
```





![效果](http://oy5vxcq55.bkt.clouddn.com/px2rem.gif)











