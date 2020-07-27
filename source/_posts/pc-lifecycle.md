---
title: 第六期 - 「PC客户端各生命周期详解」 - (2020.4.18)
date: 2020/04/18
categories :
- 分享专栏
tags: ['Zebra', '3.0', 'pc','lifecycle'] 
---

![](/images/zebra/6/banner.png)

> 本文是第六期分享主题 -「PC客户端各生命周期详解」的文字稿，可以通过查看来巩固知识点。

# 一、前言

**本文是根据2020.04.18 日，第六期分享主题「**PC客户端各生命周期详解**」整理而来的文字稿。**

本文分享以 “过程” 为切入点，对PC桌面应用程序开发主要生命周期关注点展开话题，以思路引导为主，带你全面理解开发应用程序各关注点。

# 二、介绍

## 2.1. 我是谁？

SCM，2009年参加工作，直到2014年中旬前主要从事后端JAVA开发，期间善于使用ExtJs，那会还没有明确的前后端之分，处于全栈开发时期。一个偶然的契机，接手了公司的前端框架，出于些特殊原因，要重新写一套适合公司的框架，EraJs应运而生，这目前还是公司占80%以上项目PC WEB项目的首选框架。这一从事前端框架开发，2014 ~ 至今，6年过去了，期间整过Jquery、Bootstrap、Zepto、Jquery UI、第三方UI插件等等源码，也产出了Eui1.0、T2.0、T3.0、T3.5(T系列框架主要使用于PC客户端)。构建从Grunt到后来的Gulp，再到现在的Webpack，现在主攻React方向中后端最佳解决方案，可视化开发、工程化建设、基建完善。

## 2.2. 我们的团队

我所带领的前端框架组，目前主要3个人，主要服务于公司PC WEB、PC客户端项目框架支撑工作，逐渐完善框架体系，更好的提效，做好技术支撑工作。

## 2.3. 怎么联系我？

下面是我的微信二维码，对前端基建充满兴趣的可以加我交流，我们也会定期的分享更多框架相关的主题。<br />![WechatIMG1.jpeg](/images/wechat.jpeg#align=left&display=inline&height=296&name=WechatIMG1.jpeg&originHeight=896&originWidth=674&size=61371&status=done&style=none&width=223)<br />

# 三、主题背景

<br />在开发中，可能会存在以下疑问：<br />

- 对客户端开发各阶段没有一个直观的了解
- 只知道如何开发业务功能，对工程体系一无所知
- 效率不高，出问题坐等支持，自己不知所措
-  ...

<br /> 针对以上困惑，接下来从工程体系各阶段做全面的解读，带你全面了解工程化各生命周期。 

# 四、初始化工程

<br />在介绍工程脚手架之前，我们先来了解下yeoman。

## 4.1 什么是Yeoman?

<br />Yeoman帮助我们创建项目，提供更好的工具来使我们的项目更多样化，Yeoman其实是三个工具的集合：yo、grunt、bower。这里我们主要使用的yo(脚手架)工具。<br />Yeoman提供generator系统，一个generator是一个插件，在我们在一个完整的项目上使用`yo`命令时，会运行该generator。<br />

## 4.2 安装yo以及脚手架

命令行执行：

```bash
npm install yo -g
```

针对titanOne3.0框架，我们开发了自定义的generator，可以更便捷的创建项目并开发，脚手架名为: _generator-titanone_。

命令行执行：

```bash
npm install generator-titanone -g
```

## 4.3 创建工程

<br />_generator-titanone_脚手架安装完成之后，在任意目录创建文件夹并进入：

```bash
mkdir titanOne-demo && cd titanOne-demo
```

<br />执行命令：

```bash
yo titanone
```

根据提示依次操作：

1. 输入`工程目录名`（默认为titanOne-quickstart)
1. 选择运行环境（electron/web，使用光标上下键切换）
1. 输入`项目应用名`（默认为quickstart）

<br />完成输入/选择之后，依次按回车键确认，待命令执行完毕，项目创建成功。<br />

## 4.4 创建业务模块

<br />首次创建业务模块时，需要在应用根目录（例如：`titanOne-demo-pc`）的目录下打开命令行并执行脚手架命令，之后则对命令行的打开目录则无强制要求。

```bash
yo titanone:module
```
依次提示：

