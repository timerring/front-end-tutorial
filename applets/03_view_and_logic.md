# 小程序视图与逻辑

## 页面导航

页面导航指的是页面之间的相互跳转。例如，浏览器中实现页面导航的方式有如下两种：

+ `<a>` 链接
+ location.href

小程序中实现页面导航的两种方式

1. **声明式导航**：在页面上声明一个 `<navigator>` 导航组件，通过点击 `<navigator>` 组件实现页面跳转。
2. **编程式导航**：调用小程序的导航 API，实现页面的跳转。

### 声明式导航

1.导航到 tabBar 页面

tabBar 页面指的是被配置为 tabBar 的页面。

在使用 <navigator> 组件跳转到指定的 tabBar 页面时，需要指定 url 属性和 open-type 属性，其中：

+ url 表示要跳转的页面的地址，必须以 / 开头
+ open-type 表示跳转的方式，必须为 `switchTab`

```html
<navigator url="/pages/message/message" open-type="switchTab">导航到消息页面</navigator>
```

2.导航到非 tabBar 页面

非 tabBar 页面指的是没有被配置为 tabBar 的页面。

在使用 <navigator> 组件跳转到普通的非 tabBar 页面时，则需要指定 url 属性和 open-type 属性，基本同上，open-type 必须为 navigate。

```html
<navigator url="/pages/info/info" open-type="navigate">导航到info页面</navigator>
```

> 注意：为了简便，在导航到非 tabBar 页面时，open-type="navigate" 属性可以省略。

3.后退导航

如果要后退到上一页面或多级页面，则需要指定 open-type 属性和 delta 属性，其中：

+ open-type 的值必须是 navigateBack，表示要进行后退导航
+ delta 的值必须是数字，表示要后退的层级

```html
<navigator open-type="navigateBack">后退</navigator>
```

### 编程式导航

1.导航到 tabBar 页面

调用 `wx.switchTab(Object object)` 方法，可以跳转到 tabBar 页面。其中 Object 参数对象的属性列表如下：

| **属性** | **类型** | **是否必选** | **说明**                                           |
| -------- | -------- | ------------ | -------------------------------------------------- |
| url      | string   | 是           | 需要跳转的 tabBar 页面的路径，路径后**不能带参数** |
| success  | function | 否           | 接口调用成功的回调函数                             |
| fail     | function | 否           | 接口调用失败的回调函数                             |
| complete | function | 否           | 接口调用结束的回调函数（调用成功、失败都会执行）   |

```html
<button bindtap="gotoMessage">跳转到message页面</button>
```

```javascript
  gotoMessage() {
    wx.switchTab({
      url: '/pages/message/message'
    })
  },
```

2.导航到非 tabBar 页面

调用 `wx.navigateTo(Object object)` 方法，可以跳转到非 tabBar 的页面。其中 Object 参数对象的属性列表如下：

| **属性** | **类型** | **是否必选** | **说明**                                               |
| -------- | -------- | ------------ | ------------------------------------------------------ |
| url      | string   | 是           | 需要跳转到的非 tabBar 页面的路径，路径后**可以带参数** |
| success  | function | 否           | 接口调用成功的回调函数                                 |
| fail     | function | 否           | 接口调用失败的回调函数                                 |
| complete | function | 否           | 接口调用结束的回调函数（调用成功、失败都会执行）       |

```html
<button bindtap="gotoInfo">跳转到info页面</button>
```

```javascript
  gotoInfo() {
    wx.navigateTo({
      url: '/pages/info/info'
    })
  },
```

3.后退导航

调用 **wx.navigateBack(Object object)** 方法，可以返回上一页面或多级页面。其中 Object 参数对象可选的属性列表如下：

