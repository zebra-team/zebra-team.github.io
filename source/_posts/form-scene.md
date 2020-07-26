---
title: 第三期 - 「表单场景封装/应用深度解析」 - (2020.3.28)
date: 2020/07/27
categories :
- 分享专栏
tags: ['titanOne3.0', 'theme'] 
---

![PPT首页.png](/images/zebra/3/banner.png)

> 本文是第三期分享主题 - 表单场景封装/应用深度解析 的文字稿，可以通过查看来巩固知识点。

# 一、前言

**本文是根据2020.03.28日，第三期分享主题「表单****场景封装/应用深度解析****」整理而来的文字稿。**

本文分享以 “如何封装”和“应用” 为切入点，对表单常见场景，框架是如何提炼、抽象、封装展开话题，以思路引导为主，带你理解框架的思考及实现过程。

# 二、介绍

## 2.1. 我是谁？

SCM，2009年参加工作，直到2014年中旬前主要从事后端JAVA开发，期间善于使用ExtJs，那会还没有明确的前后端之分，处于全栈开发时期。一个偶然的契机，接手了公司的前端框架，出于些特殊原因，要重新写一套适合公司的框架，EraJs应运而生，这目前还是公司占80%以上项目PC WEB项目的首选框架。这一从事前端框架开发，2014 ~ 至今，6年过去了，期间整过Jquery、Bootstrap、Zepto、Jquery UI、第三方UI插件等等源码，也产出了Eui1.0、T2.0、T3.0、T3.5(T系列框架主要使用于PC客户端)。构建从Grunt到后来的Gulp，再到现在的Webpack，现在主攻React方向中后端最佳解决方案，可视化开发、工程化建设、基建完善。

## 2.2. 我们的团队

我所带领的前端框架组，目前主要3个人，主要服务于公司PC WEB、PC客户端项目框架支撑工作，逐渐完善框架体系，更好的提效，做好技术支撑工作。

## 2.3. 怎么联系我？

