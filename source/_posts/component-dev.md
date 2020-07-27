---
title: 第四期 - 「如何二次开发组件及使用」 - (2020.4.4)
date: 2020/04/04
categories :
- 分享专栏
tags: ['Zebra', 'ui component','jquery'] 
---

![PPT首页.png](/images/zebra/4/banner.png)

> 本文是第四期分享主题 - 「如何二次开发组件及使用」的文字稿，可以通过查看来巩固知识点。

# 一、前言

**本文是根据2020.04.04 日，第四期分享主题「如何二次开发组件及使用」整理而来的文字稿。**

本文分享以 “如何”和“实践” 为切入点，对常用组件使用正常模式及二次开发后使用作对比来阐述其意义，介绍在公司实践中如何封装作为引导，了解封装组件的方法，带你理解框架的思考及实现过程。

# 二、介绍

## 2.1. 我是谁？

SCM，2009年参加工作，直到2014年中旬前主要从事后端JAVA开发，期间善于使用ExtJs，那会还没有明确的前后端之分，处于全栈开发时期。一个偶然的契机，接手了公司的前端框架，出于些特殊原因，要重新写一套适合公司的框架，EraJs应运而生，这目前还是公司占80%以上项目PC WEB项目的首选框架。这一从事前端框架开发，2014 ~ 至今，6年过去了，期间整过Jquery、Bootstrap、Zepto、Jquery UI、第三方UI插件等等源码，也产出了Eui1.0、T2.0、T3.0、T3.5(T系列框架主要使用于PC客户端)。构建从Grunt到后来的Gulp，再到现在的Webpack，现在主攻React方向中后端最佳解决方案，可视化开发、工程化建设、基建完善。

## 2.2. 我们的团队

我所带领的前端框架组，目前主要3个人，主要服务于公司PC WEB、PC客户端项目框架支撑工作，逐渐完善框架体系，更好的提效，做好技术支撑工作。

## 2.3. 怎么联系我？