| **属性** | **类型** | **默认值** | **是否必选** | **说明**                                              |
| -------- | -------- | ---------- | ------------ | ----------------------------------------------------- |
| delta    | number   | 1          | 否           | 返回的页面数，如果 delta 大于现有页面数，则返回到首页 |
| success  | function |            | 否           | 接口调用成功的回调函数                                |
| fail     | function |            | 否           | 接口调用失败的回调函数                                |
| complete | function |            | 否           | 接口调用结束的回调函数（调用成功、失败都会执行）      |

```html
<button bindtap="goBack">后退</button>
```

```javascript
goBack() {
    wx.navigateBack({
      delta: 1
    })
  },
```

### 导航传参

声明式导航传参

navigator 组件的 url 属性用来指定将要跳转到的页面的路径。同时，路径的后面还可以携带参数：

+ **参数与路径之间使用 ? 分隔**
+ **参数键与参数值用 = 相连**
+ **不同参数用 & 分隔**

```html
<navigator url="/pages/info/info?name=zs&age=20">跳转到info页面</navigator>
```

编程式导航传参

调用 **wx.navigateTo(Object object)** 方法跳转页面时，也可以携带参数。

```html
<button bindtap="gotoInfo2">跳转到info页面</button>
```

```javascript
  gotoInfo2() {
    wx.navigateTo({
      url: '/pages/info/info?name=ls&gender=男'
    })
  },
```

在 onLoad 中接收导航参数

通过声明式导航传参或编程式导航传参所携带的参数，可以直接在 onLoad 事件中直接获取到：

```javascript
  data: {
    // 导航传递过来的参数对象
    query: {}
  },

  onLoad: function (options) {
    console.log(options)
    this.setData({
      query: options
    })
  },
```

## 页面事件

### 下拉刷新事件

下拉刷新是移动端的专有名词，指的是通过手指在屏幕上的下拉滑动操作，从而重新加载页面数据的行为。

启用下拉刷新

启用下拉刷新有两种方式：

+ 全局开启下拉刷新：在 app.json 的 window 节点中，将 enablePullDownRefresh 设置为 true
+ 局部开启下拉刷新：在页面的 .json 配置文件中，将 enablePullDownRefresh 设置为 true（推荐）

配置下拉刷新窗口的样式，在全局或页面的 .json 配置文件中，

+ backgroundColor 用来配置下拉刷新窗口的背景颜色，仅支持16 进制的颜色值
+ backgroundTextStyle 用来配置下拉刷新 loading 的样式，仅支持 dark 和 light

监听页面的下拉刷新事件

在页面的 .js 文件中，通过 `onPullDownRefresh()` 函数即可监听当前页面的下拉刷新事件。调用 `wx.stopPullDownRefresh()` 可以停止当前页面的下拉刷新。

例如，在页面的 wxml 中有如下的 UI 结构，点击按钮可以让 count 值自增 +1：

```html
<view>count值是：{{count}}</view>
<button bindtap="addCount">+1</button>
```

```javascript
  addCount() {
    this.setData({
      count: this.data.count + 1
    })
  },
      
   /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {
    // console.log('触发了message页面的下拉刷新')
    this.setData({
      count: 0
    })
    wx.stopPullDownRefresh()
  },
```

### 上拉触底事件

上拉触底是移动端的专有名词，通过手指在屏幕上的上拉滑动操作，从而加载更多数据的行为。

```javascript
  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {
    console.log('触发了上拉触底的事件')
  },
```

上拉触底距离指的是触发上拉触底事件时，滚动条距离页面底部的距离。可以在**全局或页面的 .json 配置文件**中，通过 `onReachBottomDistance` 属性来配置上拉触底的距离。小程序默认的触底距离是 50px，在实际开发中，可以根据需求修改这个默认值。

```json
{
  "usingComponents": {},
  "onReachBottomDistance": 150
}
```

#### 上拉触底案例

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606155256331.png)

+ 定义获取随机颜色的方法
+ 在页面加载时获取初始数据
+ 渲染 UI 结构并美化页面效果
+ **在上拉触底时调用获取随机颜色的方法**
+ **添加 loading 提示效果**
+ **对上拉触底进行节流处理**

