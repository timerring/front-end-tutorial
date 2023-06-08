# 小程序模板与配置

## WXML 模板语法

### 数据绑定

在 data 中定义数据，在 WXML 中使用数据。使用Mustache`{{}}`语法：

- 绑定内容
- 绑定属性
- 运算（三元运算、算术运算等）

```javascript
data: {
    info: 'hello world',
    imgSrc: 'http://www.xxxx.com/images/logo.png',
    randomNum1: Math.random() * 10,
    randomNum2: Math.random().toFixed(2),
  },
```

```html
<view>{{info}}</view>
<image src="{{imgSrc}}" mode="widthFix"></image>
<view>{{randomNum1 >=5 ? '数字大于或等于5' : '数字小于5'}}</view>
<view>{{randomNum2 * 100}}</view>
```

### 事件绑定

什么是事件：事件是渲染层到逻辑层的通讯方式。通过事件可以将用户在渲染层产生的行为，反馈到逻辑层进行业务的处理。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601214342241.png)

#### 小程序中常用的事件

| **类型** | **绑定方式**              | **事件描述**                                    |
| -------- | ------------------------- | ----------------------------------------------- |
| tap      | bindtap 或 bind:tap       | 手指触摸后马上离开，类似于 HTML 中的 click 事件 |
| input    | bindinput 或 bind:input   | 文本框的输入事件                                |
| change   | bindchange 或 bind:change | 状态改变时触发                                  |

#### 事件对象的属性列表

当事件回调触发的时候，会收到一个事件对象 event，它的详细属性如下表所示：

| **属性**       | **类型** | **说明**                                     |
| -------------- | -------- | -------------------------------------------- |
| type           | String   | 事件类型                                     |
| timeStamp      | Integer  | 页面打开到触发事件所经过的毫秒数             |
| **target**     | Object   | 触发事件的组件的一些属性值集合               |
| currentTarget  | Object   | 当前组件的一些属性值集合                     |
| **detail**     | Object   | 额外的信息                                   |
| touches        | Array    | 触摸事件，当前停留在屏幕中的触摸点信息的数组 |
| changedTouches | Array    | 触摸事件，当前变化的触摸点信息的数组         |

#### target 和 currentTarget 的区别

**target 是触发该事件的源头组件，而 currentTarget 则是当前事件所绑定的组件。**举例如下：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601214454332.png)

点击内部的按钮时，点击事件以冒泡的方式向外扩散，也会触发外层 view 的 tap 事件处理函数。

此时，对于外层的 view 来说：

e.target 指向的是触发事件的源头组件，因此，e.target 是内部的按钮组件。

e.currentTarget 指向的是当前正在触发事件的那个组件，因此，e.currentTarget 是当前的 view 组件。

#### bindtap 的语法格式

在小程序中，不存在 HTML 中的 onclick 鼠标点击事件，而是通过 tap 事件来响应用户的触摸行为。通过 bindtap，可以为组件绑定 tap 触摸事件，语法如下：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601215138932.png)

在页面的 .js 文件中定义对应的事件处理函数，事件参数通过形参 event（一般简写成 e） 来接收：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601215152379.png)

在事件处理函数中为 data 中的数据赋值

通过调用 this.setData(dataObject) 方法，可以给页面 data 中的数据重新赋值，示例如下：

```javascript
setCount(){
    this.setData({
      count:this.data.count+1
    })
  }
```

#### 事件传参

小程序中的事件传参比较特殊，不能在绑定事件的同时为事件处理函数传递参数。例如，下面的代码将不能正常工作：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601215334330.png)

因为小程序会把 bindtap 的属性值，统一当作事件名称来处理，相当于要调用一个名称为 btnHandler(123) 的事件处理函数。

可以为组件提供 `data-*` 自定义属性传参，其中 * 代表的是参数的名字：

```javascript
<button bindtap="btnHandler" data-info="{{2}}">事件传参</button>
```

最终`info`会被解析成参数的名字，数值`2`会被解析为参数的值，`{{2}}`被解析为数字2，如果直接是2，则会被解析为文本2.

在事件处理函数中，通过 `event.target.dataset.参数名` 即可获取到具体参数的值，具体查找的方法可以先`console.log(e)`，然后从打印的信息中查找。

