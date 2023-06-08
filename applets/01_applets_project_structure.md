## 项目结构

## 了解项目的基本组成结构

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531152511190.png)

1. pages 用来存放所有小程序的页面
2. utils 用来存放工具性质的模块（例如：格式化时间的自定义模块）
3. app.js 小程序项目的入口文件
4. app.json 小程序项目的全局配置文件
5. app.wxss 小程序项目的全局样式文件
6. project.config.json 项目的配置文件
7. sitemap.json 用来配置小程序及其页面是否允许被微信索引

## 小程序页面的组成部分

小程序官方建议把所有小程序的页面，都存放在 pages 目录 中，以单独的文件夹存在，如图所示：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531152609916.png)

其中，每个页面由 4 个基本文件 组成，它们分别是：

1. `.js` 文件（页面的脚本文件，存放页面的数据、事件处理函数等）
2. `.json` 文件（当前页面的配置文件，配置窗口的外观、表现等）
3. `.wxml` 文件（页面的模板结构文件）
4. `.wxss` 文件（当前页面的样式表文件）

#### json配置文件的作用

json是一种数据格式，在实际开发中，json总是以 **配置文件**的形式出现。小程序项目中也不例外：通过不同的.json配置文件，可以对小程序项目进行不同级别的配置。

小程序项目中有4种json配置文件，分别是：

- 项目根目录的app.json配置文件；
- 项目根目录中的project.config.json配置文件；
- 项目根目录中的sitemap.json配置文件；
- **每个页面文件夹中的.json配置文件**

#### 全局配置文件app.json

app.json是当前小程序的全局配置 ，包括了小程序的所有页面路径 、窗口外观、界面表现 、底部 tab 等。 

app.json 中的部分配置，也支持对单个页面进行配置，可以在页面对应的 `.json` 文件来对本页面的表现进行配置。页面中配置项在当前页面会覆盖 `app.json` 中相同的配置项。

```json
{
  "pages":[
    "pages/index/index",
    "pages/logs/logs"
  ],
  "window":{
    "backgroundTextStyle":"light", //加载小圆圈的颜色
    "navigationBarBackgroundColor": "#fff", //导航栏背景颜色
    "navigationBarTitleText": "Weixin", //导航栏的文本内容
    "navigationBarTextStyle":"black" //导航栏标题颜色，仅支持 black / white
  },
  "style": "v2",
  "sitemapLocation": "sitemap.json"
}
```

这 4 个配置项的作用：

1. pages ：用来记录当前小程序所有页面的路径

   未指定 `entryPagePath` 时，数组的第一项代表小程序的初始页面（首页）。

   **小程序中新增/减少页面，都需要对 pages 数组进行修改。**

2. window ：全局定义小程序所有页面的背景色、文字颜色等

3. style ：全局定义小程序组件所使用的样式版本

4. sitemapLocation ：用来指明 sitemap.json 的位置

#### project.config.json文件

文件project.config.json是项目配置文件，用来记录我们对小程序开发工具所做的个性化配置 ，例如

+ setting 中保存了 编译相关的配置
+ projectname 中保存的是 项目名称
+ appid 中保存的是 小程序的账号 ID

```json
{
  "description": "项目配置文件",
  "packOptions": {
    "ignore": [],
    "include": []
  },
  "setting": {
    "bundle": false,
    "userConfirmedBundleSwitch": false,
    "urlCheck": true,
    "scopeDataCheck": false,
    "coverView": true,
    "es6": true,
    "postcss": true,
    "compileHotReLoad": false,
    "lazyloadPlaceholderEnable": false,
    "preloadBackgroundData": false,
    "minified": true,
    "autoAudits": false,
    "newFeature": false,
    "uglifyFileName": false,
    "uploadWithSourceMap": true,
    "useIsolateContext": true,
    "nodeModules": false,
    "enhance": true,
    "useMultiFrameRuntime": true,
    "useApiHook": true,
    "useApiHostProcess": true,
    "showShadowRootInWxmlPanel": true,
    "packNpmManually": false,
    "enableEngineNative": false,
    "packNpmRelationList": [],
    "minifyWXSS": true,
    "showES6CompileOption": false,
    "minifyWXML": true,
    "babelSetting": {
      "ignore": [],
      "disablePlugins": [],
      "outputPath": ""
    },
    "condition": false
  },
  "compileType": "miniprogram",
  "libVersion": "2.19.4",
  "appid": "",
  "projectname": "miniprogram-92",
  "condition": {},
  "editorSetting": {
    "tabIndent": "insertSpaces",
    "tabSize": 2
  }
}
```