步骤1 - 定义获取随机颜色的方法

```html
<view wx:for="{{colorList}}" wx:key="index" class="num-item" style="background-color: rgba({{item}});">{{item}}</view>
```





步骤2 - 在页面加载时获取初始数据

```javascript
  onLoad: function (options) {
    this.getColors()
  },
```

步骤3 - 渲染 UI 结构并美化页面效果

```css
.num-item {
  border: 1rpx solid #efefef;
  border-radius: 8rpx;
  line-height: 200rpx;
  margin: 15rpx;
  text-align: center;
  text-shadow: 0rpx 0rpx 5rpx #fff;
  box-shadow: 1rpx 1rpx 6rpx #aaa;
  color: black;
}
```

步骤4 - 上拉触底时获取随机颜色

```javascript
  onReachBottom: function () {
    this.getColors()
  },
```

步骤5 - 添加 loading 提示效果

步骤6 - 对上拉触底进行节流处理

在 data 中定义 `isloading` 节流阀

+ false 表示当前没有进行任何数据请求
+ true 表示当前正在进行数据请求

在 `getColors()` 方法中修改 `isloading` 节流阀的值

+ 在刚调用 `getColors` 时将节流阀设置 true
+ 在网络请求的 complete 回调函数中，将节流阀重置为 false

在 `onReachBottom` 中判断节流阀的值，从而对数据请求进行节流控制

+ 如果节流阀的值为 true，则阻止当前请求
+ 如果节流阀的值为 false，则发起数据请求

```javascript
  data: {
    colorList: [],
    isloding: false
  },
```

```javascript
  getColors() {
    this.setData({
      isloding: true
    })
    // 需要展示 loading 效果
    wx.showLoading({
      title: '数据加载中...'
    })
    wx.request({
      url: 'https://www.escook.cn/api/color',
      method: 'get',
      success: ({ data: res }) => {
        this.setData({
          colorList: [...this.data.colorList, ...res.data]
        })
      },
      complete: () => {
        wx.hideLoading()
        this.setData({
          isloding: false
        })
      }
    })
  },
```

```javascript
 /**
 	* 页面上拉触底事件的处理函数
 */
  onReachBottom: function () {
    if (this.data.isloding) return

    this.getColors()
  },
```

### 自定义编译模式

保证每次编译后都跳转到指定的页面，还可以初始一些传递参数。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606210523164.png)

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606210525533.png)

## 生命周期

在小程序中，生命周期分为两类，分别是：

+ 应用生命周期

  特指小程序从启动 -> 运行 -> 销毁的过程

+ 页面生命周期

  特指小程序中，每个页面的加载 -> 渲染 -> 销毁的过程

其中，页面的生命周期范围较小，应用程序的生命周期范围较大，如图所示：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606211255388.png)

生命周期函数：是由小程序框架提供的**内置函数**，会伴随着生命周期，自动**按次序执行**。

生命周期函数的作用：允许程序员**在特定的时间点，执行某些特定的操作。**例如，页面刚加载的时候，可以在 onLoad 生命周期函数中初始化页面的数据。

注意：**生命周期**强调的是时间段，**生命周期函数**强调的是时间点。

### 应用的生命周期函数

小程序的应用生命周期函数需要在 app.js 中进行声明，示例代码如下：

```javascript
//app.js
App({

  /**
   * 当小程序初始化完成时，会触发 onLaunch（全局只触发一次）
   */
  onLaunch: function () {
    // console.log('onLaunch')
  },

  /**
   * 当小程序启动，或从后台进入前台显示，会触发 onShow
   */
  onShow: function (options) {
    // console.log('onShow')
  },

  /**
   * 当小程序从前台进入后台，会触发 onHide
   */
  onHide: function () {
    // console.log('onHide')
  },

  /**
   * 当小程序发生脚本错误，或者 api 调用失败时，会触发 onError 并带上错误信息
   */
  onError: function (msg) {
    
  }
})

```