1. 选择当前语言环境
1. 输入作者姓名
1. 输入模块路径
1. 输入模块名称（默认为index）

<br />完成输入/选择之后按回车键确认，命令执行完毕，在`titanOne-demo-pc/app/views`目录下创建业务模块路径成功。查看模块下文件，包含标准的entry_index.page、entry_index.js、以及国际化目录i18n等相关文件。<br />

## 4.5 创建多应用

<br />脚手架支持在工程目录下创建多个应用目录，需要在工程根目录下打开命令行并执行脚手架命令：

```bash
yo titanone:ex
```

执行命令之后，依次提示：<br />

1. 输入应用名称
1. 选择运行环境（electron/web）

<br />完成输入/选择之后按回车键确认，命令执行完毕，应用目录创建成功。<br />

# 五、工程规范

## 5.1 标准工程目录及配置

<br />基于脚手架创建的标准工程目录结构是这样的：

```
titanOne-demo
├── bin
├── ├── electron.bat
├── ├── electron.sh
├── config
├── ├── electron
├── ├── ├── init.json
├── ├── ├── version.json
├── README.md
├── titanOne-demo-pc
├── ├── app
├── ├── ├── public
├── ├── ├── ├──  js
├── ├── ├── ├──  ├── common.js
├── ├── main.js
├── ├── mapping.json
├── ├── node_modules
├── ├── npm-scripts
├── ├── ├── before-build.script.js
├── ├── ├── generate.entry.file.js
├── ├── package.json
├── ├── README.md
├── ├── tests
├── ├── ├── data
├── ├── ├── ├── desktop.json
├── ├── ├── ├── menu.json
├── ├── ├── init.json
├── ├── ├── mapping.json
├── ├── webpack.config.js
├── tool
├── ├── app
├── ├── ├── electron
├── ├── ├── ├── app
├── ├── ├── ├── ├── main.js
├── ├── ├── ├── ├── package.json
├── ├── ├── ├── Gruntfile.js
├── ├── ├── ├── package.json
├── ├── ├── utils
├── ├── ├── ├── index.js
├── ├── electron
├── ├── ├── package.json
├── ├── theme
├── ├── ├── asserts
├── ├── ├── electron
├── ├── ├── ├── Gruntfile.js
├── ├── ├── ├── package.json
├── ├── utils
├── ├── ├── config.js
```

### 5.1.1 主要目录介绍

- bin:  快速编译构建脚本目录
- config: 发布版本配置文件目录
- dist: 编译输出asar临时目录
- setup: 打包脚本.iss及输出Output目录
- tool: 打包编译框架app.asar,theme.asar包工程等
- tool/app/asserts: 公共共享资源目录，比如：多个项目公共引用的图表包eKLine等，开发阶段会引用到上下文
- tool/theme/asserts: 覆盖框架样式资源文件目录，比如：登录，主页logo图片替换或自定义customCss资源等
- config.json: 打包阶段编译构建选项配置，比如：theme,i18n等
- .titanOne: 项目下的webpack约定配置，主要为了webpack构建灵活性

### 5.1.2 **webpack.config.js**

**<br />框架已经将webpack相关构建封装到`@erayt/titanOne-webpack-dependency`包中，一个标准的简单工程只需要定义引用：

```
module.exports = require('@erayt/titanOne-webpack-dependency');
```

<br />_**默认webpack封装包做了什么？**_<br />
<br />底层包默认封装了对entry_*.js,entry_*.page解析编译及devServer相关配置，打包发布优化编译等。<br />
<br />_**默认resolve定义alias:**_

```javascript
 alias: {
    commonjs: path.resolve(publicDir,'js/common.js'),
    artHelpers: path.resolve(publicDir,'js/artHelpers.js')
}
```

<br />默认对约定public目录下的common.js、artHelper.js做了别名约定，即：<br />
<br />项目js中可以像下面这样引用：

```javascript
require('commonjs');
```

<br />_**默认devtool:**_<br />项目优化原则，打包阶段关闭sourceMap，开发阶段开启：<br />

- 开发阶段："source-map"
- 打包阶段：false

<br />_**如何重写框架约定的webpack构建?**_<br />
<br />框架公共封装包对外暴露webpack配置，所以，我们可以在webpack.config.js重写默认配置，比如：