#### bindinput 的语法格式

在小程序中，通过 input 事件来响应文本框的输入事件，语法格式如下：

通过 bindinput，可以为文本框绑定输入事件：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601215746161.png)

在页面的 .js 文件中定义事件处理函数：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601215754863.png)

实现文本框和 data 之间的数据同步

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601220052871.png)

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601220056744.png)

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601220100560.png)

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601220104904.png)

### 条件渲染

#### wx:if

在小程序中，使用 wx:if="{{condition}}" 来判断是否需要渲染该代码块，也可以用 wx:elif 和 wx:else 来添加 else 判断：

```html
<view wx:if="{itype === 1}}">男</view>
<view wx:elif="{{type === 2}}">女</view>
<view wx:else>保密</view>
```

如果要一次性控制多个组件的展示与隐藏，可以使用一个 `<block></block>` 标签将多个组件包装起来，并在`<block>` 标签上使用 wx:if 控制属性。注意：`<block>` 并不是一个组件，**它只是一个包裹性质的容器，不会在页面中做任何渲染**。

```html
<block wx:if="{{false}}">
	<view>view1</view>
	<view>view2</view>
</block>
```

#### hidden

在小程序中，直接使用 `hidden="{{ condition }}"` 也能控制元素的显示与隐藏。

wx:if 与 hidden 的对比：

运行方式不同

+ wx:if 以动态创建和移除元素的方式，控制元素的展示与隐藏。频繁切换时，建议使用 hidden。
+ hidden 以切换样式的方式（display: none/block;），控制元素的显示与隐藏。控制条件复杂时，建议使用 wx:if 搭配 wx:elif、wx:else 进行展示与隐藏的切换。

```html
<view hidden="{{!flag}}">条件为 true 的时候隐藏元素，否则显示</view>
<view wx:if="{{flag}}">这是使用 wx:if 控制的元素</view>
```

### 列表渲染

#### wx:for

通过 wx:for 可以根据指定的数组，循环渲染重复的组件结构，默认情况下，当前循环项的索引用 index 表示；当前循环项用 item 表示。

```html
<view wx:for="{{arr1}}" wx:key="index">
	索引是：{{index}}，item 项是：{{item}}
</view>
```

```javascript
    arr1: ['苹果', '华为', '小米'],
```



> 手动指定索引和当前项的变量名*
>
> + 使用 wx:for-index 可以指定当前循环项的索引的变量名
> + 使用 wx:for-item 可以指定当前项的变量名
>
> ```html
> <view wx:for="{{arr1}}" wx:for-index="idx" wx:for-item="itemName" wx:key="idx">
> 	索引是：{{idx}}，item 项是：{{itemName}}
> </view>
> ```

#### wx:key

类似于 Vue 列表渲染中的 :key，小程序在实现列表渲染时，也建议为渲染出来的列表项指定唯一的 key 值，从而提高渲染的效率。

```html
<view wx:for="{{userList}}" wx:key="id" class="username">{{item.name}}</view>
```

```javascript
    userList: [
      { id: 1, name: '小红' },
      { id: 2, name: '小黄' },
      { id: 3, name: '小白' }
    ]
```

## WXSS 模板样式

WXSS (WeiXin Style Sheets)是一套样式语言，用于美化 WXML 的组件样式，WXSS 具有 CSS 大部分特性，同时，WXSS 还对 CSS 进行了扩充以及修改，以适应微信小程序的开发。与 CSS 相比，WXSS 扩展的特性有：

+ rpx 尺寸单位
+ @import 样式导入

### rpx

rpx（responsive pixel）是微信小程序独有的，用来解决屏适配的尺寸单位。为了实现屏幕的自动适配，rpx 把所有设备的屏幕，在宽度上等分为 750 份（即：当前屏幕的总宽度为 750rpx）。

+ 在较小的设备上，1rpx 所代表的宽度较小
+ 在较大的设备上，1rpx 所代表的宽度较大

小程序在不同设备上运行的时候，会自动把 rpx 的样式单位换算成对应的像素单位来渲染，从而实现屏幕适配。

rpx 与 px 之间的单位换算：

在 iPhone6 上，屏幕宽度为375px，共有 750 个物理像素，等分为750rpx。则：