### 页面的生命周期函数

小程序的页面生命周期函数需要在页面的 .js 文件中进行声明，示例代码如下：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606211511030.png)

## WXS 脚本

WXS（WeiXin Script）是小程序独有的一套脚本语言，结合 WXML，可以构建出页面的结构。

**wxml 中无法调用在页面的 .js 中定义的函数，但是，wxml 中可以调用 wxs 中定义的函数。**因此，小程序中 wxs 的典型应用场景就是“过滤器”。

wxs 和 JavaScript 的关系，虽然 wxs 的语法类似于 JavaScript，但是 wxs 和 JavaScript 是完全不同的两种语言：

wxs 有自己的数据类型

+ number 数值类型、string 字符串类型、boolean 布尔类型、object 对象类型、
+ function 函数类型、array 数组类型、    date 日期类型、      regexp 正则

wxs 不支持类似于 ES6 及以上的语法形式

+ 不支持：let、const、解构赋值、展开运算符、箭头函数、对象属性简写、etc...
+ 支持：var 定义变量、普通 function 函数等类似于 ES5 的语法

wxs 遵循 CommonJS 规范

+ module 对象
+ require() 函数
+ module.exports 对象

### 基础语法

#### 内嵌 wxs 脚本

wxs 代码可以编写在 wxml 文件中的 `<wxs>` 标签内，就像 Javascript 代码可以编写在 html 文件中的 `<script>` 标签内一样。
wxml 文件中的每个 `<wxs></wxs>` 标签，必须提供 module 属性，用来指定当前 wxs 的模块名称，方便在 wxml 中访问模块中的成员：

```javascript
<view>{{m1.toUpper(username)}}</view>

<wxs module="m1">
  module.exports.toUpper = function(str) {
    return str.toUpperCase()
  }
</wxs>
```

#### 定义外联的 wxs 脚本

wxs 代码还可以编写在以 .wxs 为后缀名的文件内，就像 javascript 代码可以编写在以 .js 为后缀名的文件中一样。示例代码如下：tools.wxs

```javascript
function toLower(str) {
  return str.toLowerCase()
}

module.exports = {
  toLower: toLower
}
```

#### 使用外联的 wxs 脚本

在 wxml 中引入外联的 wxs 脚本时，必须为 `<wxs>` 标签添加 module 和 src 属性，其中：

+ module 用来指定模块的名称
+ src 用来指定要引入的脚本的路径，且必须是相对路径

```html
<view>{{m2.toLower(country)}}</view>

<wxs src="../../utils/tools.wxs" module="m2"></wxs>
```

### WXS 的特点

1.与 JavaScript 不同：本质上，wxs 和 JavaScript 是完全不同的两种语言！

2.不能作为组件的事件回调

wxs 典型的应用场景就是“过滤器”，经常配合 Mustache 语法进行使用，例如：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606213718319.png)

但是，在 wxs 中定义的函数不能作为组件的事件回调函数。例如，下面的用法是错误的：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606213838720.png)

3.隔离性：隔离性指的是 wxs 的运行环境和其他 JavaScript 代码是隔离的。体现在如下两方面：

+ wxs 不能调用 js 中定义的函数
+ wxs 不能调用小程序提供的 API

4.性能好：在 iOS 设备上，小程序内的 WXS 会比 JavaScript 代码快 2 ~ 20 倍，在 android 设备上，二者的运行效率无差异。

## 案例 - 本地生活

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230606221755709.png)

+ 页面导航并传参
+ 上拉触底时加载下一页数据
+ 下拉刷新列表数据

列表页面的 API 接口

以分页的形式，加载指定分类下商铺列表的数据：

+ 接口地址
  https://www.escook.cn/categories/:cate_id/shops
  URL 地址中的 :cate_id 是动态参数，表示分类的 Id