```javascript
'use strict';

const path = require('path');
const merge = require('webpack-merge');
const libsDir = './app/asserts/libs';
let webpackConfigs = require('@erayt/titanOne-webpack-dependency');

module.exports = merge.smart(webpackConfigs,{
    resolve: {
        alias: {
            echarts: path.resolve(libsDir,'echarts/echarts.min.js')
        }
    },
    devServer: {
        contentBase: [
            path.join(__dirname,"../tool/theme/asserts")
        ]
    }
});
```
> 备注：定义自定义的配置，通过调用merge.smart深度合并到默认配置即可。

<br />如果个别项目开发过程，编译慢，卡顿比较明显，可在此配置:

```javascript
devtool: false
```

### 5.1.3 .titanOne

<br />该配置作为项目中webpack灵活集成的约定配置文件，主要用来定义服务代理，打包优化，主题引用等。<br />


| 配置项 | 类型 | 描述 | 默认值 |
| --- | --- | --- | --- |
| appName | String | 应用名称，请保持与打包后的.asar名称及菜单配置中的applCode保持一致 | 'app' |
| port | Number | webpack服务端口 | 80 |
| servers | Object | 代理服务配置,格式key(服务上下文):value(服务地址)，开发阶段webpack生成代理来源 | {} |
| theme | String | 当前应用主题，可选：'default' or 'blue' | 'default' |
| changeOrigin | Boolean | 是否改变远程域，具体了解webpack的devServer下的proxy的配置说明 | false |
| commons | Boolean | 是否提取app/public/js目录下公共js引用生成commons.min.js，minSize默认30000 | true |
| devtool | Boolean | 生产阶段是否开启sourMap，具体可参考[webpack]()介绍 | false |
| PEO | Boolean | 是否为标准构建工程，为true时，开发阶段会引用tool/app/asserts、tool/theme/asserts目录下的资源赋值给devServer的contentBase | false |
| inline | Boolean | 打包阶段是否内联模式，为true时，js,css都会写入html | false |
| pdfviewer | Boolean | 是否集成pdf预览插件,为true会引用@erayt/titanOne-web-pdfviewer下的dist到contentBase | false |
| equery | Boolean | 是否集成equery插件，为true会引用@erayt/titanOne-equery包下的build指定语言到contentBase | false |

### 5.1.4 config.json

<br />该配置文件主要约定打包阶段编译配置。<br />


| 配置项 | 类型 | 描述 | 默认值 |
| --- | --- | --- | --- |
| theme | String | 引用主题，可选："blue"或"default"(打包theme.asar引用) | "default" |
| i18n | String/Array | 引用国际化版本，可选："zh_CN","en"，需支持多语言时配置成数组格式 | "zh_CN" |
| pdfviewer | Boolean | 是否集成pdf预览插件，为true时会打包到app.asar | false |

## 5.2 项目规范

<br />框架结合webpack开发构建，为了项目组开发人员更好的将重心放在业务上，webpack构建的脚本做了统一约定解析。<br />

### 5.2.1 工程目录结构

```
- ~root/app/
   - public/                      //公共资源路径，其中包括：css、js、tpl；
       		- /css/common.css           //项目公共样式文件（项目中各个模块需要的公共样式文件，可放在该文件中）；
          - /js/common.js             // 项目公共js入口文件（项目中各个模块需要引用的公共代码）；
          - /tpl/*.hbs                //项目公共模板（无需修改）；
   - views/                      //业务代码路径；
          ...             
   - npm-scripts                 // 构建支持脚本
   - tests                       // 配置信息及测试数据
        - data                  // 桌面及菜单测试数据，开发阶段更方便维护菜单
      	- init.json             // 服务及代理信息配置
        - mapping.json          // 开发阶段，initjs及其他支持配置
       ...                           // .babelrc,.gitignore,.npmignore等
   webpack.config.js             // webpack相关定义
   .titanone                     // 应用名，端口，服务，主题及其他定义
```

<br />业务代码必须在`~root/app/views/`目录下，原则上每个业务模块为一个独立的目录，目录下包含入口页面文件(*.page)以及静态资源目录`~root/app/views/模块名称/static/`，
资源文件目录中包含js、css、json、i18n、validation等。<br />

### 5.2.2 模块目录结构