750rpx = 375px = 750 物理像素
    1rpx = 0.5px  = 1物理像素

| **设备**      | rpx换算px (屏幕宽度/750) | px换算rpx (750/屏幕宽度) |
| ------------- | ------------------------ | ------------------------ |
| iPhone5       | 1rpx = 0.42px            | 1px  = 2.34rpx           |
| iPhone6       | **1rpx** **=** **0.5px** | **1px = 2rpx**           |
| iPhone6  Plus | 1rpx = 0.552px           | 1px  = 1.81rpx           |

官方建议：开发微信小程序时，设计师可以用 iPhone6 作为视觉稿的标准。

开发举例：在 iPhone6 上如果要绘制宽100px，高20px的盒子，换算成rpx单位，宽高分别为 200rpx 和 40rpx。便于计算。

### 样式导入

使用 WXSS 提供的 `@import` 语法，可以导入外联的样式表。

`@import` 后跟需要导入的外联样式表的相对路径，用 ; 表示语句结束。示例如下：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601224526642.png)

### 全局样式和局部样式

全局样式：定义在 `app.wxss` 中的样式为全局样式，作用于每一个页面。

局部样式：在页面的 `.wxss` 文件中定义的样式为局部样式，只作用于当前页面。

+ 当局部样式和全局样式冲突时，根据就近原则，局部样式会覆盖全局样式
+ 当局部样式的权重大于或等于全局样式的权重时，才会覆盖全局的样式

## 全局配置

### 常用的配置项

小程序根目录下的 app.json 文件是小程序的全局配置文件。常用的配置项如下：

+ `pages`记录当前小程序所有页面的存放路径
+ `window`全局设置小程序窗口的外观
+ `tabBar`设置小程序底部的  tabBar 效果
+ `style`是否启用新版的组件样式

### window

小程序窗口的组成部分

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601230608635.png)

#### window 节点常用的配置项

| **属性名**                   | **类型** | **默认值** | **说明**                                       |
| ---------------------------- | -------- | ---------- | ---------------------------------------------- |
| navigationBarTitleText       | String   | 字符串     | 导航栏标题文字内容                             |
| navigationBarBackgroundColor | HexColor | #000000    | 导航栏背景颜色，如 #000000                     |
| navigationBarTextStyle       | String   | white      | 导航栏标题颜色，仅支持 black / white           |
| backgroundColor              | HexColor | #ffffff    | 窗口的背景色                                   |
| backgroundTextStyle          | String   | dark       | 下拉 loading 的样式，仅支持 dark / light       |
| enablePullDownRefresh        | Boolean  | false      | 是否全局开启下拉刷新                           |
| onReachBottomDistance        | Number   | 50         | 页面上拉触底事件触发时距页面底部距离，单位为px |

### tabBar

tabBar 是移动端应用常见的页面效果，用于实现多页面的快速切换。小程序中通常将其分为：

+ 底部 tabBar
+ 顶部 tabBar

注意：
tabBar中只能配置最少 2 个、最多 5 个 tab 页签，**当渲染顶部 tabBar 时，不显示 icon，只显示文本**。

#### tabBar 的 6 个组成部分

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601230823751.png)

+ backgroundColor：tabBar 的背景色
+ selectedIconPath：选中时的图片路径
+ borderStyle：tabBar 上边框的颜色
+ iconPath：未选中时的图片路径
+ selectedColor：tab 上的文字选中时的颜色
+ color：tab 上文字的默认（未选中）颜色

#### tabBar 节点的配置项

| **属性**        | **类型** | **必填** | **默认值** | **描述**                                         |
| --------------- | -------- | -------- | ---------- | ------------------------------------------------ |
| position        | String   | 否       | bottom     | tabBar 的位置，仅支持 bottom/top                 |
| borderStyle     | String   | 否       | black      | tabBar 上边框的颜色，仅支持 **black/white**      |
| color           | HexColor | 否       |            | tab 上文字的默认（未选中）颜色                   |
| selectedColor   | HexColor | 否       |            | tab 上的文字选中时的颜色                         |
| backgroundColor | HexColor | 否       |            | tabBar 的背景色                                  |
| list            | Array    | 是       |            | **tab 页签的列表，  最少 2  个、最多  5 个 tab** |