+ 请求方式
  GET 请求
+ 请求参数
  + `_page` 表示请求第几页的数据_
  + `_limit` 表示每页请求几条数据

判断是否还有下一页数据

如果下面的公式成立，则证明没有下一页数据了：

+ 页码值 * 每页显示多少条数据 >= 总数据条数
+ page * pageSize >= total

```html
<!--pages/shoplist/shoplist.wxml-->

<view class="shop-item" wx:for="{{shopList}}" wx:key="id">
  <view class="thumb">
    <image src="{{item.images[0]}}"></image>
  </view>
  <view class="info">
    <text class="shop-title">{{item.name}}</text>
    <text>电话：{{tools.splitPhone(item.phone)}}</text>
    <text>地址：{{item.address}}</text>
    <text>营业时间：{{item.businessHours}}</text>
  </view>
</view>

<wxs src="../../utils/tools.wxs" module="tools"></wxs>
```

```javascript
// pages/shoplist/shoplist.js
Page({

  /**
   * 页面的初始数据
   */
  data: {
    query: {},
    shopList: [],
    page: 1,
    pageSize: 10,
    total: 0,
    isloading: false
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    this.setData({
      query: options
    })
    this.getShopList()
  },

  // 以分页的形式获取商铺列表数据的方法
  // 传入cb函数参数
  getShopList(cb) {
    this.setData({
      isloading: true
    })
    // 展示 loading 效果
    wx.showLoading({
      title: '数据加载中...'
    })

    wx.request({
      url: `https://www.escook.cn/categories/${this.data.query.id}/shops`,
      method: 'GET',
      data: {
        _page: this.data.page,
        _limit: this.data.pageSize
      },
      success: (res) => {
        this.setData({
          shopList: [...this.data.shopList, ...res.data],
          total: res.header['X-Total-Count'] - 0
        })
      },
      complete: () => {
        // 隐藏 loading 效果
        wx.hideLoading()
        this.setData({ isloading: false })
        // wx.stopPullDownRefresh()
        // 如果cb存在，则调用cb函数
        cb && cb()
      }
    })
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {
    wx.setNavigationBarTitle({
      title: this.data.query.title
    })
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
    // 需要重置关键的数据
    this.setData({
      page: 1,
      shopList: [],
      total: 0
    })
    // 重新发起数据请求
    // 这里cb传入的是一个箭头函数
    this.getShopList(() => {
      wx.stopPullDownRefresh()
    })
  },

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {
    if (this.data.page * this.data.pageSize >= this.data.total) {
      // 证明没有下一页的数据了
      return wx.showToast({
        title: '数据加载完毕！',
        icon: 'none'
      })
    }
    // 判断是否正在加载其他数据
    if (this.data.isloading) return
    // 页码值 +1
    this.setData({
      page: this.data.page + 1
    })

    // 获取下一页数据
    this.getShopList()
  },

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {

  }
})
```

```json
{
  "usingComponents": {},
  "onReachBottomDistance": 200,
  "enablePullDownRefresh": true,
  "backgroundColor": "#efefef",
  "backgroundTextStyle": "dark"
}
```

```css
/* pages/shoplist/shoplist.wxss */
.shop-item {
  display: flex;
  padding: 15rpx;
  border: 1rpx solid #efefef;
  border-radius: 8rpx;
  margin: 15rpx;
  box-shadow: 1rpx 1rpx 15rpx #ddd;
}

.thumb image {
  width: 250rpx;
  height: 250rpx;
  display: block;
  margin-right: 15rpx;
}

.info {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  font-size: 24rpx;
}

.shop-title {
  font-weight: bold;
}
```

tools.wxs：

```javascript
function splitPhone(str) {
  if(str.length !== 11) return str

  var arr = str.split('')

  arr.splice(3, 0, '-')
  arr.splice(8, 0, '-')
  
  return arr.join('')
}

module.exports = {
  splitPhone: splitPhone
}
```

