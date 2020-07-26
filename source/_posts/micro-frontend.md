---
title: 第五期 - 「面向大型中后台应用微前端解决方案」 - (2020.4.11)
date: 2020/04/11
categories :
- 分享专栏
tags: ['Zebra', '微前端','前端框架'] 
---

<br />![PPT首页.png](/images/zebra/5/banner.png)<br />

> 本文是第五期分享主题 - 「面向大型中后台应用微前端解决方案」的文字稿，可以通过查看来巩固知识点。

# 一、前言

**本文是根据2020.04.11 日，第三期分享主题「面向大型中后台应用微前端解决方案****」整理而来的文字稿。**

本文分享以 “业务背景”和“应用实践” 为切入点，对大型中后台业务场景被背景，技术方案调研为前提，以实践为引导，带你定制开发最优微前端解决方案。

# 二、介绍

## 1. 我是谁？

SCM，2009年参加工作，直到2014年中旬前主要从事后端JAVA开发，期间善于使用ExtJs，那会还没有明确的前后端之分，处于全栈开发时期。一个偶然的契机，接手了公司的前端框架，出于些特殊原因，要重新写一套适合公司的框架，EraJs应运而生，这目前还是公司占80%以上项目PC WEB项目的首选框架。这一从事前端框架开发，2014 ~ 至今，6年过去了，期间整过Jquery、Bootstrap、Zepto、Jquery UI、第三方UI插件等等源码，也产出了Eui1.0、T2.0、T3.0、T3.5(T系列框架主要使用于PC客户端)。构建从Grunt到后来的Gulp，再到现在的Webpack，现在主攻React方向中后端最佳解决方案，可视化开发、工程化建设、基建完善。

## 2. 我们的团队

我所带领的前端框架组，目前主要3个人，主要服务于公司PC WEB、PC客户端项目框架支撑工作，逐渐完善框架体系，更好的提效，做好技术支撑工作。

## 3. 怎么联系我？

