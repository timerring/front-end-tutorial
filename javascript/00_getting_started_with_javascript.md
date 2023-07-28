- [JavaScript 简介](#javascript-简介)
  - [JavaScript 的作用](#javascript-的作用)
  - [浏览器执行 JavaScript 简介](#浏览器执行-javascript-简介)
  - [JS 的组成](#js-的组成)
    - [ECMAScript](#ecmascript)
    - [DOM](#dom)
    - [BOM](#bom)
  - [JS 的书写位置](#js-的书写位置)
    - [行内式 JS](#行内式-js)
    - [内嵌 JS](#内嵌-js)
    - [外部 JS 文件](#外部-js-文件)
  - [注释](#注释)
  - [结束符](#结束符)
  - [输入输出语句](#输入输出语句)
- [变量](#变量)
  - [特殊情况](#特殊情况)
  - [数组](#数组)
  - [变量声明](#变量声明)
  - [变量命名规范](#变量命名规范)
- [常量](#常量)
- [解释型语言和编译型语言](#解释型语言和编译型语言)
- [标识符](#标识符)
- [关键字](#关键字)
- [保留字](#保留字)


# JavaScript 简介
布兰登·艾奇（Brendan Eich，1961 年～），10 天完成 JavaScript 设计。
+ 最初命名为 LiveScript，后来在与 Sun 合作之后将其改名为 JavaScript。
+ JavaScript 是世界上最流行的语言之一，是一种运行在客户端的脚本语言 （Script 是脚本的意思），现在也可以基于 Node. Js 技术进行服务器端编程。
+ 脚本语言：不需要编译，运行过程中由 js 解释器 ( js 引擎）逐行来进行解释并执行。
## JavaScript 的作用

1. 表单动态校验（密码强度检测） （ JS 产生最初的目的 ）
2. 网页特效
3. 服务端开发 (Node. Js)
4. 桌面程序 (Electron)
5. App (Cordova)
6. 控制硬件-物联网 (Ruff)
7. 游戏开发 (cocos 2 d-js)

## 浏览器执行 JavaScript 简介

浏览器分成两部分：**渲染引擎**和 **JS 引擎**。
- 渲染引擎：用来解析 HTML 与 CSS，俗称内核，比如 chrome 浏览器的 blink ，老版本的 webkit
- JS 引擎：也称为 JS 解释器。用来读取网页中的 JavaScript 代码，对其处理后运行，比如 chrome 浏览器的 V 8。
浏览器本身并不会执行 JS 代码，而是通过内置 JavaScript 引擎 (解释器) 来执行 JS 代码。JS 引擎执行代码时**逐行解释每一句源码**（转换为机器语言），然后由计算机去执行。

## JS 的组成
![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230707163035.png)
### ECMAScript

ECMAScript 是由 ECMA 国际（ 原欧洲计算机制造商协会）进行标准化的一门编程语言，这种语言在万维网上应用广泛，它往往被称为 JavaScript 或 JScript，但实际上后两者是 ECMAScript 语言的实现和扩展。**ECMAScript 规定了 JS 的编程语法和基础核心知识，是所有浏览器厂商共同遵守的一套 JS 语法工业标准**。

> 更多可以参考： https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/JavaScript_technologies_overview

### DOM

文档对象模型（Document Object Model，简称 DOM），是 W 3 C 组织推荐的处理可扩展标记语言的标准编程接口。通过 DOM 提供的接口可以对页面上的各种元素进行操作（大小、位置、颜色等）。

### BOM

BOM (Browser Object Model，简称 BOM) 是指浏览器对象模型，它提供了独立于内容的、可以与浏览器窗口进行互动的对象结构。通过 BOM 可以操作浏览器窗口，比如弹出框、控制浏览器跳转、获取分辨率等。

## JS 的书写位置

### 行内式 JS

```js
<input type="button" value="点我试试" onclick="alert('Hello World')" />
```

- 可以将单行或少量 JS 代码写在 HTML 标签的事件属性中（以 on 开头的属性），如：onclick
- 注意单双引号的使用：**在 HTML 中推荐使用双引号, JS 中推荐使用单引号**
>缺点
> - 可读性差，在 html 中编写 JS 大量代码时，不方便阅读；
> - 引号易错，引号多层嵌套匹配时，非常容易弄混；

### 内嵌 JS

```js
 <script>
	alert('Hello  World~!');
</script>
```

可以将多行 JS 代码写到 `<script>` 标签，常用。

### 外部 JS 文件

```js
<script src="my.js"></script>
```

利于 HTML 页面代码结构化，把大段 JS 代码独立到 HTML 页面之外，方便文件级别的复用，**引用外部 JS 文件的 script 标签中间不可以写代码**。

## 注释

单行注释：`//` 快捷键 `ctrl + /`
多行注释：`/* */` 快捷键修改为 `ctrl + shift + /`

## 结束符

在 JavaScript 中 `;` 代表一段代码的结束，多数情况下可以省略 `;` 使用回车（enter）替代。

```javascript
  <script> 
    alert(1);
    alert(2);
    alert(1)
    alert(2)
  </script>
```

## 输入输出语句

+ `alert (msg)`: 浏览器弹出警示框
+ `document.write()`: 页面上文档显示
+ `console.log(msg)`: 浏览器控制台打印输出信息
+ `prompt(info)`: 浏览器弹出输入框，用户可以输入

# 变量

变量在使用时分为两步： 1. 声明变量 2. 赋值（初始化）
`var` 是一个 JS 关键字，用来声明变量 ( **variable 变量的意思** )。使用该关键字声明变量后，计算机会自动为变量分配内存空间。

```javascript
let age; // 声明一个 名称为age 的变量
var age; // 声明一个 名称为age 的变量
```

同时声明多个变量时，只需要写一个 var，多个变量名之间使用英文逗号隔开。

```javascript
let age = 10, name = 'zs', sex = 2;
```

`let` 和 `var` 都是 JavaScript 中的声明变量的关键字，推荐使用 `let` 声明变量！！！

注意： let 不允许多次声明一个变量。

>Var 声明:
>- 可以先使用在声明 (不合理)
>- var 声明过的变量可以重复声明 (不合理)
>- 比如变量提升、全局变量、没有块级作用域等等
>
>以下是使用 `let` 时的注意事项：
>- 不允许重复声明
>- JavaScript 中内置的一些关键字不能被当做变量名

## 特殊情况

|情况|说明|结果|
|---|---|---|
|var age ; console. Log (age);|只声明不赋值|undefined|
|console.Log (age)|不声明不赋值直接使用|报错|
|age = 10; console. Log (age);|不声明只赋值|10|

## 数组

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230709155747.png)

## 变量声明

变量声明有三个 `var`，`let` 和 `const`
建议： const 优先，尽量使用 const，有了变量先给 const，如果发现它后面是要被修改的，再改为 let。Const 声明的值不能更改，而且 const 声明变量的时候需要里面进行初始化。**建议数组和对象使用 const 来声明**。
> 请问以下的可不可以把 let 改为 const？
> ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230710213057.png)
> 不可以，变量进行了重新赋值。
> ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230710213131.png)
> 可以，因为对于引用数据类型，const 声明的变量，里面存的不是值，而是**地址**，扩充实际上是栈中该地址对应的堆中的数组值，而不会修改该指向的地址。
> ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230710213751.png)
> 但是下面这种做法就是错误的，他们的地址其实不一样：
> ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230710214042.png)
> 考虑修改为：
> ![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230710214102.png)


## 变量命名规范

+ 由**字母 (A-Za-z)、数字 (0-9)、下划线 (\_)、美元符号 ( $ )组成**，如：usrAge, num 01, \_name
+ **严格区分大小写**。Var app; 和 var App; 是两个变量
+ **不能以数字开头**。 18 age 是错误的
+ 不能是关键字、保留字。例如：var、for、while
+ 遵守驼峰命名法。首字母小写，后面单词的首字母需要大写。 MyFirstName

# 常量

当某个变量永远不会改变的时候，就可以使用 const 来声明，而不是 let。

```javascript
const PI = 3.14
```

> 注意： 常量不允许重新赋值, 声明的时候必须赋值（初始化）


# 解释型语言和编译型语言

![](https://raw.githubusercontent.com/timerring/scratchpad2023/main/2023/20230708234543.png)

# 标识符

标识 (zhi)符：就是指开发人员为变量、属性、函数、参数取的名字。标识符不能是关键字或保留字。

# 关键字

关键字：是指 JS 本身已经使用了的字，不能再用它们充当变量名、方法名。

包括：break、case、catch、continue、default、delete、do、else、finally、for、function、if、in、instanceof、new、return、switch、this、throw、try、typeof、var、void、while、with 等。

# 保留字

保留字：实际上就是预留的“关键字”，意思是现在虽然还不是关键字，但是未来可能会成为关键字，同样不能使用它们当变量名或方法名。

包括：boolean、byte、char、class、const、debugger、double、enum、export、extends、Fimal、float、goto、implements、import、int、interface、long、mative、package、Private、protected、public、short、static、super、synchronized、throws、transient、 Volatile 等。

>注意：如果将保留字用作变量名或函数名，那么除非将来的浏览器实现了该保留字，否则很可能收不到任何错误消息。当浏览器将其实现后，该单词将被看做关键字，如此将出现关键字错误。

