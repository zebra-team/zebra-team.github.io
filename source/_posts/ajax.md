---
title: 第七期 - 「AJAX通讯组件封装及使用」- (2020.4.25)
date: 2020/04/25
categories :
- 分享专栏
tags: ['Zebra', '3.0', 'ajax'] 
---

![](/images/zebra/7/banner.png)

> 本文是第七期分享主题 - 「AJAX通讯组件封装场景及使用」的文字稿，可以通过查看来巩固知识点。

# 一、前言

**本文是根据2020.04.25 日，第三期分享主题「****AJAX通讯组件封装及使用****」整理而来的文字稿。**

本文分享以 “场景”和“实践” 为切入点，对AJAX常见使用场景分析及如何抽象封装使用展开话题，以思路引导为主，带你理解框架的思考及实现过程。<br />

# 二、AJAX概述

## 2.1 什么是AJAX？

AJAX（Asynchronous Javascript And XML）翻译成中文就是“异步Javascript和XML”。即使用Javascript语言与服务器进行异步交互，传输的数据为XML（当然传输的数据不只是XML，可以是jSon或纯文本text）。<br />

- 与服务器异步交互
- 浏览器页面局部刷新

## 2.2 同步、异步交互

- 同步交互：客户端发出一个请求后，需要等待服务器响应结束后，才能发出第二个请求
- 异步交互：客户端发出一个请求后，无需等待服务器响应结束，就可以发出第二个请求

## 2.3 AJAX常见场景

- 进行数据验证
- 表单提交
- 自动更新页面(比如：新闻)
- 请求数据局部更新结果(比如：查询表格展示)
- 。。。

# 三、HTTP库选型

根据技术栈不同我们可以选用不同的开源库来快速集成，比如：<br />

- Jquery
- Axios
- umi-request
- 。。。

## 3.1 jquery

## 3.2 Axios