#### sitemap.json 文件

微信现已开放小程序内搜索 ，效果类似于PC 网页的 SEO 。**sitemap.json 文件用来配置小程序页面是否允许微信索引 。**

当开发者允许微信索引时，微信会通过爬虫的形式，为小程序的页面内容建立索引。当用户的搜索关键字和页面的索引匹配成功的时候，小程序的页面将可能展示在搜索结果中。

```json
{
  "desc": "关于本文件的更多信息，请参考文档 https://developers.weixin.qq.com/miniprogram/dev/framework/sitemap.html",
  "rules": [{
  "action": "allow",
  "page": "*"
  }]
}
```

> 注意：**未显式指明 "disallow" 的都默认被索引**
>
> `sitemap` 的索引提示（终端黄色警告）是默认开启的，如需要关闭 sitemap 的索引提示，可在小程序项目配置文件 project.config.json 的 setting 中配置字段 checkSiteMap 为 false。

#### 页面的 .json 配置文件

小程序中的每一个页面，可以使用 .json 文件来 对本页面的窗口外观进行配置 ，**页面中的配置项会覆盖 app.json 的 window 中相同的配置项**。

新建小程序页面

只需要在 app.json -> pages 中新增页面的存放路径，小程序开发者工具即可帮我们自动创建对应的页面文件。

```json
  "pages":[
    "pages/index/index",
    "pages/list/list",
    "pages/logs/logs"
  ],
```

#### 修改项目首页

只需要调整 app.json -> pages 数组中页面路径的前后顺序，即可修改项目的首页。小程序会把排在第一位的页面，当作项目首页进行渲染。

### WXML 模板

#### 什么是 WXML

WXML（WeiXin Markup Language ）是小程序框架设计的一套标签语言 ，用来构建小程序页面的结构 ，其作用类似于网页开发中的 HTML 。

**WXML和HTML的区别**

1. 标签名称不同

   HTML(div, span, img, a)

   WXML(view, text, image, navigator)