<br />目录结构如下：
```javascript
- module
    - static
        - i18n
        - css
        - js
        - modals
        - validation
    entry_*.page
```


> 注意：每个.page页面对应个同名的js文件，框架会自动引入script。

### 5.2.3 关键文件规范

1. **入口页面entry_*.page**

每个业务功能，都会有一个页面文件，这个文件的命名规则为`"entry_*.page"`，必须以`entry_`作为前缀，`.page`作为文件类型。（调试时，webpack会解析文件，将其生成对应的html文件）。<br />

- _.page_ 支持HTML标准语法及Handlebars语法
- 页面支持国际化，需要国际化的文字以`{ {i18n.*.*}}`替代即可, `*.*`对应国际化文件中的key值或者对象路径
- 页面中需要引用该功能模块内相对资源文件（*.json或者.hbs）,引用定义格式：`{{basePath}}/static/**/*.json`
- 页面中DOM的id命名时，建议以`project_module_*`的方式命名(`项目简称_模块名_id`)，避免不同项目和不同模块间的命名重复(单页面div模式需注意)

2. **入口js文件 entry_*.js**

每个入口页面会对应一个入口js，名称与页面名称一致，格式：`entry_*.js`，调试时，webpack会解析该文件，将其生成对应的js文件，并将引用脚本写入对应的html页面文件中，其写法规范如下：<br />

引用公共js代码：

```javascript
require('commonjs');
```
> 备注：其中commonjs为webpack配置中定义的别名，详情可查看webpack的resolve的alias定义。

引用css(.less or .css)样式文件，一般放在`static/css`目录下：

```
require('../css/*.less')
```

<br />功能需要对外暴露的方法或者属性，需要以**_export var objectName={}_**对象封装，以全局变量在外部（页面中）调用。
objectName即为全局变量，命名时需要避免模块间的重复，命名规范：_**模块路径(驼峰命名规则) + Modal(固定后缀)**_，例如：test模块下entry_index.js内objectName为`testMoal`，test模块下entry_test.js内objectName为`testTestMoal`（非index文件需要加上文件名称）。<br />

- `onRender`：function类型，在页面渲染准备就绪之后，会自动解析该方法，相当于老版本框架的_Eui.onReady_方法
- `onMessage`：Object类型，封装在该对象中的属性，页面加载之后，会在页面中注册各消息通道，供监听事件

<br />实例：比如`views`目录有个_test_模块(test/entry_index.page)，那么`test/static/js`目录下必有_entry_index.js_文件，内容如下：<br />

```
export var testMoal = {
    ...
    
    onRender: function(){
        // 如果调用了Eui.onReady方法，必须定义在该事件内部
    },
    onMesssage: {       // 非必须定义，如果有自定义接收消息通道数据，则需定义，方便框架解析
        '自定义通道名称': function(e,args){
            // args 包含title,time,data三个属性
        }
    }
};
```

> **友情提示**： 非必须对页面暴露的方法及属性不建议定义在_testModal_中，框架提供的默认通道可查看特殊消息通道详解。

### 5.2.4 自定义样式存放目录

<br />在项目中需要引用外部的资源文件或需要根据项目自定义样式。可将资源文件放在根目录下的tool目录下的asserts:

```
~root/tool/                            
    - theme/                           // 重写样式目录
        - asserts/
            - index/                   // 主页重写样式
                - /static/css/
                    - /custom.css
                    - /img
            - login/                    // 登录页替换图片
                - /static/css/img
    - app/                             // 资源文件存放目录 
        - asserts/                   
            - iframe/                  // iframe模块 
            - index/                   // index模块
            - libs/
                - charts/
                    - /eKLine.js       // 例如引入图表js
```


- 引入第三方JS包：存放的目录结构与框架引入模块的结构保持一致，编译时会进行拷贝。页面的调用路径为`../libs/charts/eKLine.js`
- 重写框架样式：在init.json中配置`customCss`自定义css文件名

> 备注：图片的对应路径`node_modules/@erayt/titanOne3.0-theme-index/dist/{{theme}}/css/img`，图片名称对应会进行替换。

<br />_**友情提醒：如何开启引用自定义样式？**_<br />_<br />可在init.json中配置：

```javascript
{
    "customCss": "custom.css"
}
```

<br />特别注意：自定义的样式一定是放在_**index/static/css**_目录下。<br />

