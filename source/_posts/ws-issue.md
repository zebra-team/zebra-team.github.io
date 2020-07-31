---
title: 第二期 - 「WS应用及常见问题排查」 - (2020.3.21)
date: 2020/03/21
categories :
- 分享专栏
tags: ['Zebra', '3.0', 'ws','issue'] 
---

![](/images/zebra/2/banner.png)
> 本文是第一期分享主题 - WS应用及常见问题排查 的文字稿，可以通过查看来巩固知识点。

# 一、前言

**本文是根据第二期分享主题「WS应用及常见问题排查」整理而来的文字稿。**

本文所有分享都以 “应用”和“如何排查” 为切入点，同时也是在支持所有项目过程中，对所有问题一个汇总，集中解答，以思路引导为主，带你理解每个知识点。

# 二、介绍

## 2.1. 我是谁？

SCM，2009年参加工作，直到2014年中旬前主要从事后端JAVA开发，期间善于使用ExtJs，那会还没有明确的前后端之分，处于全栈开发时期。一个偶然的契机，接手了公司的前端框架，出于些特殊原因，要重新写一套适合公司的框架，EraJs应运而生，这目前还是公司占80%以上项目PC WEB项目的首选框架。这一从事前端框架开发，2014 ~ 至今，6年过去了，期间整过Jquery、Bootstrap、Zepto、Jquery UI、第三方UI插件等等源码，也产出了Eui1.0、T2.0、T3.0、T3.5(T系列框架主要使用于PC客户端)。构建从Grunt到后来的Gulp，再到现在的Webpack，现在主攻React方向中后端最佳解决方案，可视化开发、工程化建设、基建完善。

## 2.2. 我们的团队

我所带领的前端框架组，目前主要3个人，主要服务于公司PC WEB、PC客户端项目框架支撑工作，逐渐完善框架体系，更好的提效，做好技术支撑工作。

## 2.3. 怎么联系我？