下面是我的微信二维码，对前端基建充满兴趣的可以加我交流，我们也会定期的分享更多框架相关的主题。<br />![WechatIMG1.jpeg](/images/wechat.jpeg#align=left&display=inline&height=296&name=WechatIMG1.jpeg&originHeight=896&originWidth=674&size=61371&status=done&style=none&width=223)<br />

# 三、主题背景

<br />有人说：抛开业务场景需求的技术方案(微前端)都是瞎扯，所以在介绍微前端解决方案之前，先来说说当时面临的业务背景是怎样的。<br />
<br />2017年的时候，公司决定使用混合模式h5 + electron来开发toC的PC桌面应用程序，慢慢覆盖以往c++开发的桌面程序，这个产品有几个特点：页面数非常多、未来还会不断迭代版本，二期、三期...，需要集成不通业务品种子业务应用，子应用page需要能混合在dashboard，应用之间存在通信，技术栈也可能会升级，由jquery 到 react，针对以上特点前端方案需要核心解决问题：<br />

- 用户端必须是「一个系统」的体验，从域名到交互体验
- 能够根据业务功能拆分多个子应用，每个子应用可以独立开发部署
- 子应用开发尽量保证跟传统开发保持一样的开发体验，避免开发者太多学习成本
- 子应用风格(皮肤主题)到功能体验保持一致交互规则
- 能够根据权限加载菜单、自由定制的dashboard，应用之间有统一的通信机制支撑，避免耦合，低耦合设计
- 兼容不同技术栈开发(框架都是由公司自研方案)
- 避免应用之间全局共享数据污染
-  。。。


<br />其中第二点提到拆分之应用是方案的重点，在产品不断迭代过程中，随着业务的扩展，这个工程的代码量随之会不断增加，如果统一一个前端工程来管理，必会遇到以下问题：<br />

- 代码量达到一定程度，单次构建时间很长，开发及发布打包效率严重降低
- 依赖的升级会影响整个应用，潜在风险增加
- 变成一个硕大的巨无霸应用，失去灵活性，无论是多人协作还是接入成本都会大大增加


<br />针对第五点根据权限自由组合，低耦合设计，这个是方案的核心，必须能够按需加载，保证系统的灵活性。<br />
<br />针对以上种种的场景需求背景，对开源的方案做了全面的调研。<br />

# 四、技术调研


## 4.1 单页面应用

<br />最初是jquery技术栈，不具可行性，潜在问题太多，比如：同一个页面需要在多个区域多次展示，随着功能的叠加，选择器效率大大降低，样式的互相影响也不可避免。<br />
<br />基于上述问题，以及场景需求，采纳多页面iframe加载方式(每个page为最小单元)。主应用提供统一的菜单及dashboard加载机制。<br />

## 4.2 iframe

<br />为了避免page之间互相影响，每个page采用统一公共的iframe加载。公共的iframe由框架设计提供相关API支撑，比如，提示、通信等。<br />
<br />在采用iframe来加载带来的一些问题：<br />

- 内部蒙层无法遮罩外部框架，同时布局居中
- page页面之间的交互通信
- 页面加载缓慢(这个在接受范围内，PC客户端均加载本地静态资源保持1秒内)


<br />这些问题，我们都不难解决，比如：把消息提醒、确认框、模态窗口等等，通过框架提供统一API由主应用统一处理；page之间低耦合交互通信，主应用提供统一的通信机制，由消息 + 事件机制来转发数据。<br />

## 4.3 微前端

<br />在2018年的时候，当时还没了解到“微前端”这个概念。至于今天比较活跃的single-spa、qiankun，它们关注点在于如何解决跨技术栈，减轻应用之间的关联，主应用足够简单。这点来说，我们要支持多应用共存，还存在一定的交互，通信有些相悖。<br />
<br />既然，没有直接可参考的解决方案，那我们就站在业务场景需求下，一个个去突破，解决。

# 五、自研方案

## 5.1 架构设计

<br />

![](/images/zebra/5/framework.png)

## 5.2 快速使用

### 5.2.1 环境准备

<br />安装[yo](https://yeoman.io)以及titanone脚手架：<br />

```bash
sudo npm i -g yo generator-titanone
```

### 5.2.2 创建工程

<br />通过命令行初始化一个框架工程：<br />

```bash
mkdir titanOne-apps && cd titanOne-apps
yo titanone
```

<br />根据提示选择适用环境"web" or "electron"，完成初始化后会自动安装依赖，进入到工程目录，执行命令`npm start`即可启动。<br />

### 5.2.3 创建子应用

<br />脚手架支持在工程目录下创建多个应用目录，需要在工程根目录下打开命令行并执行脚手架命令：<br />

```bash
yo titanone:ex
```

<br />执行命令之后，依次会提示：

1. 输入应用名称
1. 选择运行环境（electron/web）
1. 完成输入/选择


<br />按回车键确认，命令执行完毕，应用目录创建成功，并自动安装依赖包。<br />

### 5.2.4 相关配置

<br />每个子应用，都可以自定义一些相关功能点，比如：参数设置、预加载、初始化等。

**mapping.json配置文件**<br />
<br />mapping.json可以用来定义什么？预加载页面(始终隐藏状态)、自定义参数设置、嵌入预执行JS。
> 注意：仅针对该应用加载时生效，且配置项的路径，均相对views的路径。

| 配置项 | 类型 | 描述 | 实例 |
| --- | --- | --- | --- |
| preloads | Array | 首页加载时需要预载的页面队列 | ['/ws/index.html'] |
| settingPage | Object | 首页系统配置参数功能，支持加载应用的自定义配置参数页面，以标签页的方式加载 | { "url": "test/setting.html", "key": "appsetting", "name": "自定义配置" } |
| initjs | Array | 初始化JS(渲染工作台之前执行) | [ws/static/js/initParam.min.js"] |

> **注意：对应多应用整合的项目，避免引用公共js下的js文件，否则多项目多入口下容易发生冲突!!!**<br />

<br />开发阶段与打包阶段配置文件内容格式有所不同，注意，注意，注意。<br />
<br />**开发阶段：**<br />

```json
{
  "asarMaps":{
    //应用名
    "quickstart": {
      "route": "..",
      "initjs": "ws/static/js/init.bundle.js"
    }
  }
}
```

<br />**发布阶段：**
```json
{
	"initjs": "ws/static/js/init.min.js"
}
```

<br />initjs 所指向的js需遵守的格式，比如我们以子应用quickstart为例：<br />

```javascript
Eui.App.registerInitEntry("quickstart", {
    options: {
        initialized: false
    },
    handler: function () {
        return new Promise(function (resolve, reject) {
           	// 具体业务逻辑代码
            resolve();
        })
    }
});
```
> 备注：第一个参数为应用名称，第二个参数初始化需要执行的回调，handler必须return promise。在主应用加载过程中会执行promise，正常执行完成才会去加载主应用界面。


<br />**setting.json配置文件**<br />**<br />PC客户端中，支持默认的系统配置参数，同时也支持应用扩展自定义参数配置。默认的配置参数在**系统参数**标签页中，自定义的配置参数，根据配置创建自定义的标签页。<br />
<br />**如何开启参数配置功能？**<br />

```json
userBarControl: {
   settingConfig: true
}
```

<br />项目应用扩展自定义配置参数，首先需要在`mapping.json`中配置下列配置项：
```json
"settingPage": {
  "url": "config/setting.html",
  "key": "appsetting",
  "name": "自定义配置"
}
```

<br />**应用中如何获取自定义配置项？**<br />**<br />通过框架提供的`Eui.Setting.get`方法可获取指定参数的值。

```javascript
// 缓存中存储的实际key为:'system:param:widgetZoom' 
const widgetZoom = Eui.Setting.get('widgetZoom'); 
```


### 5.2.5 全局缓存数据

<br />因为我们是基于iframe设计，所以全局的缓存数据(字典数据)一般会存在在localStorage，在多应用场景下，很难避免存储的key不重复，可通过实例化new一个CacheMemory实例来操作，这一般我们会写在公共的js中。<br />

```javascript
const CacheMemory = require('@erayt/titanOne-common/lib/cacheMemory');
Eui.QkShare = new CacheMemory("quickstart:share");
```
> 说明：上述代码，我们实例化了个操作LocalStorage的实例。存储的key将会统一加上"quickstart:share"前缀。

### 5.2.6 全局注册订阅WS

<br />特殊情况下，我们需要在全局接收ws服务推送过来的消息数据等，因此会在子应用initjs中去注册并订阅ws的操作，在多子应用存在同样操作，会导致数据重复接收。<br />
<br />框架提供了优化方案，可通过调用Eui.App.subscribeGlobalWsTitles方法来优化。

| 参数 | 类型 | 描述 |
| --- | --- | --- |
| titleMaps | Array | _title与回调关系，二维数组格式，包括_[title, handler] |
| index | Number | _实例索引，非多实例下可不传_ |

> 备注：titleMaps 为二维数组，包括[title, handler]， 即每个title与回调函数；index 可不传，在多ws实例情况下才需要传递实例索引。

```javascript
/** * 
* 订阅并注册全局title。
* @param {Array}  titleMaps title与回调关系 
* @param {Number} [index] 实例索引，非多实例下可不传 
*/
subscribeGlobalWsTitles: function (titleMaps = [], index)
```

<br />比如：
```javascript
Eui.App.subscribeGlobalWsTitles([
	['b', function(e, arg){}],
  ['c', function(e, arg){}]
]);
```

<br />
<br />
<br />我们需要在Eui.App.initCompleted 方法的实现中去调用方法registerGlobalWsTitles:_
```javascript
Eui.App.initCompleted = function(){
  Eui.App.registerGlobalWsTitles();
}
```

> **备注：Eui.App.subscribeGlobalWsTitles **需要在initjs 中的中调用，多应用情况下重复定义initCompleted不会影响。

<br />
### 5.3 沙箱与隔离

<br />说到微前端一定逃不了沙箱的相关问题，针对这个业界也没有一个完整的机制，一般谈论多的事样式和脚本隔离，在我们特殊的场景下有一些自己的探索实践。<br />

- 业务没有接入第三方需求，所有业务场景需要依赖主应用能力，技术栈统一(公司内部标准解决方案)
- 对于子应用接入需要全局缓存数据，我们提供不同命名空间前缀来区分隔离
- 对于全局ws动态注册，我们提供API来缓存关系，统一initComplated事件再初始化
- 各子应用page均基于iframe加载，不存在样式冲突，样式统一主题包(less主题变量)，对外各应用无差异化交互
- 主框架支持默认样式重写覆盖，提供唯一入口一次覆盖
- 依托于权限系统，动态加载权限菜单
- 桌面工作台基于布局数据动态渲染，提供灵活用户个性化定制需求

## 5.4 与开源方案的差异

<br />目前社区开源方案主要以single-spa、qiankun、icestark活跃度高，这里简单梳理下与它们的差异：<br />

- 目标方向：开源微前端方案，以支持跨技术栈，无侵入子应用为目标，讲究主应用足够简单，titanOne更关注于统一技术栈前提下，微应用最佳实践，重复应用功能高度抽象提取，统一交互，灵活组合为目标
- 职责方向：开源方案讲究只做布局以及子应用的注册管理，不做其他任何UI或者逻辑，而titanOne微前端方案，在支撑应用拆解前提下，还需要还支撑通用主功能，提供更灵活的组合及应用之间无差感的体验

# 六、业务落地

<br />titanOne微前端解决方案，目前只服务于公司内部业务。大多是银行对私业务应用，这里以对公业务为例。<br />
<br />![image.png](/images/zebra/5/app.png)<br />
<br />

![image.png](/images/zebra/5/app2.png)

# 七、微前端未来

<br />微前端百家争鸣主要都在解决工程问题，比如：系统的解耦、探索沙箱、接入二方业务，让运行环境更加安全，同时让不可控的第三方业务接入逐渐成为可能。在接下来在我们内部追求：让jquery技术栈与react技术栈集成更加稳定，历史遗留项目，快速安全稳定的升级到兼容版本。<br />

# 八、相关链接

- [single-spa站点](https://single-spa.js.org)
- [umi qiankun](https://github.com/umijs/qiankun)
- [飞冰 icestark](https://ice.work/docs/icestark/about)