> 备注：登录模块支持替换图片，对应路径`node_modules/@erayt/titanOne3.0-theme-login/dist/{{theme}}/css/img`，图片名称对应会进行替换。

## 5.3 配置文件

<br />子应用主要涉及到以下配置文件：<br />

- init.json
- mapping.json
- menu.json
- desktop.json
- .titanOne
> 备注：这里不做详解解释，可访问文档查看。

# 六、主应用启动过程

<br />![系统交互图.jpg](/images/zebra/6/01.png)<br />

# 七、开发阶段

<br />开发阶段你可能会关注以下点：<br />

- 配置文件维护(mapping.json、setting.json、init.json、.titanOne等)
- API文档快速查找
- 集成框架功能
- 个性化存储/定制
- Mock接口使用(推荐使用EasyMock)
- 合理记录日志
- 等等

<br />这里今天不做详细一一解说，相关可以直接查找向导文档熟悉，这里今天主要讲下合理记录日志。<br />
<br />为了方便调试，提供打印输出日志，可以指定不同的日志级别，大于等于全局设置日志级别level将会控制台上输出。<br />
<br />全局日志级别由init.json中配置：

```javascript
"logLevel": "warn"
```
> 提示：默认日志级别false，即不记录任何日志。

<br />输出日志的方法_**Eui.Logger.log**_或者 _**Eui.log**_，两个方法等价。

| 参数 | 类型 | 描述 |
| --- | --- | --- |
| level | String | 日志级别，支持: silly、debug、info、warn、error |
| message | String | 日志内容，支持占位，比如："The type '{0}' is not support!" |
| format | Boolean | 是否需要格式化日志内容，如果有{0}占位符情况设置true |

> 备注：参数传递遵循第一个参数定义日志级别，最后一个参数如果是boolean类型即format，中间可以有多个占位符的参数，比如：{0} - {1} - {2}。

```javascript
Eui.Logger.log("warn","It not support!");
Eui.Logger.log("info","hello {0},welcome.","SCM",true);
Eui.Logger.log("error","The type '{0}' is not support!","panel",true);
Eui.log("debug", "今天是第%s期 -「{1}」分享。", "六","PC客户端各生命周期详解", true);
Eui.log("debug", "今天是第%s期 -「%s」分享。", "六","PC客户端各生命周期详解");
```