下面是我的微信二维码，对前端基建充满兴趣的可以加我交流，我们也会定期的分享更多框架相关的主题。<br />![WechatIMG1.jpeg](/images/wechat.jpeg#align=left&display=inline&height=296&name=WechatIMG1.jpeg&originHeight=896&originWidth=674&size=61371&status=done&style=none&width=223)<br />

# 三、主题背景

在实际业务开发中，可能会存在以下疑问：

- 出了问题，无从下手排查定位
- 不知道是否支持一些特殊场景，比如：多服务、集群环境等
- 对于性能优化没头绪，或者重构代价大，是否存在一劳永逸的方案
- ...

为了更好的打消以上疑问，结合实际场景，一步步带你熟悉应用，并能快速定位问题。

# 四、WS应用

## 4.1. 背景

webscoket作为项目中一个重要的应用场景，比如：启动、断开重连、动态注册/取消注册title、性能优化等通性。站在框架的角度上，需要针对这些普遍的场景有个标准/统一的方案。

## 4.2. 配置

为了简化开发，支持在init.json中配置'ws'来开启服务，格式："ws://{ip}:{port}/ws/"，定义如下：

```basic
"ws":"ws://192.168.10.129:80/ws/"
```
> 备注：支持多服务，**断开时自动切换服务**，多服务配置以";"拆分。

### 4.2.1 配置https服务

https服务，格式："wss://{ip}:{port}/ws/"，关键字：以**wss**开头，比如：

```basic
"ws":"wss://192.168.10.129:80/ws/"
```

### 4.2.2 配置多服务

为了保障服务的稳定，可能会集群部署，多ws服务同步推送，一台服务器挂掉，还可以使用另一台服务，我们也支持多服务配置的方式，服务之间以";" 拆分，如下：

```basic
"ws":"ws://192.168.10.129:80/ws/;ws://192.168.10.129:81/ws/"
```

### 4.2.3 多ws实例

在一些特殊应用场景下，要支持同时启动多服务，不同业务功能，需要接收不同的服务推送数据，我们可以配置如下：

```basic
"ws":["ws://192.168.10.129:80/ws/","ws://192.168.10.129:81/ws/"]
```
> 从上述配置不难看出，多服务以数组格式配置。

## 4.3. 注册title方式

在实际业务场景中，为了没必要的流量开销，性能的优化，建议动态注册，这样可以最大程度减少ws服务器的压力，也能有效避免没有订阅者title数据的处理。

### 4.3.1 以URL方式注册

对于一些特殊的title，在应用整个生命周期过程中都需要接收的title，我们可以通过在init.json中配置ws的时候默认带上，比如：

```json
"ws":"wss://192.168.10.129:80/ws/A/B"
```
> 上述实例，我们定义了默认A和B title，即服务启动即会接收推送的title数据。多服务，多实例情况下格式类似。

### 4.3.2 通过register方法注册

框架提供了方法Eui.Ws.register动态注册的方式，参数如下：

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- |
| titles | 待注册titles，格式可以是数组或"|"拆分的字符串 | String/Array | '' |
| wsIndex | ws实例索引(多实例下，非0情况下需指定)，可选项 | Number | 0 |

> 在多实例情况下，需要指定注册服务的索引(非0索引服务)。

### 4.3.3 注册并订阅title方法subscribeAndRegister

上述方法register手动注册的方式有个弊端，随着title注册越来越多，如果不及时取消注册，会给服务端/客户端增加的压力越来越大。因为业务开发中，很难判断清楚什么时候该去unregister取消注册。<br />基于此缺陷，框架提供了统一订阅并自动注册的方法。
> 原则：订阅后，如果判断当前title并未订阅过，则自动注册。
> 好处：无需再关心什么时候该去服务注册title。

**参数介绍：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- |
| titles | 待注册titles，格式可以是数组或"|"拆分的字符串 | String/Array | '' |
| callback | 回调函数(**可选项**)，服务推送注册titles时触发回调，支持参数：e、args.<br />e: 自定义事件对象<br />args: 服务推送的数据，包括：data,time,title属性 | Function |  |
| scope | 作用域为当前对外暴露对象，可以直接传xxModal对象 | Object |  |
| wsIndex | ws实例索引(多实例下，非0情况下需指定,**可选项**) | Number | 0 |

> 一个页面作用域内可执行多次，但是只能调用一次带回调函数。

**调用实例代码：**
```javascript
 Eui.Ws.subscribeAndRegister("CNY", (e, args) => {
      let {
        data,
        time,
        title
      } = args;
      ...
  }, xxModal);
```

## 4.4. 取消注册title方式

### 4.4.1 通过unregister方法取消注册

框架提供了方法**_Eui.Ws.unregister_**动态取消注册title，参数如下：

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- |
| titles | 待取消注册titles，格式可以是数组或"|"拆分的字符串 | String/Array | '' |
| wsIndex | ws实例索引(多实例下，非0情况下需指定，可选项） | Number | 0 |

> 什么情况下需要取消注册，判断依据：没有任何消费者(即订阅者)。

### 4.4.2 取消订阅并取消注册unsubscribeAndRegister

在实际业务场景中，很难判断什么时候应该去取消注册title，如果放任不处理，随着注册title越来越多，服务器/客户端压力都会持续增加，甚至会导致客户端崩溃。所以框架提供了方法_**Eui.Ws.**__**unsubscribeAndRegister**_来更好的支撑该场景，开发人员无需再关心什么时候该去取消注册ws的title。因为该方法底层，会根据title当前的订阅者数，动态判断是否需要去调用unregister方法取消注册。
> 判断依据：当取消订阅后，没有其他订阅者，就会自动去取消注册。所以不用担心丢失title问题，同时能最大程度优化接收title数据性能。

参数介绍：

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- |
| titles | 待取消订阅且注册titles，格式可以是数组或"|"拆分的字符串 | String/Array | '' |
| callback | 取消订阅成功后回调用函数 | Function |  |
| scope | 作用域为当前对外暴露对象，可以直接传xxModal或者{scopeId: Eui.scopeId}对象(_必须参数_) | Object |  |
| wsIndex | ws实例索引(多实例下，非0情况下需指定,**可选项**) | Number | 0 |

## 4.5. 订阅title方式

page需要订阅title，才能收到ws推送的数据，同一个title的数据可被多个page订阅。

### 4.5.1 通过subscribe方法订阅title

实例代码：
```javascript
Eui.Ws.subscribe("CNY", (e, args) => {
      let {data,time,title} = args;
      ...
  }, xxModal);
```
> _注意：一个页面作用域内可执行多次，但是只能调用一次带回调函数。_

参数介绍：

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- |
| titles | 待订阅titles，格式可以是数组或"|"拆分的字符串 | String/Array | '' |
| callback | 接收title数据调用函数 | Function |  |
| scope | 作用域为当前对外暴露对象，可以直接传xxModal或者{scopeId: Eui.scopeId}对象(_必须参数_) | Object |  |
| wsIndex | ws实例索引(多实例下，非0情况下需指定,**可选项**) | Number | 0 |

支持调用格式：

```javascript
Eui.Ws.subscribe(titles,callback,scope)
Eui.Ws.subscribe(titles,scope);
```

## 4.6. 取消订阅title方式

一般我们在page切换到隐藏状态时，需要取消订阅，避免收数据，触发逻辑处理，影响性能。

### 4.6.1 通过unsubscribe方法取消订阅title

实例代码：

```javascript
Eui.Ws.unsubscribe("CNY", (e, args) => {
      // 取消订阅成功后执行
  }, xxModal);
```
> _注意：一个页面作用域内可执行多次，但是只能调用一次带回调函数。_

参数介绍：

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- |
| titles | 待取消订阅titles，格式可以是数组或"|"拆分的字符串 | String/Array | '' |
| callback | 取消订阅成功后调用函数 | Function |  |
| scope | 作用域为当前对外暴露对象，可以直接传xxModal或者{scopeId: Eui.scopeId}对象(_必须参数_) | Object |  |
| wsIndex | ws实例索引(多实例下，非0情况下需指定,**可选项**) | Number | 0 |

支持调用格式：

```javascript
Eui.Ws.unsubscribe(titles,callback,scope)
Eui.Ws.unsubscribe(titles,scope);
```

# 五、性能优化

为了解决高频率下刷新ws推送页面有性能瓶颈，提供方法_Eui.Processor.addBufferRule_在上层统一对数据做缓冲。该方法必须在index即框架主页范围执行，所以定义在mapping.json指向的initjs中实现。

实例代码：

```javascript
 Eui.Processor.addBufferRule({
     test: /^USER/, //比如匹配以USER开头的title
     handler: function (response) {
         // 可对数据做二次处理或者判断上次发送时间(response.time)决定是否要发送
         // 手动发送数据可调用 this.sendWsMessage(response);
     }
 });
```
> handler函数中，可根据业务场景来实现缓冲转发实现，比如，通过比较上次发送时间间隔来判断是否需要转发，调用**this.sendWsMessage**方法来转发。可能以前实现时，个别同学直接调用的**_this.observer.send_**方法来转发，**这会导致在多屏弹出窗口时，无法接受消息缺陷**。

addBufferRule 支持传**单个对象或者数组对象**，每个对象包含:

- `test`:  支持正则表达式或者function类型(function类型返回true则会执行handler函数，否则忽略)
- `handler`:  当test执行返回true时，执行解析函数(response对象包含: time,title,data属性)

# 六、注意事项

在实际开发过程中，容易踩坑，在此特别说明下注意事项:

- `作用域`: 订阅与取消订阅需要指定作用域，可以传当前页面对应js对外export var 的xxModal对象或者
```javascript
{
	scopeId: Eui.scopeId
}
```

- `订阅回调函数`: 页面作用域内方法[subscribe](http://192.168.10.129/docs/eui-3.5/apidoc/index.html#!/api/Eui.Ws-method-subscribe)或[subscribeAndRegister](http://192.168.10.129/docs/eui-3.5/apidoc/index.html#!/api/Eui.Ws-method-subscribeAndRegister)只允许调用一次带回调函数， 因为在页面范围内所有订阅title接受的数据都会进入回调，若调用多次，会出现重复接受数据问题<br />

# 七、常见问题

## 7.1. 如何排查服务是否启动正常?

答：我们可以从启动记录的日志来查看详细信息，比如，以electron版本为例：<br />如果启动正常，日志文件会记录如下日志：
```bash
[2020-03-15 17:28:02:088] [webSocket:event:open]: success to connect webSocket => ws://192.168.193.81:81/ws/
```
> 提示：该日志为"info"级别，如果没打印，请查看日志是否开启，可通过控制台上打印：Eui.Logger.level查看或者查看init.json是否配置logLevel，默认日志是关闭的。

如果服务异常，导致不能正常启动，日志文件会记录日志如下：

```bash
[webSocket:event:error]: webSocket appear error!
[2020-03-15 17:35:48:366] [webSocket:event:close]: the webSocket have closed => ws://192.168.193.82:81/ws/,current readyState => 3, code =>  1006
[2020-03-15 17:35:48:367] [webSocket:retry]: WebSocket will retry to connect => ws://192.168.193.82:81/ws/ after 3000 ms.
```
> 连接失败，则会每过3秒会尝试不断重连，直到成功为止。

你也可以从调试模式下的控制上看到"debug"级别的日志：

```bash
WebSocket call event, type =>  ipc-message:ws:start ; event name => open
```

如果你不确定日志文件存储目录，可打印_Eui.env.logDir查看_：
```bash
Eui.env.logDir
/Users/scm/.titanOne/logs"
```
> 默认存储路径：系统用户目录下.titanOne/logs目录。

**<br />**扩散知识点：**<br />ws实例最终会缓存到**_Eui.Processor.wsInstance_**对象中，可控制台打印查看。

## 7.2. 如何注册title及查看成功与否?

答：注册title，可通过register或者subscribeAndRegister方法，推荐后者。具体可以查看上面章节「[注册title方式](#1Zmkd)」。

如何确认注册成功与否？<br />比如通过**_Eui.Ws.register('A')_**来注册title A，我们在日志中看到打印**_：_**
```bash
[2020-03-15 18:02:32:033] [webSocket:title:register]: application function register titles => [ 'A' ]
[2020-03-15 18:02:32:034] [webSocket:send]: webSocket client try to send message => {#A:A} to server.
[2020-03-15 18:02:32:051] [webSocket:event:message:status]: received message => [#B:0] from server.
```
> 如果看到_received message => [#B:0] from server _关键信息，说明注册成功。

你也可以从实例对象中查看：

![](/images/zebra/2/01.png)

## 7.3. 如何确定是否接收到title数据?
答：WS服务推送的数据首选会框架统一接收再转发出去，所以确认步骤如下：

1. 确认框架层面是否收到数据？

```bash
[2020-03-15 18:21:04:376] [webSocket:event:message:package]: the server return a content: title => [A],time => 1584267664376, body length => 3
```

2. 是否转发到指定订阅的page？

在确认之前，前提是该page订阅了指定的title，日志会记录"silly"级别的日志，如下：

```bash
[2020-03-15 18:29:49:795] target widget id => page_tab-48575，channel => ws:message:observe，success to subscribe titles => A.
```

同时，你也可以通过**_Eui.Processor.observer.subscribers_**查看订阅关系：<br />![image.png](/images/zebra/2/02.png)

如何查看目标page的pageId?<br />控制台调试模式下，选中指定页面的作用域，打印**Eui.pageLoadOptions**，查看对象中信息。<br />![image.png](/images/zebra/2/03.png)

如果订阅关系中包含page组件scopeId关系，那么订阅成功，当ws服务有"A" title推送数据时，日志文件会有如下日志(日志级别为'silly')：

```bash
[2020-03-15 18:35:44:907] [webSocket:event:message:response]: the message package not opened => A:123
[2020-03-15 18:35:44:908] [webSocket:event:message:package]: the server return a content: title => [A],time => 1584268544907, body length => 3
[2020-03-15 18:35:44:908] send data to target render div => 'page_tab-48575' successfully，channel => A,sender time => 1584268544907, details =>  { data: 123, time: 1584268544907, title: 'A' 
```

## 7.4. 如何排查通道是否正确注册?

答：通道的注册成功，在日志文件会有相应日志。

这里以pageId "page_tab-48575"查看通道"default"为例：

```bash
[2020-03-15 18:49:45:107] target widget id => page_tab-48575，channel => 40282870-00C5-4584-8479-412F21C69948，success to subscribe titles => default.
```

也可以通过查看实例对象关系查看，比如：

```bash
Eui.Processor.observer.subscribers
```
![image.png](/images/zebra/2/04.png)<br />查看通道名中的关系，如果存在，则是通道正常。

## 7.5. 如何避免重复订阅导致重复收到同一条数据?

答：只要把握一个注意项，一个page作用域内，不调用多次带回调的subscribe，比如：

```javascript
Eui.Ws.subscribe("CNY", (e, args) =>{
      let {data,time,title} = args;
      ...
  }, xxModal);
```
> 如果调用多次该方法，那么会出现重复相应的次数。根本原因是：多次绑定了事件（说到这倒是也可以底层默认处理 掉这情况😄😄😄，留点坑，让人踩踩👎）。