[Axios](https://github.com/axios/axios) 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。<br />

_**Features:**_

- 从浏览器中创建 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- 从 node.js 创建 [http](http://nodejs.org/api/http.html) 请求
- 支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 [XSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery)

Using yarn:

```bash
yarn add axios
```

## 3.3 umi-request

[umi-request](https://github.com/umijs/umi-request/blob/HEAD/README_zh-CN.md)网络请求库，基于 fetch 封装, 兼具 fetch 与 axios 的特点, 旨在为开发者提供一个统一的api调用方式, 简化使用, 并提供诸如缓存, 超时, 字符编码处理, 错误处理等常用功能.

_**支持功能：**_

- url 参数自动序列化
- post 数据提交方式简化
- response 返回处理简化
- api 超时支持
- api 请求缓存支持
- 支持处理 gbk
- 类 axios 的 request 和 response 拦截器(interceptors)支持
- 统一的错误处理方式
- 类 koa 洋葱机制的 use 中间件机制支持
- 类 axios 的取消请求
- 支持 node 环境发送 http 请求

# 四、我们的实践

我们的请求封装组件是基于jquery ajax封装的，首先来了解jquery的几个相关方法post、get、ajax。

## 4.1 jquery相关方法介绍

### 4.1.1 post方法

通过远程 HTTP POST 请求载入信息。这是一个简单的 POST 请求功能以取代复杂 $.ajax 。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 $.ajax。<br />

| 参数 | 描述 | 类型 |
| --- | --- | --- |
| url | 发送请求地址 | String |
| data | 待发送 Key/value 参数(可选项) | Map |
| callback | 发送成功时回调函数(可选项) | Function |
| type | 返回内容格式，xml, html, script, json, text, _default(可选项) | String |

<br />向页面 test.php 发送数据，并输出结果（HTML 或 XML，取决于所返回的内容）：
```javascript
$.post("test.php", { name: "John", time: "2pm" },
    function(data){
  		alert("Data Loaded: " + data);
    }
);
```

### 4.1.2 get方法

<br />通过远程 HTTP GET 请求载入信息。这是一个简单的 GET 请求功能以取代复杂 $.ajax 。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 $.ajax。
> 备注：参数与post方法一致，这里不做重复介绍。

```javascript
$.get("test.php", { name: "John", time: "2pm" },
    function(data){
  	    alert("Data Loaded: " + data);
    }
);
```

### 4.1.3 ajax方法

通过 HTTP 请求加载远程数据。jQuery 底层 AJAX 实现。简单易用的高层实现见 $.get, $.post 等。$.ajax() 返回其创建的 XMLHttpRequest 对象。大多数情况下你无需直接操作该函数，除非你需要操作不常用的选项，以获得更多的灵活性。<br />
最简单的情况下，$.ajax()可以不带任何参数直接使用。<br />

**注意**，所有的选项都可以通过$.ajaxSetup()函数来全局设置。<br />
**回调函数**
<br />如果要处理$.ajax()得到的数据，则需要使用回调函数。beforeSend、error、dataFilter、success、complete。<br />

- beforeSend 在发送请求之前调用，并且传入一个XMLHttpRequest作为参数
- error 在请求出错时调用。传入XMLHttpRequest对象，描述错误类型的字符串以及一个异常对象（如果有的话）
- dataFilter 在请求成功之后调用。传入返回的数据以及"dataType"参数的值。并且必须返回新的数据（可能是处理过的）传递给success回调函数
- success 当请求之后调用。传入返回后的数据，以及包含成功代码的字符串
- complete 当请求完成之后调用这个函数，无论成功或失败。传入XMLHttpRequest对象，以及一个包含成功或错误代码的字符串。

## 4.2 我们的需求

- 支持多上下文且发布阶段上下文可能会变动
- 提交参数统一需要做warp操作，比如：{'id':'12','name':'metro'} 转换为{reqJson : {'id':'12','name':'metro'}}
- 需要对返回报文(message、success)做全局解析(session失效，后端返回统一报文{success:false,message:'error.user.notLogin'}则作出相应动作，重登框自动弹出等)
- 统一对返回message节点或者success: false情况下需要做统一提示(避免业务开发中重复判断处理)
- error情况下，异常信息收集记录日志(文件或接口持久化)
- 兼容不同平台web or electron
- 加密请求参数及解密返回报文
- ......

## 4.3 我们的设计

基于需要满足上述各需求，我们初步从以下节点入手封装。<br />

### 4.3.1 支持多上下文

不同上下文或者不同平台，最终涉及到都是url请求地址的拼接，对于暴露给开发者定义url时，我们只需定义接口路径，比如：getVersion.action，至于是何上下文呢，可以分为以下几种场景：<br />

- 当前作用域内接口同一上下文
- 有某几个特殊接口上下文不同

出于以上场景，我们可以约定全局上下文，比如：defaultHostKey，针对特殊接口可以调用时指定，比如：hostKey参数。
<br />对于上下文可能会发布前变动，我们可以支持全局环境变量可自定义。
<br />**_场景描述：_**<br />_全局默认上下文约定为"ecas"，发布阶段变更为"auth"。_<br />_<br />_**我们的实现：提供环境变量 proxy来支持转换别名**_

```javascript
"proxy": {
     "ecas": {
         "server": "http://192.168.10.129" //服务地址
         "alias": "auth"
     }
 }
```
> 备注：场景alias 服务别名，例如[http://192.168.10.129/ecas](http://192.168.10.129/ecas)转换成[http://192.168.10.129/](http://192.168.10.129/ecas51)auth
针对上述考虑，只需要对url进行处理即可很好的支持。

### 4.3.2 参数warp处理

根据后端的差异，大多后端服务需要做这样的处理，考虑个别项目可能不需要做这样统一处理。所以我们需要考虑项目可全局自定义(约定的Eui.Extra.warp即是这个目的)。
<br />**_Q: 全局考虑就够了吗_**

A: Say No ! 业务场景中会存在某些接口特殊不需要的情况，所以在考虑SDK API时，需要支持自定义传递来覆盖全局默认值。<br />

### 4.3.3 登录session超时处理

与后端认证协商一致，通过返回指定的报文可作为判断依据，如果全局来做呢？
<br />仔细的同学可能已经想到了利用ajax的Complete事件来实现。

是的，没错，针对这个我们提供了`_Eui.Comm._``_recvCompleteDispatch_`就是基于此事件全局实现。
 

```javascript
recvCompleteDispatch(xhr, status) {
    if (this.dataType === "json" && status === "success") {
      let response = xhr.responseJSON;
      const { message } = response;
      // 判断ecas未登录情况
      if (!Eui.isSuccess(response) && !Eui.isStableLogined(message)) {
        let action;
        // 免登录版本未登录，直接跳转到登录页
        if (!Eui.isLogin()) {
          action = 'login';
        } else if (Eui.Share.get('systemStatus') !== 'timeout') {
          Eui.Share.set('systemStatus', 'timeout');
          action = 'relogin';
        }
        // iframe情况下将消息发出通知主页，否则直接打开
        if (action) {
          if (Eui.iframe) {
            Eui.Ipc.send("ipc:interface:status", {
              url: this.url,
              message,
              action,
            });
          } else {
            Eui.openReloginWin(action, message === "error.user.sl.exit");
          }
        }
      }
    }
  },
```
> 备注：这里只贴出核心代码，其他相关间接调用代码不做解释。

### 4.3.4 success统一消息提醒

业务中，会存在这普遍交互要求，表单提交成功，给出相应的成功提示，如果都自己去判断处理，势必会写重复的方法调用。仔细一想，我们只需要将传递的success与约定默认的成功执行方法合并即可，考虑到特殊情况自行处理的，我们约定return false就不走默认处理逻辑。

```javascript
/**
   * 基于返回值分发成功回调,可重写来实现默认处理。
   *
   * @param {Object} response 后端返回数据
   * @param {Boolean} response.success  成功状态
   * @param {String} [response.message] 返回信息
   * @param {Object} response.data 数据节点
   *
   */
  recvSuccessDispatch(response) {
    const msg = response.message,
      { dataType, autoTip = true } = this,
      isSuccess = Eui.isSuccess(response);

    if (dataType === 'json') {
      Eui.Logger.log("debug", "ReqUrl => {%s}, Method type => {%s}, Params => {%o},Response data =>", this.url, this.type, Eui.Object.fromQueryString(this.data || ""), response);
    }

    //如果是未登录情况，不提示@since 3.1.4
    if (!Eui.isStableLogined(msg)) {
      return;
    }

    if (dataType === "json" && autoTip && (msg || !isSuccess)) {
      const infoType = isSuccess ? "info" : "warning",
        msgKey = msg || "EXECUTE_FAILRE",
        message = Eui.Messager.parseResultMsg(msgKey);

      Eui.Comm.showHttpResMsg(infoType, message);
    }
  },
```
> 备注：相关代码不做多余介绍。

### 4.3.5 error统一异常记录

```javascript
 /**
   * 基于返回值分发错误回调.
   * @param {Object} response  返回信息对象
   */
  recvErrorDispatch(response) {
    Eui.Logger.log("warn", "服务发生异常，状态码 => %s, 详情 => %s.", response.status, response.responseText);
  },
```
> 提示：当然这里可以自由发挥，根据自己的情况重写，实现自己的特定需求。

### 4.3.6 更多场景

更多特色场景需求，比如：<br />

- 全局ajax自定义头信息
- 接口请求参数加密
- 返回报文解密(dataFilter)

## 4.4 封装核心代码

_**在这里来看下封装SDK方法postToRemote核心代码：**_
```javascript
 postToRemote : function(url,params,successCallback,errorCallback){
   var self = this,
       args = arguments,
       len = args.length,
       defaultSettings = self.defaultSettings;

   var ajaxSetting = Eui.util.clone(defaultSettings);

   // 如果第一个参数是对象表示多个配置项；
   if($.isObject(url)){
     $.extend(ajaxSetting,url);
   }else{
     $.extend(ajaxSetting,{
       url: url
     });
   }

   // 处理带成功回调情况
   if(len > 1){
     //如果第二个参数是个成功回调函数
     if($.isFunction(params)){
       if(!ajaxSetting.hasOwnProperty('success')){
         ajaxSetting["success"] = params;
       }
       if(successCallback && !ajaxSetting.hasOwnProperty('error')){
         ajaxSetting["error"] = successCallback;
       }
     }else{

       if(successCallback && !ajaxSetting.hasOwnProperty('success')){
         ajaxSetting["success"] = successCallback;
       }
       if(errorCallback && !ajaxSetting.hasOwnProperty('error')){
         ajaxSetting["error"] = errorCallback;
       }

       // 避免需要自定义不包括参数的情况
       var lastParam = args[len - 1];
       var isWarp = $.isBoolean(lastParam) ? lastParam : Eui.Extra.warp;
       $.extend(ajaxSetting,{
         data : Eui.Extra.warpReqParams(params, isWarp)
       });
     }
   }

   if(!ajaxSetting.hasOwnProperty("timeout")){
     $.extend(ajaxSetting,{
       timeout : self.ajaxTimeout
     });
   }

   var ajaxEvents = {};

   self.events.forEach(function (eventName) {
     var method = ajaxSetting[eventName] || $.emptyFn;
     ajaxEvents[eventName] = function () {
       var rs = method.apply(this, arguments);
       if(rs !== false){
         var defFnName = "recv" + Eui.String.capitalize(eventName) + "Dispatch",
             fn = self[defFnName] || $.emptyFn;

         fn.apply(this, arguments);
       }
     };
   });

   ajaxSetting = self.convertDataParams($.extend(ajaxSetting, ajaxEvents));

   self.executeAjax(ajaxSetting);
 },
```
> 备注：该方法内部调用了作用域内的其他封装方法，这里不做详细介绍。

# 五、合理使用API

合理科学的调用方法，能避免多余累赘的代码，让代码更加的简洁而且有效的全局可控，方便做优化。<br />

## 5.1 postToRemote

我们先来看看封装的postToRemote方法支持的使用方式：
```javascript
postToRemote(ajaxSetting)
postToRemote(ajaxSetting,callbackSuccess,errorCallback)
postToRemote(ajaxSetting,params,callbackSuccess,errorCallback)
postToRemote(url)
postToRemote(url,callbackSuccess)
postToRemote(url,callbackSuccess,errorCallback)
postToRemote(url,params,callbackSuccess)
postToRemote(url,params,callbackSuccess,errorCallback)
```
_**参数介绍：**_<br />

- url: 请求地址
- ajaxSetting: ajax所支持的所有配置项
- params: 参数
- callbackSuccess：ajax成功success回调函数
- errorCallback：ajax异常error回调函数



> 备注：可根据自己的场景需要灵活调用不同格式的方法传参。

<a name="mpb2m"></a>
### 5.2 Eui.fetch

<br />该方法是针对postToRemote的promise二次封装，具有以下特性：<br />

- 支持promise用法
- 支持同步yield用法
- 支持全局实现beforeResolveSuccess(即resolve ajax success回调之前调用)
- 支持全局实现beforeRejectError(即reject ajax error回调之前调用)

> 备注：支持postToRemote方法除callbackSuccess、errorCallback参数外所有参数，传参顺序一致，这里不再对参数作详细说明。

_**实现源码：**_

```javascript
/**
* 通信promise封装，参数与postToRemote类似。
* @param  {...any} args
*/
fetch(...args) {
    let len = args.length,
        lastArg = args[len - 1],
        ajaxArgs = [...args];

    if (typeof lastArg === 'boolean') {
        ajaxArgs = args.slice(0, -1);
    }

    return new Promise((resolve, reject) => {
        ajaxArgs.push((res) => {
            return resolve(Eui.Comm.beforeResolveSuccess(res));
        }, (xhr, textStatus) => {
            return reject(Eui.Comm.beforeRejectError(textStatus, xhr));
        });

        if (typeof lastArg === 'boolean') {
            ajaxArgs.push(lastArg);
        }

        Eui.postToRemote(...ajaxArgs);
    });
},
```

> ⚠️注意：相关事件执行顺序：beforeResolveSuccess -> recvSuccessDispatch -> resolve
> beforeRejectError -> recvErrorDispatch -> reject

### 5.2.1 调用格式

```javascript
Eui.fetch(ajaxSetting)
Eui.fetch(ajaxSetting,params)
Eui.fetch(url)
Eui.fetch(url,params)
```

** ⚠️特别提示：最后一个参数可以传isWarp(默认与Eui.Extra.warp一致)，用来判断是否需要对参数做Eui.Extra.warpReqParams(即包装参数reqJson格式)操作。**<br />**

### 5.2.2 yield 用法

常见使用场景，在modal层effects中，ajax请求同步写法支持。
<br />比如，修改密码功能实现代码：

```js
*modifyPwdSubmit({ payload }, { call, put }) {
    const response = yield call(() =>
        Eui.fetch(
            {
            hostKey: Eui.env.authAppHostKey,
            url: Eui.env.ecasMoreURLs.ModifyPasswordUrl,
            },
            Eui.Extra.addPrefixForObject(payload, 'user'),
        ),
    );

    if (response.success) {
        yield put({
            type: 'modifyPwdState',
            payload: false,
        });
    }
},
```

> 备注：如果在effects中需要调用reducers中的方法，必须使用`yield call()`的方式，来保证同步执行。

>**_如何按顺序调用多个effects中的方法而且后面的方法依赖前一个方法的结果？_**
<br />effects中的方法本质上返回Promise对象，基于此特性，所以我们可以这么定义：

```javascript
export default {
  namespace: 'list',
  state: {
  },
  effects: {
    *fnA({ payload }, { call, put }) {
        const response = yield call(() =>
            Eui.fetch('getVersion.action'),
        );
        // 将获取的版本号返回，作为fnB方法调用的参数
        if (Eui.isSuccess(response)) {
            const { data: { version } } = response;
            return version;
        }
        return '';
    },
    fnB({ payload }) {
        // 拿到方法fnA返回的结果
        Eui.Logger.log('debug', 'The payload value is => %s.', payload);
    },
  },
  reducers: {
  },
};
```

<br />_**调用方法：**_<br />_
```javascript
dispatch({
    type: 'list/fnA',
  }).then(
    version => {
      dispatch({
        type: 'list/fnB',
        payload: version,
      })
    },
  );
```

> 备注：从上面实例代码不难看出，只要将第一个方法结果return，作为fnB方法调用的参数payload。

### 5.2.3 Promise 用法

该方法支持promise调用方式，如下：<br />

```javascript
getVersion() {
    Eui.fetch(
        'getVersion.action',
    ).then(
        response => {
            Eui.Logger.log('debug', 'success event resolve, response => %o.', response);
        },
        status => {
            Eui.Logger.log('debug', 'error event reject, status => %s.', status);
        },
    );
},
```

### 5.2.4 同步await写法

可以结合async和await来实现同步写法，如下：<br />

```javascript
async getVersion() {
    const response = await Eui.fetch({
        url: 'getVersion.action',
        autoTip: false,
    });
    Eui.Logger.log('debug', 'await response => %o.', response);
},
```

> 备注：then包括2个参数，第一个参数是进入ajax success回调后执行的resolve，第二个参数是进入ajax error回调后执行的reject。

### 5.2.5 阻止默认异常提示

框架默认对ajax succss回调recvSuccessDispatch做了以下统一处理，包括：<br />

- 未登录情况下，弹出重登窗口(判断依据：Eui.isStableLogined)，不再做提示
- 返回接口报文包含`message`或者`success`为false且`autoTip`(默认为`true`)不为false

Eui.Comm.recvSuccessDispatch默认实现如下：<br />

```javascript
recvSuccessDispatch(response) {
    const msg = response.message,
        { dataType, autoTip = true } = this,
        isSuccess = Eui.isSuccess(response);

    if (dataType === 'json') {
        Eui.Logger.log("debug", "ReqUrl => {%s}, Method type => {%s}, Params => {%o},Response data =>", this.url, this.type, Eui.Object.fromQueryString(this.data || ""), response);
    }

    //如果是未登录情况，不提示@since 3.1.4
    if (!Eui.isStableLogined(msg)) {
        return;
    }

    if (dataType === "json" && autoTip && (msg || !isSuccess)) {
        const infoType = isSuccess ? "info" : "warning",
            msgKey = msg || "EXECUTE_FAILRE",
            message = Eui.Messager.parseResultMsg(msgKey);
            
        Eui.Comm.showHttpResMsg(infoType, message);
    }
}
```

**_当我们期望个性化提示信息或者不提示信息时，该怎么做呢？_**
<br />从上述的recvSuccessDispatch默认实现，不难看出，我们可以传参数`autoTip`为false来达到效果。
<br />以下以`getVersion.action`接口为例：
<br />假设正常情况下访问报文如下：<br />

```
{
    "data":{
        "messageId":"/getVersion.action"
    },
    "success":false
}
```

默认情况下，会提示：
<br />![ajax_error.png](/images/zebra/7/01.png)

**_场景一：不做提示_**

```javascript
async getVersion() {
    const response = await Eui.fetch({
        url: 'getVersion.action',
        autoTip: false,
    });
    Eui.Logger.log('debug', 'skip auto tip warning, await response => %o.', response);
},
```

> 提示：关键字`autoTip`，第一个参数必须为对象格式，当然这里也可以用`.then`的方式。

**_场景二：在modal中的effects个性化提示_**
<br />![ajax_custom_error.png](/images/zebra/7/02.png)<br />
<br />_**实例代码**_：<br />

```javascript
*getVersion({ payload }, { call, put }) {
    const response = yield call(() =>
        Eui.fetch({
            url: 'getVersion.action',
            autoTip: false,
        }),
    );
    if (!Eui.isSuccess(response)) {
        Eui.Extra.showToast('warning', '获取版本号失败！');
    }
},
```

_**也可以：**_

```javascript
getVersion() {
    Eui.fetch({
        url: 'getVersion.action',
        autoTip: false,
    }).then(
    response => {
        if (!Eui.isSuccess(response)) {
            Eui.Extra.showToast('warning', '获取版本号失败！');
        }
    });
},
```

### 5.2.6 全局对报文数据做二次处理

在一些特殊场景下，如果想做框架默认的处理逻辑做重写，可在提供的扩展接口基础上做二次封装，比如：`beforeResolveSuccess`、`beforeRejectError`，方法均属于[`Eui.Comm`](http://192.168.10.129/docs/eui-3.5/apidoc/index.html#!/api/Eui.Comm)。<br />

默认代码如下：<br />

```javascript
/**
* @new
* @since 3.5.9
* 在ajax resolve成功回调之前执行。
* @param {Object} response
*/
beforeResolveSuccess(response) {
    return response;
},

/**
* @new
* @since 3.5.9
* 在ajax reject异常error回调之前执行。
* @param {String} status
*/
beforeRejectError(status, _xhr) {
    return status;
},
```
