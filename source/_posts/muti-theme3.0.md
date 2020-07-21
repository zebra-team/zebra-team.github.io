---
title: 多主题集成方案及规范
categories :
- 解决方案
tags: ['titanOne3.0', 'theme'] 
---

设计规范和技术上支持灵活的样式定制，以满足业务和品牌上多样化的视觉需求。

## 一. 实现原理

- 基于自定义less函数`.generator-themes`循环生成多主题命名空间样式；
- 基于init.json配置theme加载默认主题(body标签添加class，格式: `eui-theme-${Eui.env.theme}`);
- 切换主题时，切换主应用body的主题class，并将消息广播到所有iframe模块，无感切换主题，并发送消息到主进程，写文件theme.info记录选择主题(下次登录，默认加载)。

## 二、快速集成

### 2.1 添加依赖

框架默认提供主题包[@erayt/eui3.0-customize-themes](http://192.168.10.129:90/frontend-themes/eui-customize-theme)(包含：'default' and 'dark')主题，集成时，需在package.json中的devDependencies添加依赖:

```basic
"@erayt/eui3.0-customize-themes": "^1.0.0"
```

_删除旧主题依赖：_

```basic
"@erayt/eui2-themes": "^3.2.25",
"@erayt/titanOne3.0-theme-index": "^3.0.0",
"@erayt/titanOne3.0-theme-login": "^4.0.0"
```

### 2.2 环境变量定义

需要在.titanOne配置文件中定义：

```basic
"EUI3_CUSTOMIZE_THEMES": "eui3.0-customize-themes"
```
> 备注：值为主题包name去除"@erayt"固定前缀。

### 2.3 主题配置

init.json中增加配置：

```json
"themes": [{
    "key": "default",
    "name":"默认主题"
},{
    "key": "dark",
    "name":"黑色主题"
}],
```

主题配置说明：

- key：对应多主题定制样式的key，@theme中定义的主题变量；
- name： 主题的名称(也可以直接配置国际化标识)。

### 3.4 功能入口

按照以上步骤配置完，启动程序在用户图标下即可看到切换主题入口，选择主题来切换。
![image.png](/images/tod/theme/switch.png)
> 前提：用户必须登录才可见。

## 三、业务主题开发

### 3.1 指导原则

- 主题变量尽量统一管理；
- 模块样式统一@import目录public/theme/variables.less文件;
- 模块样式涉及需区分主题，必须统一定义在.add-prefix函数中;
- 调用生成多主题样式函数.generator-themes(1)。

### 3.2 业务模块开发

首先，来了解下公共主题变量如何定义？

> 备注：在项目app/public目录下创建theme目录。

```
├── titanOne-demo-pc
├── ├── app
├── ├── ├── public
├── ├── ├── ├──  theme
├── ├── ├── ├──  ├── dark.less
├── ├── ├── ├──  ├── default.less
├── ├── ├── ├──  ├── variables.less
```

variables.less源码：

```less
@themes: default,dark;

.import-vars(@theme) when (@theme = default){
    @import './default.less';
}

.import-vars(@theme) when (@theme = dark){
    @import './dark.less';
}

.generator-themes(@index) when (@index =< length(@themes)){
    .import-vars(extract(@themes,@index));
    .add-prefix(extract(@themes,@index));
    .generator-themes((@index + 1));
};
```
> 备注：该文件特定格式，定义主题皮肤 => @themes(多主题以多号分割)，导入变量 => .import-vars，生成主题函数 .generator-themes。

业务模块代码：

```less
@import '../../../../../public/css/theme/variables.less'; //上面定义的公共调用文件路径

.describe{
  padding: 10px;
}
.add-prefix(@theme) {
    .eui-theme-@{theme} {
        .form-label-theme{
            color: @label-color;
        }
    }
}

.generator-themes(1);  //执行代码
```

**_代码风格三步曲：_**

1. 导入主题变量定义文件；
1. 提取主题相关样式到.add-prefix；
1. 执行.generator-themes函数。

### 3.3 知识点详解

#### 3.3.1 拆分方法

比如，业务模块user下``static/css/index.less``原来定义样式的写法：

```less
.form-label-theme{
    color: #DA444A;
}
```

转换成：

```less
.add-prefix(@theme) {
    .eui-theme-@{theme} {
        .form-label-theme{
            color: @label-color;
        }
    }
}
```
> 提示：实现多主题样式的生成，需要将样式写在.add-prefix()方法内。该方法定义.eui-theme-@{theme}为不同的主题class，所有重写样式都必须定义在主题函数内，颜色变量建议在全局public/css/theme/variables.less中。

_标准格式：_

```less
.add-prefix(@theme) {
    .eui-theme-@{theme} {
      //提取出来的各种样式
      ...
    }
}
```

#### 3.3.2 @themes变量定义

变量@themes用来定义多主题，用","分割，定义的主题与init.json中themes下主题定义key保持一致。

例如：

```less
@themes: default, dark;
```
> 备注：定义在public/css/theme/variables.less文件顶部。

#### 

#### 3.3.3 引入多个主题变量文件

使用.import-vars方法来引入指定主题变量文件。

例如：

```less
.import-vars(@theme) when (@theme = default){
    @import './default.less';
}
.import-vars(@theme) when (@theme = dark){
    @import './dark.less';
}
```
> 备注：如果还有其他主题，按照实际情况增加引用。

#### 

#### 3.3.4 .generator-themes方法

.generator-themes方法是生成重写样式的主方法，结构固定，根据定义的themes内主题的数量进行循环，生成不同的主题样式。

```
.generator-themes(@index) when (@index =< length(@themes)){
    .import-vars(extract(@themes,@index));
    .add-prefix(extract(@themes,@index));
    .generator-themes((@index + 1));
};
```
> 备注：调用顺序 .import-vars -> .add-prefix -> .generator-themes。

### 3.4 常见问题排查

1. _**Q：主题样式没有生效？**_

A：首先确认在body层是否存在主题类(比如：`eui-theme-default)`。其次检查加载的less文件是否生成多主题样式，未生成主题样式，检查index.less是否调用`.generator-themes(1)。`

## 四、如何个性化主题

在公司gitlab仓库[frontend-themes](http://192.168.10.129:90/groups/frontend-themes)组下[eui-customize-theme](http://192.168.10.129:90/frontend-themes/eui-customize-theme)工程为框架相关底层样式，包含：start、login、autoupdate、index、eui主题源码，可基于此工程源码来自定义个性化主题。

### 4.1 环境准备

- node：node环境，可通过[http://nodejs.cn](http://nodejs.cn)下载。
- grunt：全局安装`grunt-cli`。

```bash
npm install -g grunt-cli
```

### 4.2 下载源码

您可以使用git命令下载：

```bash
git clone http://192.168.10.129:90/frontend-team/eui-customize-theme.git
```

或者点击右侧的"[Download Zip](http://192.168.10.129:90/frontend-themes/eui-customize-theme/repository/archive.zip?ref=master)"下载按钮下载。

### 4.3 安装依赖

```bash
cd eui-customize-theme && npm install
```

### 4.4 命令介绍

#### 4.4.1 server
待安装完依赖插件后，启动服务并实时编译(修改less立刻编译):

```bash
npm run server
```
> 备注：可访问[http://127.0.0.1:9000/app/zh_CN/views/index/index.html](http://127.0.0.1:9000/app/zh_CN/views/index/index.html)来访问主页，登录页为login/index.html。

#### 4.4.2 dev

监听less文件变更，实时编译，输出到dist目录：

```bash
npm run dev
```

#### 4.4.3 build

编译输出静态资源，输出到dist：

```bash
npm run build
```

#### 4.4.4 prepublish

在入库执行npm publish命令时，会先执行prepublish。

```bash
npm publish
```

### 4.5 目录规范

```
├── dist/                           // 编译输出目录  
├── fonts/                          // 字体文件目录
└── grunt/                         	// 打包配置目录
└── less/                         	// 存放less目录
    ├── eui                      		// 框架主题的less目录
    |—— index                   		// 主页主题less目录
    |—— login                    		// 登录主题less目录
    |—— start                     	// 启动页主题less目录
    |—— theme/                      // 自定义多主题less目录
    |—— |—— default/                // 默认主题变量文件目录
    |—— |—— |—— variables.less      // 存放主题颜色变量的less文件
    |—— override.less              	// 定义多主题变量的文件
├── package.json
└── ...
```
> 备注：如上述文件目录规范，在theme中自定义多主题文件如：dark，下面统一存放**variables.less**文件，文件中包含通过变量设置的主题颜色。

### 4.6 自定义主题

需要新增自定义多主题，可在`less/theme`目录下创建主题文件夹及定义变量文件，比如：theme/dark/variables.less。
同时在**less/override.less**文件中增加主题定义，比如：

```less
@themes:default, dark;

.import-vars(@theme) when (@theme = default){
  @import (multiple) "./theme/default/variables.less";
}

.import-vars(@theme) when (@theme = dark){
  @import (multiple) "./theme/dark/variables.less";
}
```
> 说明：@themes变量定义多主题，比如：default,dark(多主题会同时编译输出，通过主页中切换主题功能切换主题)。增加.import-vars方法定义，判断不同主题加载不同主题定义变量文件。

#### 4.6.1 variables.less文件

本文件存放当前主题使用的颜色变量，变量对应着各个模块样式内的变量，每个主题的变量文件内定义的变量名相同颜色值不同，从而编译出多套主题。

#### 4.6.2 修改主题包名

自定义的主题，入库必须区分包名(即package.json的name值)，格式：`@erayt/eui3.0-``项目名``-themes`
比如：

```
"name": "@erayt/eui3.0-xpads-themes",
```
> 备注：这里以xpads项目为例，实际中根据自己项目情况命名。

#### 4.6.3 开发阶段联调

> 在讲解调试技巧前，希望同学可以先了解下[npm link](https://www.npmjs.cn/cli/link/)，这样会更有助于同学了解下面的技巧内容。

进入项目并执行：

```bash
cd eui-customize-theme && npm link
```

2. 在.titanone文件内定义`EUI3_CUSTOMIZE_THEMES`：

```
"EUI3_CUSTOMIZE_THEMES": "eui3.0-xpads-themes"
```
> 提醒：值与主题包名(name去除"@erayt/"前缀)一致，个性化主题必须修改主题源码中package.json中的name值(格式:@erayt/eui3.0-项目名-themes)，切记，切记，切记！！！

3. 进入业务项目根目录使用`npm link`命令来关联eui-customize-theme主题工程：

```bash
npm link @erayt/eui3.0-xpads-themes
```

至此项目启动使用的主题包就为下载定义的eui-customize-theme，如果在eui-customize-theme内使用`npm run dev`命令启动实时编译，则项目使用的主题样式也会实时改变只需刷新即可。

## 五、入库自定义版本

自定义主题需项目组自行入库，入库类型必须：Node.js，工程下包含tmp_Node.js.lst 和 tmp_update_Node.js.lst。

**注意点：**

- tmp_Node.js.lst: 文件内容为空
- tmp_update_Node.js.lst: 文件内容如下

```
erayt_release_cnpm_public:eui-customize-themes
```
> 备注：主题工程目录为"eui-customize-themes"(这里以放在根目录下为例)，如果多级目录用"\"分割。

⚠️：如果发起入库未改动主题工程，需修改为：
```
erayt_release_cnpm_public:
```
> 备注：该.lst文件是用来定义需要入库模块目录，多模块以","分割。

**特别提示**：入库必须修改package.json中的name名称，区分不同项目自定义主题。
```json
"name": "@erayt/eui3.0-customize-themes"
```
比如：
```json
"name": "@erayt/eui3.0-xpads-themes"
```
> 提醒：必须以"@erayt/"开始，可自行维护版本号，原则递增版本号。

## 六、项目使用

### 6.1 开发阶段

该主题针对titanOne3.0框架版本，我们可以集成到项目中。
这里以引用自定义主题包`@erayt/eui-xpads-themes`为例，需要在.titanone中定义：

```json
"EUI3_CUSTOMIZE_THEMES": "eui-xpads-themes"
```
> 备注：模块包名以`@erayt/主题包名`的格式定义。

并在package.json中的devDependencies增加依赖：

```json
"@erayt/eui-xpads-themes": "^1.0.0"
```
> 备注：假如版本号是从1.0.0开始叠加的，实际中，根据自己的版本号定义而定。

### 6.2 打包阶段

打包阶段，theme.asar由"tool/theme/electron"工程生成，集成步骤如下：

1. package.json中的dependencies中增加依赖：

```json
"@erayt/eui3.0-customize-themes": "^1.0.0"
```
> 备注：实践中以实际需要引用的包定义为主。

如果由之前版本升级，可以删除以下依赖：

```json
"@erayt/eui2-themes": "^3.2.8",
"@erayt/titanOne3.0-theme-index": "^3.0.0",
"@erayt/titanOne3.0-theme-login": "^3.0.0",
"@erayt/titanOne3.0-theme-start": "^3.0.0",
```

2. 修改Gruntfile.js中的copy脚本，比如：

```json
theme: {
    expand: true,
    cwd:'<%= meta.base %>/eui3.0-customize-themes/dist',
    src: ['**','!**/css/app.css','!**/css/*.map'],
    dest: '<%= meta.dest %>'
},
```
> 提示：'eui3.0-customize-themes'注意替换为实际依赖的主题包name。

如果是之前版本升级，那么可以删除之前copy主题相关任务：

```json
vendor: {
  expand: true,
  cwd:'<%= meta.base %>/eui2-themes/dist/<%= config.theme %>',
  src: '**',
  dest: '<%= meta.dest %>/vendor'
},
index: {
  expand: true,
  cwd:'<%= meta.base %>/titanOne3.0-theme-index/dist/<%= config.theme %>',
  src: '**',
  dest: '<%= meta.dest %>/index/static'
},
login: {
  expand: true,
  cwd:'<%= meta.base %>/titanOne3.0-theme-login/dist/<%= config.theme %>',
  src: '**',
  dest: '<%= meta.dest %>/login/static'
},
start:{
  expand: true,
  cwd:'<%= meta.base %>/titanOne3.0-theme-start/dist/<%= config.theme %>',
  src: '**',
  dest: '<%= meta.dest %>/start/static'
},
```

3. 删除多余定义配置，如果根目录下config.json定义了theme，现在可以删除了。

### 6.3 设置默认主题

可在init.json中定义theme来设置默认主题。
> 提示：theme值可以是@themes中的主题值。

### 6.4 FAQ

**Q：如何查看当前默认主题？**

A：如果从主页切换过主题，那么会记录到当前操作系统用户目录下`${Eui.env.projectName}/theme.info`(默认目录.titanOne/theme.info)，如果没切换保存过，那么读取默认的${Eui.env.theme}主题。