#### 每个 tab 项的配置选项

| 属性             | 类型   | 必填 | 描述                                                  |
| ---------------- | ------ | ---- | ----------------------------------------------------- |
| pagePath         | String | 是   | 页面路径，页面必须在 pages  中预先定义                |
| text             | String | 是   | tab 上显示的文字                                      |
| iconPath         | String | 否   | 未选中时的图标路径；当 postion 为 top 时，不显示 icon |
| selectedIconPath | String | 否   | 选中时的图标路径；当 postion 为 top 时，不显示 icon   |

```json
  "tabBar": {
    "list": [{
      "pagePath": "pages/home/home",
      "text": "首页",
      "iconPath": "/images/tabs/home.png",
      "selectedIconPath": "/images/tabs/home-active.png"
    },{
      "pagePath": "pages/message/message",
      "text": "消息",
      "iconPath": "/images/tabs/message.png",
      "selectedIconPath": "/images/tabs/message-active.png"
    },{
      "pagePath": "pages/contact/contact",
      "text": "联系我们",
      "iconPath": "/images/tabs/contact.png",
      "selectedIconPath": "/images/tabs/contact-active.png"
    }]
  },
```

**含有tabBar页面必须放在pages的开头，否则可能会显示没有tabBar的页面，这样就无法跳转。**

## 页面配置

小程序中，app.json 中的 window 节点，可以全局配置小程序中每个页面的窗口表现。
如果某些小程序页面想要拥有特殊的窗口表现，此时，“页面级别的 .json 配置文件”就可以实现这种需求。**常用的配置项与全局配置中window配置项相同**。

## 网络数据请求

1.小程序中网络数据请求的限制

出于安全性方面的考虑，小程序官方对数据接口的请求做出了如下两个限制：

- 只能请求 HTTPS 类型的接口

- 必须将接口的域名添加到信任列表中

  ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230605214843974.png)

2.配置 request 合法域名

需求描述：假设在自己的微信小程序中，希望请求 https://www.escook.cn/ 域名下的接口

配置步骤：**登录微信小程序管理后台 -> 开发 -> 开发设置 -> 服务器域名 -> 修改 request 合法域名**

注意事项：

+ 域名只支持 https 协议
+ 域名**不能使用 IP 地址或 localhost**
+ 域名必须经过 ICP 备案
+ 服务器域名一个月内最多可申请 5 次修改

> 也可以跳过 request 合法域名校验
>
> 如果后端程序员仅仅提供了 http 协议的接口、暂时没有提供 https 协议的接口。
> 此时为了不耽误开发的进度，我们可以在微信开发者工具中，临时开启「开发环境不校验请求域名、TLS 版本及 HTTPS 证书」选项，跳过 request 合法域名的校验。
>
> ⚠注意：跳过 request 合法域名校验的选项，仅限在开发与调试阶段使用！

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230605220017253.png)

3.发起 GET 请求

调用微信小程序提供的 **wx.request()** 方法，可以发起 GET 数据请求，示例代码如下：

```html
<button bindtap="getInfo">发起GET请求</button>
```

```javascript
  // 发起GET数据请求
  getInfo() {
    wx.request({
      url: 'https://www.escook.cn/api/get',
      method: 'GET',
      data: {
        name: 'zs',
        age: 20
      },
      success: (res) => {
        console.log(res.data)
      }
    })
  },
```

4.发起 POST 请求

调用微信小程序提供的 **wx.request()** 方法，可以发起 POST 数据请求，示例代码如下：

```html
<button bindtap="postInfo">发起POST请求</button>
```

```javascript
// 发起POST请求
  postInfo() {
    wx.request({
      url: 'https://www.escook.cn/api/post',
      method: "POST",
      data: {
        name: 'ls',
        age: 33
      },
      success: (res) => {
        console.log(res.data)
      }
    })
  },
```

5.在页面刚加载时请求数据

在很多情况下，我们需要在页面刚加载的时候，自动请求一些初始化的数据。此时需要**在页面的 onLoad 事件中调用获取数据的函数**，示例代码如下：

```javascript
  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    // 填写下面的调用
    this.getInfo()
    this.postInfo()
  },
```