下面是我的微信二维码，对前端基建充满兴趣的可以加我交流，我们也会定期的分享更多框架相关的主题。<br />![WechatIMG1.jpeg](/images/wechat.jpeg#align=left&display=inline&height=296&name=WechatIMG1.jpeg&originHeight=896&originWidth=674&size=61371&status=done&style=none&width=223)<br />

# 三、主题背景

在中后台应用中，大多功能都在跟表单(Form)、表格(Datagrid)、窗口(Window)、模态框(Modal)、布局(Layout)打交道，从第三方开源的UI库中选型时，你会发现普遍存在一个现象，不管第三方库开源者背景如何，社区如何活跃，亦不管处于当下的什么阶段：<br />

- F2V： Function函数驱动视图，以jQuery为代表，代表库Bootstrap、Jquery UI、各种Jquery UI插件、        EasyUI、Layui等等；
- D2V：Data数据驱动视图，以MVVM框架React(Antd、Fusion Design)、Angular(Ng-zorro、Angular Material)、Vue(Element、Iview)为代表；
- C2V：Component组件驱动视图，以Ant Design、Fusion Design为代表；
- T2V：Template模板驱动视图，以Ant Design Pro等为代表；
- S2V：Scene场景驱动视图；
- M2V：Model模型驱动视图；
- AI2V建设：AI智能驱动视图，比如以ImageCook为典型代表

<br /> 以上不同阶段，典型的代表UI库，虽然能在基础库上满足业务中需求，但是在实际应用中，你会发现在代码上存在重复定义，甚至在需求变更时导致整个应用中组件使用定义需要改动。总结几个缺点如下：<br />

- 代码量：你会发现整个应用中重复的代码比较多，但是又不能省；
- 可扩展性：需求变更，全局性的污染，需要作出相应改动；
- 统一性：不同第三方组件库整合，在使用、主题等不统一，无法很好定制主题、多语言等场景；
- 未来发展：使用方法过于局限，难以规划未来往场景驱动、模型驱动、智能驱动实践。

<br />要避免上述缺点，我们可以从业务需求场景出发，深度定制二次开发，约定大于配置的原则，以统一模式配置化方式来支持灵活多变的场景，减轻不同技术栈切换带来学习的成本。

# 四、常见组件

# 五、二次开发组件的意义

<br />在谈论二次开发组件的意义前，我们先来看下常规模式下，不同技术栈使用第三方组件的标准用法。<br />

## 5.1 jquery代表jquery ui库

<br />在这里jquery ui库以datepicker组件使用为例：<br />

```html
<p>日期：<input type="text" id="datepicker"></p>
```

<br />初始化方式：<br />

```javascript
$( "#datepicker" ).datepicker();
```

## 5.2 react代表antd库

<br />在这里antd以DatePicker组件使用为例：<br />

```javascript
import { DatePicker } from 'antd';
<DatePicker />
```

## 5.3 vue代表element库

<br />在这里element以DatePicker组件使用为例：<br />

```vue
<el-date-picker
    v-model="value1"
    type="date"
    placeholder="选择日期">
</el-date-picker>
```

<br />通过上述不同组件库的定义方式我们可能想到了什么：<br />

- jquery ui：组件必须需要js来初始化(如果我现在页面上有100以上个组件)，这将会怎么样？
- antd：组件支持全局重写组件的默认显示格式吗？
- vue：组件支持显示格式与序列化表单获取的格式不一样吗？

<br />而这些疑问，恰恰是实际业务中，普遍存在的需求场景。如果UI 组件本身不能很好的支持解决上述问题，那使用者注定不能愉快、轻松的工作。<br />
<br />我们今天的主题正是要解答你的疑虑，并提供一种行之有效的解决方案：二次开发组件想法方法。

# 六、如何二次开发组件

<br />任何的解决方案都是为了更好的解决现实中存在的需求。公司目前主要包括jquery和react技术栈，今天也会以我是如何在公司中约定二次开发的规则及实现的展开话题。<br />
<br />在这里主要以jquery技术栈为主。

## 6.1 约定关键字

<br />为了最大程度减少以js驱动初始化UI组件，约定了`xtype` 标识来区分不同组件类型，以`**custom-options**`扩展属性来达到参数配置化目的。<br />
<br />**_我们来看个例子：_**<br />
<br />定义常规日期组件：
```html
<p>日期：<input type="text" xtype="datepicker"></p>
```

> 备注：以上定义即完成了组件的初始化，如果有其他属性需要定义，可以配置在custom-options中。

<br />定义个两个日期比较范围：
```html
<p>开始时间：<input type="text" 
               id="startDate" 
               xtype="datepicker" 
               custom-options="
  								lessToPicker: '#endDate'
  						 "></p>
<p>结束时间：<input type="text"
               id="endDate" 
               xtype="datepicker"
               custom-options="
  								greaterToPicker: '#startDate'
  						 "
               ></p>
```


> 仔细观察的同学，也许发现了，开始时间增加了配置"lessToPicker"，结束时间增加了配置"greaterToPicker"。我们就是通过增加了扩展配置来达到省去了监听组件的onchange事件去与另个组件的交互代码。

## 6.2 API 设计

<br />要达到上述可配置，我们必须先设计好我们整体的解析规则。接下来我们先来看下设计的API，注册扩展UI组件类型的用法。<br />
<br />框架封装了方法Eui.Extra.registerUiExtType来支持自定义扩展组件类型。<br />
<br />参数介绍：<br />

| 参数 | 说明 | 类型 |
| --- | --- | --- |
| uiType | 组件约定类型，注意唯一 | String |
| parser | 解析实现，包含：options, handler两个属性 | Object/Function |

> 备注：parser参数也支持是function类型，在无需默认配置项时，可直接定义成funciton类型。

<br />_**API调用格式：**_
```javascript
Eui.Extra.registerUiExtType(
    '组件扩展类型',{
    options: {
        // 默认配置项
    },
    handler: function(target,options){
        //具体扩展解析实现
    }
});
```

<br />_**以扩展switch为例：**_<br />

```javascript
Eui.Extra.registerUiExtType(
  	'switch',	{
      options: {
        onText: "ON",
        offText: "OFF"
      },
      handler: function(target,options){
        var $target = $(target),
            opts = options || {},
            onText = opts.onText,
            offText = opts.offText;
        if(!$target.parent("label").hasClass("toggle")){
          $target.wrap('<label class="toggle"></label>')
            .after('<i data-swchon-text="' + onText + ' " data-swchoff-text="' + offText + '"></i>');
        }
      }
  });
```

> 介绍到这里的时候，是不是已经对二次开发组件有一定的了解了。

## 6.3 约定大于配置

<br />“约定大于配置”这个原则，在框架封装过程中，尤其重要，我们虽然可以直接在custom-options中去配置参数，但是如果你的参数是具有普遍代表性的，你决定这样做还合理吗？自然 “say no”。<br />
<br />在封装组件时，我们应该遵循这个原则，将具有代表性的参数可以定义在options中。这里我们以封装第三方日期组件[jedate](http://www.jemui.com/uidoc/jedate-jquery.html)为例来体验它的意义。<br />

```javascript
/**
		 * @singleton
		 * @class Eui.uiExtensions.Jedate
		 *
		 * 基于第三方[Jedate](http://www.jemui.com/uidoc/jedate-jquery.html)组件扩展 xtype='jedate'，jeDate包含日历固定、有效无效日期、日期时间戳转换、日期加减、限制时分秒、初始化日期加减N、
		 * 日期标注点、输入框可以直接输入时间、设定年月（YYYY-MM）、日期范围限制、开始日期设定、自定义日期格式、当天的前后若干天返回、时分秒选择、智能响应、自动纠错、节日识别，操作等常规功能外，
		 * 根据不同的日期格式，显示不同内容。同时扩展升级到了6.0版本增加提交格式转化，配置日期关联等。
		 *
		 */
		Eui.Extra.registerUiExtType(
			"jedate",{
				priority: 2,
				options: {
					/**
					 * @cfg {String} 日期默认显示格式
					 */
					format: 'YYYY-MM-DD',

					transformFormat: true,
					/**
					 * @cfg {String} 日期当前格式，支持有效的Eui.Date所支持的日期格式
					 */
					fromFormat: 'Y-m-d',
					/**
					 * @cfg {String} 提交格式，支持有效的Eui.Date所支持的日期格式
					 */
					toFormat: 'Ymd',
					/**
					 * @cfg {Boolean} 是否显示图标
					 */
					showIcon: true,
					/**
					 * @cfg {Boolean} 选择日期之后是否关闭选择框，为true时，不立即关闭点击确认之后才关闭
					 */
					onClose: true,
					/**
					 * @cfg {String} 日期图标的字体样式
					 */
					iconCls: "glyphicon-calendar",
					/**
					 * @cfg {Boolean} 是否显示'清除'按钮
					 */
					isClear:false,
					/**
					 * @cfg {Boolean} 是否禁用组件
					 */
					disabled: false,
					/**
					 * @cfg {Boolean} 是否只读
					 */
					readOnly: true,
					/**
					 * @cfg {String} 对应的结束日期组件的对象选择器，如'#jeDateID'
					 */
					lessToPicker:'',
					/**
					 * @cfg {String} 对应的开始日期组件的对象选择器
					 */
					greaterToPicker:''
				},
				handler: function (target, options) {
					var $target = $(target),
						opts = $.extend({},options || {});
          
          // 中间省去具体实现逻辑
					...
					$target.jeDate(opts);
				}
			}
		)
```

<br />在扩展jedate组件过程中，我们做了什么：<br />

- 支持组件显示格式、提交到后端格式自定义
- 支持日期范围限制
- 以及重写组件原生默认参数


<br />我们这么做的意义就在于，在使用过程中，避免重复定义，又能支持特定场景下的重写，满足特殊需求。

## 6.4 可延伸扩展

<br />如果细心的同学，可能发现了：虽然将通用的配置提取到options默认约定了，但是如果实际中对于框架所默认约定有异议呢？以目前的设计，看不到如何很好的支持全局重写。“是的，你没错，你的疑虑是对的”。<br />
<br />所以，横向支持扩展，这点也很重要。我们可以怎么做呢？<br />
<br />**_以下以下拉组件select2组件为例：_**<br />**<br />假设我们有以下需求：<br />

- 下拉选项支持多数据源(dataMode)，比如：
  - `dom`: 页面"option"的dom
  - `var`: js变量，组件data属性引用
  - `remote`: 从远程获取，指定组件的url属性，如果后端返回数据非标准格式，可指定扩展属性dataNode
  - `ls`: 从localStorage中获取初始化数据项，此时storeNode必须指定存储节点名称
  - `local`: 从框架[dictionaries](http://127.0.0.1:8080/eui3.0/#!/api/Eui.LocalStore-cfg-dictionaries)全局对象上获取，此时storeNode必须指定存储节点名称
- 增加默认"全部"选项(defaultOption)，且支持文本(defaultText)，值(defaultValue)自定义
- 每个选项对象，支持指定文本(textField)、值(valueField)对应字段
- 支持级联组件(refSmId)
- ... 等等可能的需求

> 当看到这么多需求的时候，可能会觉得不可思议，但是这些确实都是实际场景中提取抽象出来的。

<br />先来看个使用实例：
```html
<select xtype="select2" 
        name="language" 
        style="width:100%"
        custom-options="
          data: [
             {id:'java',text:'Java'},
             {id:'ruby',text:'Ruby'}
          ]
				" />
```

<br />代码实现：
```javascript
var ExtSelect = Eui.uiExtensions.Select2 = {
		extSelect2Options: {
			/**
			 * @cfg {String} 组件数据来源，可以是：
			 *
			 * - `dom`: 页面`select`选项元素
			 * - `remote` : 服务器端请求数据
			 * - `local`: js对象本地存储，绑定到{@link Eui.LocalStore#dictionaries}对象的数据
			 * - `ls`: 从localStorage缓存中读取
			 */
			dataMode: "ls",
			/**
			 * @cfg {String} 显示文本的字段(在`loadFilter`扩展中重新组装数据时使用)
			 */
			textField: "label",
			/**
			 * @cfg {String} 值对应的字段(在`loadFilter`扩展中重新组装数据时使用)
			 */
			valueField: "value",
			/**
			 * @cfg {Boolean} 是否显示搜索过滤选项框，相当于插件原生支持的属性minimumResultsForSearch: Infinity.
			 */
			allowSearch: false,
			/**
			 * @cfg {Boolean} 是否显示"x"清除图标
			 */
			allowClear: false,
			theme: "classic",
			/**
			 * @cfg {String} 默认缺省的提示信息初始值
			 */
			placeholder: "Select a state",
			/**
			 * @cfg {Boolean} 是否加载默认项
			 */
			defaultOption: false,
			/**
			 * @cfg {String} 默认项的文本内容
			 */
			defaultText: "DEFAULT_SELECT_TEXT",
			/**
			 * @cfg {String} 默认项的值
			 */
			defaultValue: "",
			/**
			 * @cfg {String/Boolean} 当dataMode是'ls'时，指定缓存key的前缀，即：`${lsStorePrefix}:${storeNode}`
			 */
			lsStorePrefix: false,
			/**
			 * @cfg {Array} 初始化数据
			 */
			data: []
		},
  
  	_loadFilter: function ( response,opts) {
    	//这里省去转换处理数据过程
      ...
      return response;
    },
  
  	parseExtUiSelect2: function (target,options) {

			var self = this,
				$target = Eui.getJqDom(target);

			return $target.each(function (index, element) {

				var $t = $(element),
					opts = Eui.domData($t,"select2.options") || $.extend(true,{},self.extSelect2Options);
				$.extend(true,opts,options);
				
        // 这里省去具体解析实现
				...
				$t.select2(opts);
			});
		}
};

Eui.Extra.registerUiExtType(
		"select2",{
			priority: 4,
			options: {
				/**
				 * @cfg {String} 级联下拉组件的ID
				 */
				refSmId: '',
				/**
				 * @cfg {String} 级联下拉组件请求的路径
				 */
				refUrl: '',
				/**
				 * @cfg {String} 级联条件参数指定的参数名，默认与valueField定义的值一致
				 */
				refParam: '',
				/**
				 * @cfg {String} 指定远程返回数据下拉项数据节点，dataMode为'remote'时有效（在`processResults`方法中解析该扩展属性）.
				 */
				dataNode: '',  //dataMode为'remote'时有效
				/**
				 * @cfg {String} 存储中对应的key，不为空，将会调用_getStoreData方法获取数据（dataMode为'local'或'ls'时有效）
				 */
				storeNode: "", // 存储中对应的key（dataMode为'local'或'ls'时有效）
				/**
				 * @cfg {Function} [loadFilter] 在渲染选项前，提供接口可以重新组装数据，提供参数：data.
				 */
				loadFilter: function (data) {
					return ExtSelect._loadFilter(data,this.options.options);
				}
			},
			handler: function (target, options) {
				return ExtSelect.parseExtUiSelect2(target,options);
			},
			afterReady: function (target,options) {
				// 这里做些事件绑定
				var opts = Eui.domData(target,"select2.options") || options;
				var events = opts.events;

				if(events){
					$.each(events,function (name, fn) {
						target.on(name,function () {
							Eui.pass(fn,arguments)();
						});
					});
				}
			}
		}
	);
```

<br />在封装select2的时候我们多做了什么？<br />

- 部分方法提取，调用外部方法
- 部分参数提取到外部约定默认值


<br />这么做的目的正是考虑到，框架的约定不能满足于业务需求的多变，留个后路供全局重写默认参数、方法。解析优先级由低到高：组件默认配置 -> options默认配置 -> 全局extSelect2Options配置 -> custom-options配置。

# 七、在公司实践

<br />在公司实践中，我们封装了常用的所有相关组件，包括：button、comboGrid、comboTree、grid、jedate、modal、select2、multipleSelect、timepicker、weekTime、spinner、numberspinner等等，覆盖业务开发过程中基础组件大多场景可配置化。<br />
<br />**这里主要来介绍下按钮场景的实践。**<br />
<br />按钮在项目中比较常见，比如：表单提交、关闭窗口、打开模态框、查询等。对于以扩展方式配置实现的按钮点击事件，框架统一会控制短时间内重复执行。详细场景配置可参考BtnTypeMap配置说明。<br />

## 7.1 按钮扩展类型

<br />按钮扩展类型(btnType)包括：'action','modal','native','query','submit'等，每个实现都包含以下属性：<br />

- `options`: 默认配置项
- `handler` : 处理函数，包括：target,options两个参数


<br />_实例：点击按钮打开新增模态页面_<br />_
```html
<button class="btn btn-danger" data-toggle="modal"
    custom-options="
    title: 'Validate Form',
    href:'base.html'
">新增</button>
```

### 7.1.1 action

<br />与后端交互操作封装，底层调用Eui.uiExtensions.Button.executeAction方法。<br />

```
/**
 * @property {Boolean} [action.mask=true] 是否显示遮罩
 * @property {String} [action.maskEl=""] 遮罩区域
 * @property {String} [action.maskMsg="IN_PROCESSING"] 遮罩的显示信息
 *
 * @property {String} [action.refDgId=""] 关联表格id（不为空表示必须选中行）.
 * @property {Boolean} [action.singleSelect=true] refDgId不为空时，表格的选中模式，单选 or 多选.
 * @property {String} [action.identifyField="id"] 组装表格参数的标识字段，可多个（数组格式）.
 *
 * @property {String} [action.paramPrefix=""]  添加对参数前缀
 * @property {Array} [action.ignorePrefixKeys=[]] paramPrefix不为空时，定义忽略添加前缀对字段(备注：singleSelect为true时有效) @since 2.1.5有效.
 *
 * @property {Object} [action.extraParams={}] 额外参数，将会与动态组装的参数合并（备注：paramPrefix对它无效） @since 2.1.5有效.
 * @property {Boolean} [action.warp=true] 是否需要reqJson,默认与全局{@link Eui.Extra#warp warp}一致
 * @property {Array} [action.ignoreWarpKeys=[]] warp为true时，可定义忽略warp的字段（忽略字段不执行reqJson操作，前提：singleSelect为true时有效）
 *
 * @property {Function} [action.beforeExecute] 提交后端之前执行的事件,return false将阻止继续执行，提供参数(params，options).
 * @property {String} [action.url=""] 远程请求地址
 * @property {Object} [action.ajax] 可定义ajax支持的配置项
 *
 * @property {Function} [action.callback] 后端成功返回数据后的回调，提供参数：(response,options)，return false将不再执行{@link Eui.uiExtensions.Button#defaultActionCallback defaultActionCallback}默认回调.
 * @property {String} [action.autoRefresh=false] 自动刷新表格数据（前提：refDgId不为空）
 */
```

### 7.1.2 export

<br />导出满足关联表单条件的excel数据，按钮定义在form内部。<br />

```
/**
 * 备注：支持{@link Eui.Form.submitForm}相关配置项及BeforePrepareParams事件执行之前的相关事件.
 *
 * @property {String} export.url 请求服务地址
 * @property {String} [export.mask=false]
 * @property {String} [export.fileType='xls']
 * @property {String} [export.filename] 默认当前时间格式化(YmdHis)后的时间字符串
 */
```

### 7.1.3 modal

<br />缺省打开模态框及表单相关初始化，详情可以参考Eui.Extra.openModalWin方法， 更多配置项可查看defaultModalOptions, formOptions(`autoLoad`为true时有效)详解。<br />

```
/**
 * @property {String} [modal.refDgId=""] 关联表格id（不为空表示必须选中行）,如果已经选中行，将动态给param赋值，dataMode为'local'时，将会给data参数赋值（行记录对象）.
 * @property {String} [modal.identifyField=""] 组装表格参数的标识字段，可多个（数组格式）.
 * @property {Boolean} [modal.singleSelect=true] refDgId不为空时，表格的选中模式，单选 or 多选.
 *
 * @property {Function} [modal.beforeRender] 模态框打开之前执行的事件，return false将阻止打开模态框.
 * @property {Object} modal.beforeRender.options 配置项
 *
 * @property {Boolean} [modal.localize=true] 是否需要本地化标题，默认与全局{@link Eui.Locale#localize localize}一致.
 *
 * @property {Object} [modal.extraParams={}] 额外参数，将会与表格组装的参数合并
 * @property {String} [modal.dataMode="local"] 数据加载模式，可选"local" or "remote"
 *
 * - `local`: 数据从行记录中获取
 * - `remote`: 数据发起远程请求获取
 *
 * @property {Boolean} [modal.autoLoad=false] 显示模态框后是否自动赋值，如果为true，{@link Eui.uiExtensions.Modal#formOptions formOptions}相关配置项生效
 */
```

### 7.1.4 native

<br />原生onclick实现（备注：为了统一控制重复提交，建议使用该方式替换onclick的实现)。<br />

```
/**
 * @property {Function} native.handler 点击时触发的方法.
 * @property {Function} native.handler.el 当前按钮的dom元素
 */
```

### 7.1.5 query

<br />关联表格查询实现，自动组装查询表单条件， 底层调用crudSearch方法。<br />

```
/**
 * 备注：跟参数相关的配置，比如：`ignorePrefixKeys`,`warp`等，请查看表格相关扩展配置。
 *
 * @property {String} query.refDgId 关联表格ID(必需)
 * @property {Boolean} [query.validate=false] 查询之前是否需要验证，关联表格定义了扩展属性`queryFormName`.
 *
 * @property {Function} [query.beforeExecute] 执行查询之前触发事件，return false阻止继续执行.
 * @property {Object} [query.beforeExecute.queryParams] 条件参数，参数的处理将会在提交参数前，有表格扩展统一解析.
 */
```

### 7.1.6 submit

<br />"保存按钮"扩展配置项，底层调用Eui.Form.submitForm；options详情可以查看Eui.Form.defaultOptions， 主要涉及到遮罩及提交前验证及执行操作。<br />

```
/**
 * @property {String} [submit.url] 远程服务地址，为空时将会解析表单的"action"属性值.
 * @property {String} [submit.refFormName] 待提交表单的name属性值
 *
 * @property {String} [submit.mask=true] 是否显示提交等待信息.
 * @property {String} [submit.maskEl] 遮罩区域(jq对象或选择器表达式)，默认表单区域.
 * @property {String} [submit.maskMsg] 遮罩的提示信息，为空时默认与{@link Eui.Extra#showMask}方法"maskMsg"参数值一致.
 *
 * @property {Boolean} [submit.validate] 表单是否需要验证，默认与全局{@link Eui#globalValidated}一致.
 * @property {Function} [submit.beforeValidate] 验证之前执行的事件，参数:$form；return false将退出.
 * @property {Eui} [submit.beforeValidate.form] 表单目标jq对象.
 *
 * @property {Function} [submit.beforeExecute] 提交之前执行的事件,async为true时，return false将会退出提交，否则会弹出确认操作提示框.
 * @property {Object} [submit.beforeExecute.submitParams] 提交的参数对象.
 * @property {Object} [submit.beforeExecute.form] 表单jq对象(@since 2.1.5).
 * @property {Object} [submit.beforeExecute.options] 按钮所有扩展配置项对象(@since 2.1.5).
 *
 * @property {Boolean} [submit.async=true] 是否异步提交，为false时，将会弹出确认框.
 * @property {String} [submit.confireMsg='CONFIRM_INFO'] 同步提交时(async为false)，确认框提示信息.
 *
 * @property {String} [submit.paramPrefix] 参数前缀.
 * @property {Array} [submit.ignorePrefixKeys=[]] 忽略添加前缀的参数键(@since 2.1.5有效).
 * @property {Object} [submit.extraParams={}] 额外参数，将会与动态参数合并（备注：paramPrefix对它无效，@since 2.1.5有效).
 * @property {Boolean} [submit.warp=true] 使用使用reqJson包装参数，默认与全局的{@link Eui.Extra#warp warp}一致(@since 2.1.5有效).
 * @property {Array} [action.ignoreWarpKeys=[]] warp为true时，可定义忽略warp的字段（忽略字段不执行reqJson操作)
 *
 * @property {Object} [submit.ajax] 可以配置ajax配置项，比如：{type:'GET'}(@since 2.1.5有效).
 *
 * @property {Function} [submit.callback] 成功回调函数，return false将终止继续执行自动关闭模态框，刷新表格，成功后默认解析的扩展可通过以下方式扩展.
 * @property {Object} [submit.callback.response] 返回数据对象.
 *
 * 		Eui.apply(Eui.Defaults._afterSubmitFormTodos,{

			// 提交表单后自动关闭模态框
			"closeModal": function ($form, options) {
				var openMode = options.openMode,
					autoClose = options.autoClose;

				if(!!autoClose && openMode && openMode === "modal"){
					var modalDialog = $form.closest("div[role='dialog']");
					modalDialog.modal("hide");
				}
			}
		});
 *
 * @property {String} [submit.openMode='modal'] 当前窗口的打开模式，默认是'模态框'(@since 2.1.5有效).
 * @property {Boolean} [submit.autoClose=true] 是否自动关闭，如果非窗口打开请设置false(@since 2.1.5有效).
 * @property {String} [submit.refDgId] 自动刷新表格的ID，需要自动刷表格时可设置(@since 2.1.5有效).
 *
 */
```

## 7.2 自定义按钮扩展类型

<br />开发人员可自定义按钮扩展类型，包含参数：自定义btnType名称、具体触发click事件时执行函数(包含options,handler)。<br />
<br />_**参数介绍：**_

| 参数 | 说明 | 类型 |
| --- | --- | --- |
| btnType | 按钮类型 | String |
| parser | 解析实现，包含：options, handler两个属性 | Object/Function |


<br />_**代码格式：**_
```javascript
 Eui.Extra.registerBtnType(
    'custom', // 自定义按钮类型
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

## 7.3 表格扩展

<br />为了更方便的初始化表格，框架实现了扩展类型xtype='datagrid', 主要对数据读取，列名本地化，关联查询表单扩展，实现配置化； 如下定义一个简单的表格：<br />

```html
<table id="jqGrid" xtype="datagrid"
       custom-options="
            url: 'static/json/griddata.json',
            langNode: 'jqGrid',
            colModel: [
               { label: '机构编号', name: 'bankId', width: 45, key: true },
               { label: '机构名称', name: 'chsName', width: 75 },
               { label: '所属机构', name: 'headName', width: 90 },
               { label: '机构级别', name: 'levelDisplay', width: 100 },
               { label: '机构状态', name: 'enabledDisplay', width: 80 },
               { label: '机构类型', name: 'typeDisplay', width: 80 }
          ],
        	queryFormName: 'logListForm'
   " />
```

> 备注：该配置实现列名本地化（langNode)，关联查询表单(queryFormName)，加载{data: {result: {datals:[]}}}格式的数据。langNode指向Eui.Locale.locales对象存储的数据。

### 7.3.1 后端返回数据非标准格式?

<br />可以通过定义dataNode，配合jsonReader：<br />

- `dataNode`: 定义表格数据上层节点的路径，默认值"data.result"
- `jsonReader`: jgrid插件所支持的配置项，关键配置项root、page、total、records


<br />详细说明可以参考jsonReader详解介绍：

| 参数 | 说明 | 默认值 |
| --- | --- | --- |
| root | 代表实际模型数据的入口 | 'datals' |
| page | 代表当前页码 | 'page' |
| total | 代表页码总数 | 'totalPage' |
| records | 代表数据总条数 | 'total' |


<br />如果返回结果数据分页信息非start、pageSize、total参数，还需重写transformReaderKey扩展参数（方法类型），如下默认实现：<br />

```javascript
transformReaderKey: function (results) {
    var self = this,
        reader = self.jsonReader;
    var pageKey = reader.page,
        totalKey = reader.total;
    var total = results.total,
        start = results.start,
        pageSize = results.pageSize;

    results[totalKey] = Math.ceil(total/pageSize);
    results[pageKey] = Math.ceil(start/pageSize);

    return results;
  }
```

> 该方法返回的results包含的分页信息必须与jsonReader定义的保持一致。

### 7.3.2 分页查询时，分页条件的参数跟约定的不一致时，如何处理?

<br />框架提供了全局范围参数定义，如果是项目级别的重写，可以通过重写全局变量pageStart,pageSize:<br />

- `pageStart`: 页码标识字段名，默认'pageStart'
- `pageSzie`: 分页大小标识字段，默认'pageLimit'


<br />上述配置项也支持组件级别的定义。<br />

### 7.3.3 如果分页，后端需要的非开始页码pageStart，分页大小pageLimit参数？

<br />如果遇到的是该情况，可以重写全局的customPageParam方法，默认实现：<br />

```javascript
customPageParam: function (opts,postData) {
    var self = this;
    Eui.Extra.replaceKeys(postData,{
      "page": $.Object.getKeyValue(opts,"pageStart",self.pageStart),
      "rows": $.Object.getKeyValue(opts,"pageSize",self.pageSize)
    });
  }
```

## 7.4 span支持表单赋值扩展

<br />span标签默认只显示文本值，为了更多样化的显示格式，`custom-options`提供了扩展配置项`formatter。`<br />
<br />具体用法如下：<br />

- 简单用法
- 自定义扩展属性及解析方法
- 使用框架默认提供的解析器

> 备注：关键字：custom-options、initname、formatter。

### 7.4.1 简单用法

<br />指向自定义的方法（方法名任意），场景如下：
```html
<span initname="price"
     custom-options="formatter:spanFormatter"
 ></span>
```

<br />定义JS方法spanFormatter:
```javascript
function spanFormatter(value){
     //value 是 1234,使用工具类将value值格式化
    var result =Eui.util.Format.number(value,'0,000');
    //格式化后的b=1,234
    return result;
  }
```

### 7.4.2 自定义扩展属性及解析方法

<br />为了解析方法复用，我们一般可以采用同一方法解析扩展配置属性动态解析。<br />
<br />_**比如：格式化数字有效位数**_
```html
<span initname="price"
     custom-options="
         format:'0.00',
         formatter:numberFormat
     "></span>
```

<br />定义JS方法numberFormat：<br />
<br />备注：参数arguments包含:val,element,original,options(除formatter以外的所有扩展属性)。<br />

- value: 值
- element: 当前span目标元素
- original: 原始值
- options: 自定义扩展的属性，排除formatter以外所有属性
- values: 完整的赋值对下
```javascript
function numberFormat(val,element,original,options,values){
   var opts = options || {},
       format = opts.format || '0'; //默认只显示整数,没有小数位数.
   return Eui.util.Format.number(val,format);
}
```

### 7.4.3 使用框架默认提供的解析器

<br />框架默认扩展了"currency","datetime",亦可自定义解析器：<br />

- currency: 金额解析
- datetime: 日期格式解析

_**格式化金额数据(currency)：**_<br />
<br />参数介绍：<br />

- formatter:'currency' 针对金额数据封装的方法名，参数值固定
- sign：使用的货币符号,默认值：'$'， 与Eui.util.Format.currencySign一致
- decimals：使用的货币值小数点后的位数,默认值：2， 与Eui.util.Format.currencyPrecision一致
- end：如果为true则货币符号应追加在字符串的结尾,默认值：false， 与Eui.util.Format.currencyAtEnd一致

<br />THML定义示例：
```html
// 230 =>  230.00RMB
   <span initname="appIdName"
         custom-options="
             sign :'RMB',
             end : true,
             formatter:'currency'
   "></span>
```

_**格式化日期(datetime) **_

<br />参数说明：<br />

- fromFormat: 值的格式,默认'Ymd'
- toFormat: 转换后显示格式,默认'Y-m-d'

> 备注：支持Eui.Date支持的所有格式化格式。

<br />THML定义示例：
```html
// 20160301 ==》2016-03-01
<span initname="dateVal"
      custom-options="
         formatter:'datetime'
"></span>
```

### 7.4.4 自定义formatter解析器

<br />框架已经默认提供了'currency'等解析器；项目开发过程中，开发人员自己也可以自己注册自定义方法。<br />

```javascript
Eui.Extra.registerFormatter('解析器名',{
     options : {}, //默认配置项
     handler : function(value,context,payload,options){
     }
 });
```

<br />以自定义扩展一个以特定模版转译html内容为例子，formatter值：'htmlEncode':<br />

```javascript
Eui.Extra.registerFormatter('htmlEncode',{
    options: {
        tpl: ""
    },
    handler: function (value,context,payload,options) {
        var args = arguments,
            len = args.length;
            var opts = args[len - 1],
            tpl = opts.tpl;
        if(tpl){
            value = Eui.String.format(tpl, value);
        }
        return Eui.String.htmlEncode(value);
    }
});
```

<br />_**更多扩展直接查看官方API文档，有疑问也欢迎反馈。**_

# 八、未来规划

<br />将具有通用性的功能场景，抽象约定schema json定义，来动态生成，在基础上提供可视化开发工具辅助。更好的版本迭代，朝着智能化方向探索，发展。<br />
<br />**看完如果有启发，给辛勤的开源者送颗稻草吧**<br />👇

