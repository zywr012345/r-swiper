---
theme: channing-cyan
---
## r-swiper vue3版本 含有禁止滑动块、快速连播模式，完美解决移动/PC端轮播需求

[![NPM version](https://img.shields.io/npm/v/r-swiper.svg?style=flat)](https://npmjs.org/package/r-swiper)
[![NPM Downloads](https://img.shields.io/npm/dm/r-swiper.svg?style=flat)](https://npmjs.org/package/r-swiper)
![npm peer dependency version](https://img.shields.io/npm/dependency-version/r-swiper/peer/vue)

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a08f12822ad43438c3601792932bfbb~tplv-k3u1fbpfcp-watermark.image?)
#### 这是一个vue3版本的轮播插件,移动端和pc端都已完美适配.
[vue2版本链接](https://www.npmjs.com/package/r-swiper2)

可能有小伙伴会问了，都2023年了，市面上的轮播组件数不胜数，谁还会傻到造轮播的轮子啊？

啊这。。。

不是说存在即合理吗

原因吗，主要有两点

 1、如果引入官方swiper包会不会太大了，有必要吗?

 2、有其他体积小的vue3开源组件，大部分场景都能适配，也多次遇到过特殊需求，无奈只能多次更改源码

But Please: Don't Repeat Yourself!

既然如此，那我就来封装一个功能强大，动效精美的轮播组件，可以完美适配绝大部分正常的业务需求（如果这个组件还满足不了你的需求，本着忍一时越想越气,退一步越想越亏的原则，我劝你即刻去找产品对线。。。啊不，即刻联系我，我看看能不能再优化一下）。

此插件含有 禁止滑动块模式(noMove属性) 可以在swiper轮播内部实现阻拦滑动效果
丝滑模式(fast属性), 连续切换时, 没有强制限制动画时间, 切换快慢只取决于你的手速, 德芙~纵享丝滑
可嵌套使用, 目前遇到的所有bug都已更改

第一次写轮播还是17年,用的jQuery, 代码已经找不到了, 这次浏览了多个swiper开源代码，综合整理，做了部分优化，如果感觉不流畅或者对此插件有修改建议请及时联系我(联系方式在最底部), 最后希望大家能够喜欢！

### 安装依赖
1. 打开项目根目录，执行命令
    ```js
    // 如果使用的是npm
    npm i r-swiper

    // 如果使用的是yarn
    yarn add r-swiper
    ```
### 引入依赖
安装依赖后根据自己的需求引入
- 全局引入
    ```js
    // 10.14k 目前体积还可以接受
    import Swiper from 'r-swiper'

    // ... 创建 app ...

    app.use(Swiper)
    ```
- 局部引入

    ```vue
    <scriipt setup>
      // Composition 组合式API引入
      import {rSwiper, rSlide} from 'r-swiper'
    </script>

    // Options 选项式API引入
    <script>
    import {rSwiper, rSlide} from 'r-swiper'

    export default {
      components: {
        'rSwiper': rSwiper,
        'rSlide': rSlide
      }
    }
    </script>
    ```
#### 组件讲解

名称|介绍
-|-
rSwiper|外部组件，各种事件动效都在此内完成
rSlide|内部组件，包裹各个轮播页面内容

#### props参数 (一律使用小驼峰命名)

  名称 | 默认值| 类型 |介绍
  -|-|-|-
  fast | false | Boolean | 是否为快速滑动，纵享丝滑模式
  autoPlay | false | Boolean | 是否自动轮播播放
  playTime | 5000 | Number,Sting | 自动播放切换时间
  speed | 500 | Number, String | 切换页面后的动画过渡时间
  therehold | 100 | Number | 滑动多少距离后触发切页函数
  slide | 0 | Number | 初始默认下标
  indicator | true | Boolean | 指示器显示? 底部居中闪烁点/条状
  indicatorFlash | true | Boolean | 指示器闪烁动效
  noMove | cs | String| 非滑动模式 className(一会重点讲)
  mobile | false | Boolean | 是否为移动端(移动端会隐藏 左右切页按钮)
  vLock | false | Boolean | 垂直方向是否可滚动


#### methos事件
类型|名称|介绍
-|-|-
ref事件|refDom.prev()|切换到上一页
/|refDom.next()|切换到下一页
/|refDom.slideTo(i)|切换到指定下标(i)页
emit事件|@loadEnd|初次渲染完成后反馈函数
/|@transitionend(i)|切换页面后反馈函数, i 为当前下标


### 代码案例
```vue
<template>
<div class="home-style">
  <rSwiper ref="swiperDOM" class="swiper" @loadEnd="funLoadEnd" @transitionend="funTransitionend">
    <rSlide>
      <div class="box"></div>
    </rSlide>
    <rSlide>
      <div class="box box2"></div>
    </rSlide>
    <rSlide>
      <div class="box box3"></div>
    </rSlide>
  </rSwiper>
</div>
</template>

<script setup>
  import { rSlide, rSwiper } from 'r-swiper'

  const nowIndex = ref(0) // 当前下标

  const swiperDOM = ref(null) // Dom元素


  /**
  * 初始化加载完成反馈函数 2023-08-12 19:23:06 Ywr
  */
  const funLoadEnd = () => {
    console.log('loading - success');
    // changePage(1, true)
  }

  /**
  * 切换页面完成反馈函数 2023-08-12 19:23:06 Ywr
  * @param {Number} i 当前下标
  */
  const funTransitionend = (i) => {
    nowIndex.value = i
    console.log('当前下标', nowIndex.value)
  }

  /**
  * 切换页函数 2023-08-12 19:23:06 Ywr
  * @param {Number} i 下标
  * @param {Boolean} st 当前下标
  */
  const changePage = (i, st = false) => {
    // 如果是跳转到某页
    if (st) {
      swiperDOM.value.slideTo(i)
    }
    else {
      i < 0 ? (swiperDOM.value.prev()) : (swiperDOM.value.next())
    }
  }

</script>

<style scope lang="scss">
.home-style {
  width: 100vw;
  height: 100vh;

  .box {
    width: 100vw;
    height: 100vh;
    background: url('../1.png') no-repeat center center;
    background-size: cover;
  }

  .box2 {
    background-image: url('../2.png')
  }

  .box3 {
    background-image: url('../5.png')
  }
}
</style>
```

### 效果图

pc端

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cecc8a51eb374654a5e5deed614b303e~tplv-k3u1fbpfcp-watermark.image?)


移动端
![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f88a55d4261e49e18bc46ca425bb6689~tplv-k3u1fbpfcp-watermark.image?)

### 温馨提示
默认会显示底部指示器、 [pc端：左右两个切换按钮], 指示器和按钮都有点击切页效果

可根据props传值隐藏


具名插槽：
  如果感觉 左右按钮、指示器样式不美观 或者想写入新内容 可使用以下插槽

```html
  <!-- 左右按钮 切换上下页 -->
  <template #leftBtn>
      ...
  </template>
  <template #rightBtn>
      ...
  </template>

  <!-- 指示器 -->
  <template #indicator>
      ...
  </template>

  <!-- 其他插槽 -->
  <template #all>
      ...
  </template>
```

### noMove讲解
为什么会有noMove属性 [默认为：cs] 呢?

##### 如果我想两个轮播嵌套，子轮播切换时不影响父轮播，此时，就要求子轮播在滑动时不得触发父轮播滑动
pc端：

1、滑动顶部蓝色hello world！区域，会切换整体父swiper页面,如下效果

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f528bff8d1244e488339e314e0e3855b~tplv-k3u1fbpfcp-watermark.image?)

2、滑动地球图片，只切换内部的子swiper

![微信图片_20230731114908.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e7176f886d74ebeb7f5ac470f2ec775~tplv-k3u1fbpfcp-watermark.image?)

我们只需要给 内部的元素添加className属性 "cs" 即可
```vue
 <img class="cs" src="../2.png" alt="">
```

 注意如果觉得 cs不符合要求，可以选择更改props属性
```vue
<rSwiper :noMove="yourClassName"></rSwiper>

<!-- 此处与noMove传参保持一致 -->
<img class="yourClassName" src="../2.png" alt="">
```

完整代码案例
```vue
<template>
<div class="home-style">
  <rSwiper class="swiper" :autoPlay="false" :indicator="false" ref="swiper" @loadEnd="funLoadEnd" @transitionend=funTransitionend :mobile="true">
    <rSlide>
      <div class="first-cont">
        <div class="can"></div>
        <!-- 注意此处 子swiper 的 noMove 属性一定不能是 cs [父swiper中的 noMove属性，否则自己的滑动事件也会被阻止] -->
        <rSwiper class="child" noMove="nononononono" playTime="2000" :mobile="true" :autoplay="false">
          <rSlide>
            <img class="cs" src="../5.png" alt="">
          </rSlide>
          <rSlide>
            <img class="cs" src="../7.png" alt="">
          </rSlide>
          <rSlide>
            <img class="cs" src="../6.png" alt="">
          </rSlide>
        </rSwiper>
      </div>
      </rSlide>
      <rSlide>
        <img src="../1.png" alt="">
      </rSlide>
      <rSlide>
      <img src="../1.png" alt="">
      </rSlide>
    </rSwiper>
</div>
</template>

<script setup>
  import { rSlide, rSwiper } from 'r-swiper'

  const nowIndex = ref(0) // 当前下标

  const swiperDOM = ref(null) // Dom元素


  /**
  * 初始化加载完成反馈函数 2023-08-12 19:23:06 Ywr
  */
  const funLoadEnd = () => {
    console.log('loading - success');
    // changePage(1, true)
  }

  /**
  * 切换页面完成反馈函数 2023-08-12 19:23:06 Ywr
  * @param {Number} i 当前下标
  */
  const funTransitionend = (i) => {
    nowIndex.value = i
    console.log('当前下标', nowIndex.value)
  }

  /**
  * 切换页函数 2023-08-12 19:23:06 Ywr
  * @param {Number} i 下标
  * @param {Boolean} st 当前下标
  */
  const changePage = (i, st = false) => {
    // 如果是跳转到某页
    if (st) {
      swiperDOM.value.slideTo(i)
    }
    else {
      i < 0 ? (swiperDOM.value.prev()) : (swiperDOM.value.next())
    }
  }

</script>

<style scope lang="scss">

.home-style {
  width: 100vw;
  height: 100vh;

  .first-cont {
    display: flex;
    flex-direction: column;
    overflow: hidden;
    width: 100%;
    height: 100vh;
    .can {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 100%;
      height: 10vh;
      background: skyblue;
    }
    .child {
      flex: 1;
    }
  }
  img {
    width: 100vw;
    height: 100vh;
  }
}

</style>
```

移动端：
京东/淘宝/拼多多/得物等多个平台都有这种需求

滑动左侧图红框部分，整体swiper会切换

滑动右侧图红框部分，整体swiper不会切换，内部的swiper-banner会切换

![1690784740829.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/31474ec1b4774488bf0345c5df7cfb45~tplv-k3u1fbpfcp-watermark.image?)

亦或者，内部某些区域(如下方蓝色快列表)需要完成自己的滚动效果

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/86f818a0e6bd4e588efb49c9e791539d~tplv-k3u1fbpfcp-watermark.image?)

完整代码如下
```vue
<template>
  <div class="home-style">
    <rSwiper class="swiper" :autoPlay="false" :identifier="false" ref="swiper" @loadEnd="funLoadEnd" @transitionend="funTransitionend " :mobile="true">
      <rSlide>
        <div class="first-cont">
          <img src="../1.png" alt="">
          <!-- 此处添加 noMove属性 cs -->
          <div class="box cs">
             <div class="can cs">
               <div class="cs" v-for="i in 100" :key="i">{{i}}</div>
             </div>
          </div>
        </div>
      </rSlide>
      <rSlide>
        <img src="../1.png" alt="">
      </rSlide>
      <rSlide>
        <img src="../1.png" alt="">
      </rSlide>
    </rSwiper>
  </div>
</template>

<script setup>
  import { rSlide, rSwiper } from 'r-swiper'

  const nowIndex = ref(0) // 当前下标

  const swiperDOM = ref(null) // Dom元素


  /**
  * 初始化加载完成反馈函数 2023-08-12 19:23:06 Ywr
  */
  const funLoadEnd = () => {
    console.log('loading - success');
    // changePage(1, true)
  }

  /**
  * 切换页面完成反馈函数 2023-08-12 19:23:06 Ywr
  * @param {Number} i 当前下标
  */
  const funTransitionend = (i) => {
    nowIndex.value = i
    console.log('当前下标', nowIndex.value)
  }

  /**
  * 切换页函数 2023-08-12 19:23:06 Ywr
  * @param {Number} i 下标
  * @param {Boolean} st 当前下标
  */
  const changePage = (i, st = false) => {
    // 如果是跳转到某页
    if (st) {
      swiperDOM.value.slideTo(i)
    }
    else {
      i < 0 ? (swiperDOM.value.prev()) : (swiperDOM.value.next())
    }
  }

</script>

<style scope lang="scss">
.home-style {
  width: 100vw;
  height: 100vh;

 .first-cont {
    display: flex;
    flex-direction: column;
    overflow: hidden;
    width: 100%;
    height: 100vh;

    .box {
      width: 100%;
      height: 20vh;
      overflow-x: scroll;
      .can {
        display: flex;
        justify-content: flex-start;
        align-items: center;
        height: 20vh;

        div {
          background: skyblue;
          border: 1px solid #fff;
          width: 100px;
          padding: 80px;
          margin: 10px;
        }
      }
    }

    img {
      height: 80vh;
    }

    .child {
      flex: 1;
    }
  }
  img {
    width: 100vw;
    height: 100vh;
  }
}

</style>
```

上面这几种情况都可以使用当前swiper解决。


#### fast 丝滑模式
大家可以试一下swiper官网 以及京东、淘宝，得物的轮播页面，快速滑动时，会有不连贯的感觉
因为在轮播组件内部，会有一个时间段的保护机制，在此时间段内需要等待轮播事件完成的动态效果，此时如果强制滑动会有异常
而本文插件的 fast模式，只需要设置swiper的 fast即刻开启丝滑模式

```vue
<rSwiper fast></swiper>
```

此时就可以实现快速滑动, 切换的时间只取决于你的手速, 纵享丝滑

#### 这就是本期的全部内容了，谢谢大家的阅读。

#### 感谢
感谢各swiper开源开发者
特别感谢linfeng大佬[https://github.com/helicopters?tab=repositories]


#### 联系作者

Email: [zywr012345@gmail.com](mailto:zywr012345@gmail.com)

WeChat: ywr_98