<br />当然该日志记录方法也支持[_**console**_](https://developer.mozilla.org/zh-CN/docs/Web/API/Console)_** **_记录日志用法(占位符方式)。<br />

# 八、入库发布版本

## 8.1 维护配置

<br />发布版本涉及到应用相关配置，以及全局版本及服务相关配置文件的维护。<br />

### 8.1.1 子应用mapping.json配置

<br />该文件用来定义预加载页面(始终隐藏状态)、自定义参数设置、嵌入预执行JS相关配置。
```json
{
    "initjs":"ws/static/js/initParam.min.js"
}
```

<br />注意项：

- 打包发布阶段，维护应用根目录下的mapping.json，注意与开发阶段文件的区分
- initjs指向的文件路径相对于views目录，打包阶段文件后缀为`.min.js`, 注意与开发阶段`.bundle.js`区分
- 如果没有需要定义配置，可不需要此文件(加载本地桌面/菜单数据除外，需定义`{}`)。

### 8.1.2 子应用setting.json配置

<br />除系统默认全局参数外，每个子应用都可以定义自定义参数项，来达到业务更灵活的个性化需求。开发与生产阶段存储目录有所不同。<br />

- 开发阶段: `~root/tests/setting.json`，放在tests目录下
- 打包阶段: `setting.json`的存放根目录下，最终打包进xxx.asar包


<br />框架会读取合并所有应用参数配置，最终暴露`Eui.Setting`对象供调用，该对象是CacheMemory类的实例化，存储缓存key均以_**'system:param'**_作为key的前缀，可调用该类的所有方法，比如get,set,remove,batchSet方法。<br />
<br />调用方法示例：

```javascript
// 缓存中存储的实际key为:'system:param:widgetZoom'
var widgetZoom = Eui.Setting.get('widgetZoom');
Eui.log('debug', widgetZoom); // 打印的值为：'true'
```

### 8.1.3 主应用version.json配置

<br />该配置文件用来定义程序最新发布版本的增量版本(appVersion)号及全量版本号(frameVersion)。<br />

> 提示：入库发布阶段，该文件在主工程根目录下的_**config/electron/version.json**_目录下。

<br />比如：

```json
{
  "frameVersion":"1.0.0",
  "appVersion": "1.1.0"
}
```
> 提醒：该配置文件根据入库实际情况维护，遵循版本增量递增原则，如果仅发布增量版本，全量版本号无需更新。

### 8.1.4 主应用init.json配置

<br />该配置文件用来定义服务的相关上下文代理信息，以及一些个性化配置等。<br />

> 提示：入库发布阶段，该文件在主工程根目录下的_**config/electron/init.json**_目录下。

<br />比如：

```json
{
  "proxy": {
    "ecas": {
      "server":"http://192.168.10.129:8080"
    },
    "equery":{
      "server":"192.168.10.138:8080"
    }
  }
}
```

### 8.1.5 FAQ

<br />**Q: 出于敏感信息保护，不希望_app.asar.unpacked_目录下的init.json暴露太多配置？**<br />
<br />A：我们可以修改_**tool/app/electron**_目录下package.json下的

```json
"compile:asar": "asar pack app ../../../dist/electron/app.asar --unpack {init,version}.json",
```

变更为：

```json
"compile:asar": "asar pack app ../../../dist/electron/app.asar --unpack \"version.json\"",
```

这样，**_init.json_**将不会被打包到_**dist/electron/app.asar.unpacked**_目录下。
> 如果部分配置期望对外暴露，我们可以直接修改app.asar.unpack目录下的init.json，可能您不期望不受信任的配置有效，那么可以配置[allowUnpackOptions](http://127.0.0.1:8080/eui3.0/#!/api/Eui.env-property-allowUnpackOptions)(更多说明查看文档)来限制。

## 8.2 版本入库

<br />服务端的编译是通过选择不同的"编译环境类型"来解析对应的描述文件执行构建任务。现就对客户端入库的相关文件作详细的说明，构建类型：<br />

- Node.js打包 : 定义模块需要执行`yarn upgrade`更新包及`npm run build`命令编译
- Setup : 执行setup目录下的.iss文件制作exe
- Node.js : 入库框架模块，最终需要发布npm到152私服

> 备注：常规的PC客户端入库需要选择"Node.js打包"和"Setup"，特殊情况下，比如：需要入库自定义登录模块的，必须选择"Node.js"类型（建议与项目代码不同分支管理）。


<br />**特别提醒：基于titanOne3.5开发的项目入库必须是yarn编译，入库之前必须先找版本管理员指定yarn编译服务器，切记，切记，切记！！！**。<br />

### 8.2.1 Node.js打包

<br />该类型是客户端入库必选编译环境类型，包含tmp_update_Node.js.lst和tmp_Node.js.lst两个文件，格式：`编译类型 + 仓库名:多个文件路径`(以`,`分割)。<br />
<br />实例如下，tmp_update_Node.js.lst 内容模版：<br />

```
yarn_erayt_release_cnpm_public:examples,tool\theme\electron,tool\app\electron,tool\app\electron\app,tool\electron
```

<br />备注：以上内容是以titanOne quickstart的electron版本作介绍。<br />
<br />`yarn_erayt_release_cnpm_public`为固定文本(编译环境指定的npm源仓库别名)，":"后的为工程的目录(需要执行yarn upgrade来更新依赖的工程)。<br />
<br />tmp_build.lst 内容模版：

```
examples,tool\theme\electron,tool\app\electron
```

> 备注：该描述文件用来定义需要执行`npm run build`命令(build定义在package.json中的scripts中)的工程目录，多目录以`,`号区分。

### 8.2.2 Setup

<br />如果需要制作exe安装包，那么需要选择该编译环境类型，约定回去执行`setup`目录下的所有.iss文件(如果该目录包含logo.ico，那么会替换exe的安装桌面快捷图标)。<br />

### 8.2.3 Node.js

<br />如果入库的模块需要发布到公司[npm私服](http://192.168.10.152:7001)，那么需要选择该类型。包含描述文件：tmp_Node.js.lst 和 tmp_update_Node.js.lst，内容模版如下：<br />

```
yarn_erayt_release_cnpm_public:
```

<br />备注：以上内容为固定文本，`:`后定义执行命令的目录，多个以`,`号区分。规则同tmp_update_Node.js.lst文件定义。<br />
<br />文件功能说明：<br />

- tmp_Node.js.lst：执行npm publish命令的工程目录定义
- tmp_update_Node.js.lst：执行`yarn upgrade`命令再执行`npm publish`命令的命令。

### 8.2.4 FAQ

<br />_**Q: 如何入库发布自定义模块到私服？**_<br />

- 申请入库分支，编译环境类型必须为"Node.js"
- 通知版本管理员(马XX)，指定编译服务器
- 初始化入库工程，模块以及描述文件tmp_Node.js.lst 和 tmp_update_Node.js.lst，具体注意项可参考上述。

# 九、预投产演练阶段

<br />投产演练阶段，还有个重要的环节，维护自动更新服务报文数据。<br />

## 9.1 自动更新

<br />自动更新服务接口作为PC客户端检测判断一个重要数据来源，版本信息中主要包含：平台信息、版本信息、加密MD5值、资源下载地址、变更日志等信息。
> 提醒：优先检测全量版本号。

### 9.1.1 版本配置

<br />在version.json中我们可以配置版本信息。<br />
<br />配置项介绍如下：

| 配置项 | 类型 | 描述 |
| --- | --- | --- |
| frameVersion | String | 全量包版本号 |
| appVersion | String | 增量包版本号 |

### 9.1.2 开启自动更新检测

<br />通过在`init.json`中配置_**checkUpdateUrl**_来开启自动更新检测：<br />

```json
"checkUpdateUrl": "http://127.0.0.1/update.json"
```

> 备注：更新服务可以指向一个可访问的json请求地址，也可以是一个.action请求接口。

### 9.1.3 建议文件结构

```
version                   // 自动更新文件夹
			- appVersion            //用来存放增量包的文件夹
								- ResourceSetup.exe //增量包exe文件
			- frameVersion          	 //用来存放全量包的文件夹
								- Setup.exe      //全量包exe文件
changelog.txt         //自动更新日志
update.json           //自动更新配置文件
```

### 9.1.4 自动更新服务报文内容

<br />update.json内容如下：

```json
{
  "windows": {
      "appVersion": "0.0.1",
      "frameVersion": "0.0.1",
      "appUpdateUrl": "http://****/**/ResourceSetup.exe",
      "frameUpdateUrl": "http://****/**/Setup.exe",
      "appSign":"e3681da227e879fecc3e0b071a8787b0",
      "frameSign":"8c89e13fd3bbfed0c485c429d9a617d1",
      "forceUpdate":false,
      "more":{
        "updateTime":"2018-2-6",
        "changelog":"http://****/**/changelog.txt"
      }
  }
}
```

<br />配置项说明：

| 配置项 | 参数类型 | 描述 |
| --- | --- | --- |
| appVersion | String | 增量包所对应的版本号，需要与项目中version.json中的增量版本号对应 |
| frameVersion | String | 全量包所对应的版本号，需要与项目中version.json中的全量版本号对应 |
| appUpdateUrl | String | 增量包下载地址 |
| frameUpdateUrl | String | 全量包下载地址 |
| appSign | String | 增量包文件MD5加密码 由MOCK工具生成 |
| frameSign | String | 全量包文件MD5加密码 由MOCK工具生成 |
| forceUpdate | Boolean | 是否强制更新 |
| more | Object | 自动更新的提示内容；包含`updateTime`(更新时间)和`changelog`(更新提示的内容)属性 |

## 9.2 开启完整性检测

<br />为了保障客户端运行环境的安全，对关键electron文件(.dll等)及项目资源包(.asar)做md5校验，保障运行环境中客户端是完整未被篡改的。<br />为了方便不同使用人员快速对客户端文件生成加密串，提供了包括：命令式 和 工具exe。<br />

### 9.2.1 客户端集成方法

<br />可根据服务端支持情况，通过配置开启防篡改功能，可在init.json配置中增加:<br />

```
"enableTamperValidate": true
```

<br />相关配置说明：<br />

- tamperResistantUrl: 默认接口地址'tamperProofing.action'
- enableTamperValidate: 是否启用防篡改验证功能，默认false

<br />检测过程：

1. 请求接口
1. 解密报文
1. 判断istamper是否为"1"
1. 通过filelist生成sign值与接口数据sign是否一致，如果false，立即退出，返回检测结果"不通过"

> 备注：当检测不通过时，过8秒自动关闭客户端。

### 9.2.2 前提

<br />服务端支持防篡权请求接口`tamperProofing.action`通过init.json中`tamperResistantUrl`来修改请求服务地址。<br />

1. _**请求接口参数格式：**_

```
  let  requestJson = {
        platform: "4",
        versions: [
            { version:”0.1.0”,type: “1”},
            { version:”0.1.0”,type: “2”}
        ]
    }
```

> 备注：请求参数使用RSA加密(与握手秘钥相同)。


<br />参数描述：

| 参数 | 说明 | 类型 |
| --- | --- | --- |
| platform | 平台类型，1:'IOS',2:'Android',3:'PC',4:'electron' | String |
| type | 资源类型， 1: 框架资源(根目录下dll,exe等，排除resources,locales之外), 2: resources 资源 | String |
| version | 版本号，对应version.json中appVersion(对应type值为"2"),frameVersion(对应type值为"1") | Array |

2. _**返回数据格式：**_
```
{
    "data":{
        "result": {
            "istamper": "1",
            "resData": [
                {"sign":"","filelist":"","type":"1"},
                {"sign":"","filelist":"","type":"2"}
            ]
        }
    },
    "success": true
}
```

> 备注：返回报文是使用RSA加密，客户端做解密，istamper字段标识是否客户端需要做防篡改校验。

<br />字段说明：

- sign: 生成的hash加密串
- filelist: 加密文件列表，以","分割

### 9.2.3 基于命令生成加密hash

<br />前提电脑已经安装[node.js](http://nodejs.cn/download/)并指定公司npm源。<br />
<br />指定引用公司源：
```
npm config set registry http://192.168.10.152:7001
```

<br />全局安装包：
```
npm i @erayt/hfile -g
```

<br />用法：

```bash
$ hfile --help

  Usage: hfile [options] [command]

  Manipulate hfile archive files

  Options:
    -V, --version                    output the version number
    -h, --help                       output usage information

  Commands:
    hash|h [options] <dir> <output>  create hfile archive
```

<br />排除多个文件:<br />
<br />Given:
```
    app
(a) ├── x1
(b) ├── x2
(c) ├── y3
(d) │   ├── x1
(e) │   └── z1
(f) │       └── x2
(g) └── z4
(h)     └── w1
```

<br />Exclude: a, b
```bash
$ hfile h app sign.txt --unpack-dir "{x1,x2}"
```

<br />Exclude: a, b, d, f
```bash
$ hfile h app sign.txt --unpack-dir "**/{x1,x2}"
```

<br />Exclude: a, b, d, f, h
```bash
$ hfile h app sign.txt --unpack-dir "{**/x1,**/x2,z4/w1}"
```

<br />对客户端资源文件生成hash:
```bash
$ cd root
hfile h ./resources ../resources.txt
```

> 备注：root为客户端根目录。

<br />对electron底层相关dll等生成hash，忽略resources,locales目录：
```bash
hfile h . ../frame.txt --unpack-dir="{resources,locales}" --unpack=unins000.*
```

> 备注：unins000.dat,unins000.exe 为安装程序时生成，属于动态文件所以要忽略。

### 9.2.4 基于加密工具客户端生成

<br />为了不同人员需求，提供客户端版本供使用，操作界面如下：<br />![tool.png](/images/zebra/6/02.png)
> 备注：可点击"copy"将值复制到剪贴板。

# 十、异常信息收集

<br />投产的项目从表面现象根据客户描述我们很难定位排查到具体问题。所以异常信息的收集并记录显得尤为重要。虽然我们的项目非自运营产品，没有配套的日志监控系统等基础建设来支撑，但是我们可以对_**window.onerror**_做统一监听，并把日志记录到日志文件。<br />
<br />系统主进程相关过程默认会有相关启动日志，根据情况合理设置日志级别：

```json
"logLevel": "warn"
```

_**Q：如何开启error日志监听？**_<br />A：可init.json中配置`"reportJsError": true`来开启全局监听，系统右下角提示，并记录日志文件(方便问题排查定位)。<br />
<br />**看完如果有启发，给辛勤的开源者送颗稻草吧**<br />👇