6.关于跨域和 Ajax 的说明

跨域问题只存在于基于浏览器的 Web 开发中。由于小程序的宿主环境不是浏览器，而是微信客户端，所以小程序中不存在跨域的问题。

Ajax 技术的核心是依赖于浏览器中的 XMLHttpRequest 这个对象，由于小程序的宿主环境是微信客户端，所以小程序中不能叫做“发起 Ajax 请求”，而是叫做“发起网络数据请求”。

## 案例 - 本地生活

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230605223328365.png)

+ 新建项目并梳理项目结构
+ 配置导航栏效果
+ 配置 tabBar 效果
+ 实现九宫格效果
+ 实现图片布局

app.json

```json
{
  "pages": [
    "pages/home/home",
    "pages/message/message",
    "pages/contact/contact"
  ],
  "window": {
    "backgroundTextStyle": "light",
    "navigationBarBackgroundColor": "#2b4b6b",
    "navigationBarTitleText": "本地生活",
    "navigationBarTextStyle": "white"
  },
  "tabBar": {
    "list": [
      {
        "pagePath": "pages/home/home",
        "text": "首页",
        "iconPath": "/images/tabs/home.png",
        "selectedIconPath": "/images/tabs/home-active.png"
      },
      {
        "pagePath": "pages/message/message",
        "text": "消息",
        "iconPath": "/images/tabs/message.png",
        "selectedIconPath": "/images/tabs/message-active.png"
      },
      {
        "pagePath": "pages/contact/contact",
        "text": "联系我们",
        "iconPath": "/images/tabs/contact.png",
        "selectedIconPath": "/images/tabs/contact-active.png"
      }
    ]
  },
  "style": "v2",
  "sitemapLocation": "sitemap.json"
}
```

wxml

```html
<!--pages/home/home.wxml-->
<!-- 轮播图区域 -->
<swiper indicator-dots circular>
  <swiper-item wx:for="{{swiperList}}" wx:key="id">
    <image src="{{item.image}}"></image>
  </swiper-item>
</swiper>

<!-- 九宫格区域 -->
<view class="grid-list">
  <view class="grid-item" wx:for="{{gridList}}" wx:key="id">
    <image src="{{item.icon}}"></image>
    <text>{{item.name}}</text>
  </view>
</view>

<!-- 图片区域 -->
<view class="img-box">
  <image src="/images/link-01.png" mode="widthFix"></image>
  <image src="/images/link-02.png" mode="widthFix"></image>
</view>

```

js

```javascript
// pages/home/home.js
Page({

  /**
   * 页面的初始数据
   */
  data: {
    // 存放轮播图数据的列表
    swiperList: [],
    // 存放九宫格数据的列表
    gridList: []
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    this.getSwiperList()
    this.getGridList()
  },

  // 获取轮播图数据的方法
  getSwiperList() {
    wx.request({
      url: 'https://www.escook.cn/slides',
      method: 'GET',
      success: (res) => {
        this.setData({
          swiperList: res.data
        })
      }
    })
  },

  // 获取九宫格数据的方法
  getGridList() {
    wx.request({
      url: 'https://www.escook.cn/categories',
      method: 'GET',
      success: (res) => {
        this.setData({
          gridList: res.data
        })
      }
    })
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {

  },

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {

  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {

  },

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {

  },

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {

  },

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {

  },

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {

  }
})
```

wxss

```css
/* pages/home/home.wxss */
swiper {
  height: 350rpx;
}

swiper image {
  width: 100%;
  height: 100%;
}

.grid-list {
  display: flex;
  flex-wrap: wrap;
  border-left: 1rpx solid #efefef;
  border-top: 1rpx solid #efefef;
}

.grid-item {
  width: 33.33%;
  height: 200rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-right: 1rpx solid #efefef;
  border-bottom: 1rpx solid #efefef;
  box-sizing: border-box;
}

.grid-item image {
  width: 60rpx;
  height: 60rpx;
}

.grid-item text {
  font-size: 24rpx;
  margin-top: 10rpx;
}

.img-box {
  display: flex;
  padding: 20rpx 10rpx;
  justify-content: space-around;
}

.img-box image {
  width: 45%;
}
```