2. 属性节点不同

   `<a href="#"></a>`

   ``<navigator url="pages/home/home"></navigator>`

3. 提供了类似于Vue中的模块语法

   数据绑定

   列表渲染

   条件渲染

### WXSS 样式

WXSS (WeiXin Style Sheets)是一套样式语言 ，用于描述 WXML 的组件样式，类似于网页开发中的 CSS 。

**WXSS和CSS的区别**

新增了rpx尺寸单位

- CSS需要手动进行像素单位换算，例如rem
- WXSS在底层支持新的尺寸单位rpx，在不同大小屏幕小程序会自动进行换算

提供了全局的样式和局部样式

- 全局：项目根目录中的app.wxss
- 局部：当前页面的.wxss

WXSS仅支持部分CSS选择器

+ .class 和 #id
+ element
+ 并集选择器、后代选择器
+ ::after 和 ::before 等

### JS逻辑交互

通过.js 文件来处理用户的操作。例如：响应用户的点击、获取用户的位置等等。

小程序中的JS文件分为三大类，分别是:

app.js

- 是整个小程序项目的入口文件，通过调用**App()函数**来启动整个小程序

页面的.js 文件

- 是页面的入口文件，通过调用**Page()函数**来创建并运行页面

普通的.js 文件

- 是普通的功能模块文件，用来封装公共的函数或属性供页面使用

## 小程序宿主环境

宿主环境就是依赖环境，小程序的宿主环境是手机微信

主要包含四个内容：`通信模型`，`运行机制`， `组件`， `API`

### 通信模型

通信的主体：`渲染层` 和 `逻辑层`

小程序中的通信模型分为两部分:

渲染层和逻辑层之间的通信由微信客户端进行转发。

逻辑层和第三方服务器之间的通信由微信客户端进行转发。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531160908137.png)

### 运行机制

小程序启动的过程

1. 把小程序的代码包下载到本地
2. 解析 `app.json` 全局配置文件
3. 执行 `app.js` 小程序入口文件， 调用 `App()` 创建小程序实例
4. 渲染小程序首页
5. 小程序启动完成

页面渲染的过程

1. 加载解析页面的 .json 配置文件
2. 加载页面的 .wxml 模板和 .wxss 样式
3. 执行页面的 .js 文件， 调用 Page() 创建页面实例
4. 页面渲染完成

### 组件

#### 小程序中组件的分类

小程序中的组件也是由宿主环境提供的，开发者可以基于组件快速搭建出漂亮的页面结构。官方把小程序的组件分为了 9 大类，分别是：

1. **视图容器**
2. **基础内容**
3. **表单组件**
4. **导航组件**
5. 媒体组件
6. map 地图组件
7. canvas 画布组件
8. 开放能力
9. 无障碍访问

#### 常用的视图容器类组件

1. view

   普通视图区域，类似于HTML中的div，是一个块级元素，常用来实现页面的布局效果。

   实现如图的 flex 横向布局

   ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531162526942.png)

2. scroll-view

   可滚动的视图区域，常用来实现滚动列表效果。

   - **scroll-y 或 scroll-x属性：规定在哪个方向滚动**
   - **要给swiper容器设置指定的高度或宽度才可以实现滚动**

   ```html
   <scroll-view class="container1" scroll-y>
     <view>A</view>
     <view>B</view>
     <view>C</view>
   </scroll-view>
   ```

   ```css
   /* pages/list/list.wxss */
   .container1 view {
     width: 100px;
     height: 100px;
     text-align: center;
     line-height: 100px;
   }
   
   .container1 view:nth-child(1) {
     background-color: lightgreen;
   }
   .container1 view:nth-child(2) {
     background-color: lightskyblue;
   }
   .container1 view:nth-child(3) {
     background-color: lightpink;
   }
   
   .container1 {
     border: 1px solid red;
     width: 100px;
     height: 120px;
   }
   ```

   ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531162544074.png)

3. swiper 和 swiper-item

   轮播图容器组件和轮播图item组件

   ```html
   <swiper class="swiper-container" indicator-dots indicator-color="white" indicator-active-color="gray" autoplay interval="3000" circular>
   	<swiper-item>
       <view class="item">A</view>
     </swiper-item>
   	<swiper-item>
       <view class="item">B</view>
     </swiper-item>
   	<swiper-item>
       <view class="item">C</view>
     </swiper-item>
   </swiper>
   ```

   ```css
   .swiper-container {
     height: 150px;
   }
   
   .item {
     height: 100%;
     line-height: 150px;
     text-align: center;
   }
   
   swiper-item:nth-child(1) .item {
     background-color: lightgreen;
   }
   swiper-item:nth-child(2) .item {
     background-color: lightskyblue;
   }
   swiper-item:nth-child(3) .item {
     background-color: lightpink;
   }
   ```

   ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531162853215.png)

   swiper组件的常用属性

   | 属性                   | 类型    | 默认值           | 说明                 |
   | ---------------------- | ------- | ---------------- | -------------------- |
   | indicator-dots         | boolean | false            | 是否显示面板指示点   |
   | indicator-color        | color   | rgba（0,0,0,.3） | 指示点颜色           |
   | indicator-active-color | color   | #000000          | 当前选中的指示点颜色 |
   | autoplay               | boolean | false            | 是否自动切换         |
   | interval               | number  | 5000             | 自动切换时间间隔     |
   | circular               | boolean | false            | 是否采用衔接滑动     |

#### 基础内容组件

- text：类似于 HTML 中的 span 标签，是一个行内元素。

  ```html
  <view>
    手机号支持长按选中效果
    <text selectable>13800005056</text>
  </view>
  ```

  - selectable属性：实现长按选中文本内容。

    ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531163949741.png)

- rich-text：富文本组件，支持把 HTML 字符串渲染为 WXML 结构。

  ```html
  <rich-text nodes="<h1 style='color: red;'>标题</h1>"></rich-text>
  ```

  - **nodes**属性：把HTML字符串渲染为对应的UI结构。

    ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531164001782.png)

#### 其它常用组件

- button

  ```html
  <!-- 按钮组件的基本使用 -->
  <!-- 通过 type 属性指定按钮颜色类型 -->
  <button>普通按钮</button>
  <button type="primary">主色调按钮</button>
  <button type="warn">警告按钮</button>
  <!-- size="mini" 小尺寸按钮 -->
  <button size="mini">普通按钮</button>
  <button type="primary" size="mini">主色调按钮</button>
  <button type="warn" size="mini">警告按钮</button>
  <!-- plain 镂空按钮 -->
  <button size="mini" plain>普通按钮</button>
  <button type="primary" size="mini" plain>主色调按钮</button>
  <button type="warn" size="mini" plain>警告按钮</button>
  ```

  - open-type属性：可以调用微信提供的各种功能（客服、转发、获取用户授权、获取用户信息等）
  - type属性：指定按钮颜色类型
    - primary：主色调按钮，绿色
    - warn：警告按钮，红色
  - size属性：
    - mini：小尺寸按钮
  - plain属性：镂空按钮

  ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531165032910.png)

- image

  ```html
  <!-- image 图片组件 -->
  <image></image>
  <image src="/images/1.png" mode="heightFix"></image>
  ```

  - 默认宽度300px，高度240px
  - mode属性：指定图片的裁剪和缩放模式
    - scaleToFill：默认值，缩放模式，不保持纵横比缩放图片，使完全填满image元素
    - aspectFit：保持纵横比缩放，将图片完整显示出来
    - aspectFill：保持纵横比缩放，使完全填满image元素，但是可能发生裁剪
    - widthFix：宽度不变，高度自动变化，保持宽高比不变
    - heightFix：高度不变，宽度自动变化，保持宽高比不变

  ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230531165043942.png)

- navigator
  - 页面导航组件，类似于a链接

### API

小程序官方把API分成三大类

1.事件监听API

- 特点：以**on**开头，用来监听某些事件的触发

  举例：`wx.onWindowResize(function callback)`监听窗口尺寸变化的事件

2.同步API

- 特点：以**Sync**结尾的API都是同步API，其执行结果可以通过函数返回值直接获取，如果执行出错会抛出异常
- 举例：`wx.setStorageSync('key','value')`向本地存储中写入内容

3.异步API

- 特点：类似于jQuery中的`$.ajax(optjions)`函数，需要通过**success**、**fail**、**complete**接受调用的结果
- 举例：`wx.request()`发起网络数据请求，通过success回调函数接收数据。

## 小程序成员管理

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601203349735.png)

项目成员 ：

+ 表示参与小程序开发、运营的成员
+ 可登录小程序管理后台
+ 管理员可以添加、删除项目成员，并设置项目成员的角色

体验成员 ：

+ 表示参与小程序内测体验的成员
+ 可使用体验版小程序，但不属于项目成员
+ 管理员及项目成员均可添加、删除体验成员

不同项目成员对应的权限

开发者的权限说明

1. 开发者权限 ：可使用小程序开发者工具及对小程序的功能进行代码开
2. 体验者权限 ：可使用体验版小程序
3. 登录权限 ：可登录小程序管理后台，无需管理员确认
4. 开发设置 ：设置小程序服务器域名、消息推送及扫描普通链接二维码打开小程序
5. 腾讯云管理：云开发相关设置

## 小程序的版本

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601203805483.png)

## 发布上线

一个小程序的发布上线，一般要经过 上传代码 -> 提交审核 -> 发布 这三步。

上传代码

1. 点击开发者工具顶部工具栏中的“ 上传 ” 按钮
2. 填写 版本号 以及 项目备注

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601203919483.png)

在后台查看上传之的版本

登录小程序管理后台 -> 管理 -> 版本管理 -> 开发版本 ，即可查看刚才提交上传的版本：

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601204144316.png)

提交审核

提交审核的方式：在开发版本的列表中，点击“ 提交审核 ”按钮之后，按照页面提示填写相关的信息，就能把小程序提交到官方进行审核。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601204214993.png)

发布

审核通过之后，管理员的微信中会收到小程序通过审核的通知，此时在审核版本的列表中，点击“发布 ”按钮之后，即可把“ 审核通过 ”的版本发布为 线上版本 ”，供所有小程序用户访问和使用。

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/image-20230601204240397.png)

基于小程序码进行推广

获取小程序码的 5 个步骤：登录小程序管理后台 -> 设置 -> 基本设置 -> 基本信息 -> 小程序码及线下物料下载。