下面是我的微信二维码，对前端基建充满兴趣的可以加我交流，我们也会定期的分享更多框架相关的主题。<br />![WechatIMG1.jpeg](/images/wechat.jpeg#align=left&display=inline&height=296&name=WechatIMG1.jpeg&originHeight=896&originWidth=674&size=61371&status=done&style=none&width=223)<br />

# 三、主题背景

<br />在实际业务开发中，可能会存在以下疑问：<br />

- 重复的做crud功能，没技术含量可言，但是又只能继续coding
- 想提取封装，抽象代码能力有限，达不到最大程度封装
- 需求变更，导致每个功能点都要去改一遍
-  ...

<br /> 针对以上困惑，接下来会一步步带你打消你的疑问。 <br />

# 四、表单常见场景

<br />在中台应用中以下几种场景比较普遍：<br />

- 查询条件表单：作为表格数据查询条件表单，查询参数有固定前缀，比如：queryParam；
- 简单维护表单：维护类型表单，简单的单列或者多列布局方式，字段不会太多，对应后端单实体对象；
- 复杂表单：有复杂的布局，比如多标签(tabs)、带文件上传组件、甚至包含维护表格(可能是可编辑表格)的场景；
- 动态表单：存在联动操作，动态改变组件，可能是增加、删除组件，同时需动态添加或删除组件验证规则等。

![image.png](/images/zebra/3/01.png)<br />

# 五、表单加载过程分析


## 5.1 打开模态窗口加载过程介绍

![image.png](/images/zebra/3/01.png)<br />

## 5.2 loadForm方法详解

<br />**参数详解：**

```javascript
Eui.Form.loadForm 加载表单数据,批量设置值，方法及事件执行的顺序：

- `远程` : onBeforeLoad => showMask => onBeforeTransformData => setBindDatas => onLoadSuccess => setValues => onAfterLoad
- `本地`: onBeforeLoad => showMask => setValues => hideMask => setBindDatas => onAfterLoad

注意：事件return false,将不再继续往下执行.

@param {String/HTMLElement} target 选择器表达式或者HTMLElement对象.
@param {Object} options 参数配置项，具体查看子属性说明.
@param {String/Object/Function} options.data 请求地址或者数据也可以是方法（当为方法类型时提供'param'参数，结果必须返回对象）.
@param {Object} [options.params] 请求参数.
@param {String} [options.dataPrefix] 数据添加的前缀，在<code>setValues</code>前解析.
@param {Object} [options.events] 加载表单相关事件.
@param {Function} [options.events.onBeforeLoad] (Optional) 加载表单数据之前执行的事件，返回false来阻止表单数据加载.
@param {Object} [options.events.onBeforeLoad.param] 请求参数.
@param {Object} [options.events.onBeforeLoad.data] 数据或请求地址.
@param {Function} [options.events.onLoadSuccess] 远程获取表单数据成功时执行.
@param {Object} [options.events.onLoadSuccess.data] 数据.
@param {Object} [options.events.onLoadSuccess.originalData] 原始数据.
@param {Function} [options.events.onBeforeTransformData] 远程获取初始化数据时，转换数据(toDataObjects)之前执行事件，必须`return`处理后的数据对象，@since 2.0.0有效.
@param {Object} [options.events.onBeforeTransformData.data] 转换之前的原始数据对象.
@param {Function} [options.events.onAfterLoad] 表单加载数据批量赋值成功后执行，提供参数：data.
@param {Object} [options.events.onAfterLoad.data] 处理后的数据（多层级通过'.'连接）.
@param {Object} [options.events.onAfterLoad.originalData] 原始数据,当从远程获取数据时有效.
@param {Function} [options.events.onLoadError] 远程获取表单数据失败执行.
@param {Function} [options.events.onLoadFailure] 远程获取数据success状态为false，进入return false将不执行默认提示,@since 2.0.0有效.
@param {Object} [options.events.onLoadFailure.rslt] 后端返回的数据.

@param {String} [options.dataNode='data'] 指定初始化数据节点路径.
@param {Boolean} [options.showMask=true] 是否显示遮罩信息.
@param {String} [options.maskMsg='IN_INIT_DATA'] 默认遮罩信息
@param {Boolean} [options.isMerged=false] 调用{@link Eui.Form#setBindDatas}时是否合并原始绑定的数据(针对表单多次请求绑定数据有意义).
@param {Boolean} [options.isBindOriginalData=true] 是否缓存原始数据，方便提交时做合并,默认与{@link Eui.Form#isBindOriginalData isBindOriginalData}一致.
@param {Array} [options.filterFields] 当`isBindOriginalData`为true时，过滤缓存数据指定字段,比如：radio,checkbox 标签未选中，序列化时是忽略的，如果缓存了，会导致值还是会传递.

```

<br />**图解如下：**<br />

![image.png](/images/zebra/3/03.png)<br />

# 六、表单提交过程可能发生的事

<br />![image.png](/images/zebra/3/04.png)<br />

# 七、封装方法原则

<br />![image.png](/images/zebra/3/05.png)<br /><br />

# 八、在公司最佳实践

<br />我们首先来看下，定义一个按钮打开一个模态框加载一个指定页面，是怎么定义的：<br />

```javascript
<button class="btn btn-danger" 
			data-toggle="modal"
     custom-options="
            btnType:'modal',
            langid:'ALERT_TITLE_SHOW',
            title: 'Validate Form',
            href:'base.html'
     "
 >打开模态框</button>

```

## 8.1 表单相关按钮扩展类型

![image.png](<br />![image.png](/images/zebra/3/06.png)<br />)

## 8.2 如何自定义扩展按钮类型

<br />**注册扩展按钮类型：**
```javascript
Eui.Extra.registerBtnType(
      '按钮类型', // 自定义按钮类型
      {
        options:{
            // 自定义配置项
        },
        handler: function(target,options){
            // 具体实现
        }
     }
 );

```

<br />按钮扩展类型(btnType)包括：'action','modal','native','query','submit'等，每个实现都包含以下属性：<br />
<br />options:默认配置项;
handler : 处理函数，包括：target,options两个参数.<br />
<br />**示例：定义一个按钮点击打开模态框 **
```html
 <button class="btn btn-danger" data-toggle="modal"
     custom-options="
        langid:'ALERT_TITLE_SHOW',
        title: 'Validate Form',
        href:'base.html'
     "
 >

```

<br />默认'btnType'的值"modal"，每个按钮都包括公共扩展属性'btnType','needAuthorized','controlTrigger'等， 详情可查看extBtnOptions介绍。

# 九、表单应用探索Formily

<br />纵观近几年前端技术发展方向，以及延伸到未来前端发展方向，我们可以总结为以下几个阶段： <br />

<br />![image.png](/images/zebra/3/07.png)<br />

- F2V(Function函数驱动视图，以jQuery为代表)
- D2V（Data数据驱动视图，以MVVM框架React、Angular、Vue为代表）
- C2V(Component组件驱动视图，以Ant Design、Fusion Design为代表)
- T2V（Template模板驱动视图，以Ant Design Pro等为代表）
- S2V（Scene场景驱动视图）
- M2V（Model模型驱动视图）以及 AI2V建设（AI智能驱动视图，比如以ImageCook为典型代表）。 


<br />各个阶段从下至上环环相扣，紧密依赖，虽然各层在同一时期都有建设，但最终上层建设的完善离不开下层建设的基础。  在组件化、模板化建设趋于完善的状态下，通过特定场景（例如表单、列表等）进行提效是当下中后台建设的关键，即当前处于S2V场景驱动视图建设的关键时期。能否建立一套标准方案，使协议通、物料通、能力通，是开箱即用SDK战役核心要解决的问题，而在中后台业务中，表单是任何业务都避免不了的一个基本需求。在当前表单方案需求多、效率低、性能差，各BU业务都在重复性建设的背景下，表单核心要实现的目标如下：
统一表单解决方案，具备高可用、高性能、高扩展性等特点，避免重复建设；
建立一套标准表单Schema描述协议，提升表单开发效率及动态化需求，为未来自动化、智能化提供协议基石；
统一表单Schema协议渲染引擎，生态表单组件无缝接入，提供自定义组件机制及接入能力；
不断完善表单开发周边工具生态，降低表单开发门槛，提升表单开发效率。 <br />

## 9.1 四层建设

<br />4层建设分别为协议层、工具层、渲染引擎及表单组件层。协议层主要定义了表单的描述协议，有10+协议规范，部分仍在提案中（比如布局方案），完备的描述能力，后面会有详细描述。工具层是指在协议的基础上，提供一个可视化的协议生成工具，具备schema快速生成、可视化配置及实时预览功能。渲染引擎是基于标准schema协议的渲染实现，所有在协议层定义的能力都将在这一层实现，同时能够对react生态表单输入组件及自定义组件无缝接入，同样以@formily/react-schema-renderer NPM包的形式提供。表单组件层是渲染层实现的核心依赖，表单组件层负责表单消息通信、状态管理及副作用的逻辑处理，这一层对上层业务一般可以不关心，以@formily/core 包向上层输出能力。 <br />

<br />![image.png](/images/zebra/3/08.png)<br />

## 9.1 适用场景

<br />在业界已有很多表单方案的情况下，什么情况适用Formily表单呢？  <br />

- 表单项不固定，对动态表单渲染有需求；
- 表单项特别多，对渲染性能有严苛要求；
- 对现有的表单组件不满意，寻求更好的解决方案。

<br /> 官网地址：https://formilyjs.org/<br />Github开源项目地址：https://github.com/alibaba/formily <br />

> 声明：第九篇章，引用自Formily 自[https://mp.weixin.qq.com/s/pBQNtE1gBwH89-8KV07eMA]() 的对外宣传。

**看完如果有启发，给辛勤的开源者送颗稻草吧**<br />👇
