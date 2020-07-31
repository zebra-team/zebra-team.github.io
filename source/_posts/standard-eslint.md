---
title: 工程化之「集成代码检测规则」 - (2020.7.31)
date: 2020/07/31
categories :
- 工程化
tags: ['Zebra', 'stylelint', 'prettier', 'husky', 'lint-staged', 'eslint'] 
---

指定前端代码规范，是质量保证的重要手段，如何有效的检测规范是否有效执行呢？


主要使用stylelint + prettier + husky + lint-staged + eslint 对我们的规范进行实践，首先来介绍下这几个工具都是干什么的。

- eslint: [ESLint](https://eslint.bootcss.com) 是一个开源的JavaScript验证工具，相比JSLint，ESLint具有可配置性；
- husky: 可以用于实各种 `Git Hook`。这里主要用到 `pre-commit` 这个 hook，在执行 commit 之前，运行一些自定义操作;
- lint-staged: 用于对Git暂存区中的文件执行代码检测。



知道了这几个包的作用后，我们开始来给项目实现一套在提交代码之前的stylelint、eslint检测。
## 一、Stylelint
[Stylelint](http://stylelint.docschina.org)是一个强大的现代CSS检测器,可以让开发者在样式表中遵循一致的约定和避免错误。
### 1.1 安装依赖包
```bash
yarn add stylelint -D
```
### 1.2 规则配置
接在在项目的根目录下创建stylelint相关文件：
```bash
# stylelint 规则
touch .stylelintrc.js
touch .stylelintrc.json
```
_这里我们引用fabric的stylelint规则：_
```javascript
const fabric = require('@umijs/fabric');

module.exports = {
  ...fabric.stylelint,
};
```
_.stylelintrc.json文件：_
```json
{
  "extends": ["stylelint-config-standard", "stylelint-config-prettier"],
  "rules": {
    "declaration-empty-line-before": null,
    "no-descending-specificity": null,
    "selector-pseudo-class-no-unknown": null,
    "selector-pseudo-element-colon-notation": null
  }
}
```
### 1.3 执行脚本lint:style
接下来我们配置package.json里面的执行脚本
```basic
"lint:style": "stylelint --fix \"src/**/*.less\" --syntax less"
```
> 备注：通过--fix自动解决不满足规则的格式。

## 二、Prettier
[Prettier](https://prettier.io) 是一个“有主见”的代码格式化工具，支持列表如下：

- JavaScript，包括 [ES2017](http://link.zhihu.com/?target=https%3A//github.com/tc39/proposals/blob/master/finished-proposals.md)
- [JSX](http://link.zhihu.com/?target=https%3A//facebook.github.io/jsx/)
- [Flow](http://link.zhihu.com/?target=https%3A//flow.org/)
- [TypeScript](http://link.zhihu.com/?target=https%3A//www.typescriptlang.org/)
- CSS、[LESS](http://link.zhihu.com/?target=http%3A//lesscss.org/) 和 [SCSS](http://link.zhihu.com/?target=http%3A//sass-lang.com/)
- [JSON](http://link.zhihu.com/?target=http%3A//json.org/)
- [GraphQL](http://link.zhihu.com/?target=http%3A//graphql.org/)

简而言之，这个工具能够使输出代码保持风格一致。
### 2.1 安装依赖
```bash
yarn add prettier check-prettier -D
```
### 2.2 规则配置
项目根目录下创建相关配置文件：
```bash
# prettier 配置
touch .prettierignore
touch .prettierrc.js
```
忽略定义：
```basic
**/*.svg
package.json
.umi
.umi-production
/dist
.dockerignore
.DS_Store
.eslintignore
.env
*.png
*.toml
*.dll
*.exe
docker
.editorconfig
Dockerfile*
.gitignore
.prettierignore
LICENSE
.eslintcache
*.lock
yarn-error.log
.history
```
_这里我们引用fabric的prettier规则：_
```javascript
const fabric = require('@umijs/fabric');

module.exports = {
  ...fabric.prettier,
};

```
### 2.3 执行脚本
接下来package.json中定义执行脚本：
```bash
"lint:prettier": "check-prettier lint",
"prettier": "prettier -c --write \"**/*\""
```
## 三、ESlint
由于react技术栈基于[umijs](https://umijs.org)，所以我们直接继承@umijs/fabric的规则。
### 3.1 安装依赖
```bash
yarn add eslint @erajs/fabric -D
```
### 3.2 规则配置
进入你的项目中，假设为项目quickstart开始配置。
```bash
# 进入项目根目录
cd quickstart
# 安装eslint,@umijs/fabric依赖
yarn add eslint @erajs/fabric -D
```

- 接着在项目的根目录下创建eslint相关文件
```bash
# eslint要忽略的文件
touch .eslintignore
# eslint 的规则
touch .eslintrc.js
```

- 在.eslintignore文件中填写eslint要过滤的文件
```
dist
test
```

- 配置 .eslintrc.js

```javascript
module.exports = {
  extends: [require.resolve('@umijs/fabric/dist/eslint')],
  globals: {
    Eui: true,
    $: true,
  },
};
```
### 3.3 执行脚本
接下来我们配置package.json里面的执行脚本
```basic
"scripts": {
	"lint:js": "eslint --cache --ext .js,.jsx,.ts,.tsx --format=pretty ./src"
}
```
> 备注：主要是检测js,jsx,ts,tsx 代码，可以通过--fix 把eslint能解决的问题都在检测的时候解决掉，比如格式，变量合并。这里我们通过_prettier插件来修复格式。_



接下来，我们要在git提交时对我们所写的代码进行检测。
## 四、husky + lint-staged
### 4.1 安装依赖
```bash
yarn add husky lint-staged -D
```
### 4.2 钩子配置
接下来要在package.json里面配置一下我们的git钩子：


```json
{
  "scripts": {
      "lint-staged": "lint-staged",
      "lint-staged:js": "eslint --ext .js,.jsx,.ts,.tsx"
    },
  "husky": {
      "hooks": {
        "pre-commit": "npm run lint-staged"
      }
    },
    "lint-staged": {
      "**/*.less": "stylelint --syntax less",
      "**/*.{js,jsx,tsx,ts,less,md,json}": [
        "prettier --write",
        "git add"
      ],
      "**/*.{js,jsx}": "npm run lint-staged:js",
      "**/*.{js,ts,tsx}": "npm run lint-staged:js"
    }
}
```
husky 里面定义了 git 的钩子函数，我们主要在 commit 之前进行检查，所以用到了 pre-commit 这个钩子。
lint-staged 在 pre-commit 的时候执行，定义了对 git 暂存区中的文件执行的任务，我们这里只对 js 文件做 eslint 的格式化校验。
> 提醒：因为 pre-commit 这个钩子是需要配合 git 去用的，它主要对文件夹 `.git/hooks/pre-commit` 文件做手脚，当我们从 git 上拉下来项目时，如果之前没对 hooks 下的文件做修改，那 hooks 下的文件都是以 sample 结尾的，这个时候钩子函数是不起作用的，当我们安装了 husky 之后，husky 会自动对 hooks 下面的文件做修改，当你安装完 husky 之后，再打开 `.git/hooks` 文件夹，你会发现所有的钩子文件，都会存在一份带有.sample 的，一份不带.sample 的，不带.sample 的文件就是 husky 创建的，这个才会让 git 钩子起作用。所以我们最好是先拉项目，然后再安装 husky，否则可能会导致 husky 失效。如果你是新开发项目，开发完后才提交到 git，开发完之后，你可以先关联 git 仓库，然后重新安装一下 husky 这个包就可以了。

## 五、.editorconfig
“EditorConfig帮助开发人员在不同的编辑器和IDE之间定义和维护一致的编码样式。EditorConfig项目由用于定义编码样式**的文件格式**和一组**文本编辑器插件组成**，这些**插件**使编辑器能够读取文件格式并遵循定义的样式。EditorConfig文件易于阅读，并且与版本控制系统配合使用。”


不同的开发人员，不同的编辑器，有不同的编码风格，而EditorConfig就是用来协同团队开发人员之间的代码的风格及样式规范化的一个工具，而.editorconfig正是它的默认配置文件。


_示例文件：_
```perl
# 告诉EditorConfig插件，这是根文件，不用继续往上查找root = true
# 匹配全部文件
[*]
# 结尾换行符，可选"lf"、"cr"、"crlf"
end_of_line = lf
# 在文件结尾插入新行
insert_final_newline = true
# 删除一行中的前后空格
trim_trailing_whitespace = true
# 匹配js和py结尾的文件
[*.{js,py}]
# 设置字符集
charset = utf-8

# 匹配py结尾的文件
[*.py]
# 缩进风格，可选"space"、"tab"
indent_style = space
# 缩进的空格数
indent_size = 4

# 以下匹配，类同
[Makefile]
indent_style = tab# tab的宽度tab_width = 4
# 以下匹配，类同
[lib/**.js]
indent_style = space
indent_size = 2

[{package.json,.travis.yml}]
indent_style = space
indent_size = 2
```
## 六、项目实践
```json
{
  "name": "quickstart-react",
  "version": "0.1.0",
  "scripts": {
    "lint": "npm run lint:js && npm run lint:style && npm run lint:prettier",
    "lint-staged": "lint-staged",
    "lint-staged:js": "eslint --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint --fix --cache --ext .js,.jsx,.ts,.tsx --format=pretty ./src && npm run lint:style",
    "lint:js": "eslint --cache --ext .js,.jsx,.ts,.tsx --format=pretty ./src",
    "lint:prettier": "check-prettier lint",
    "lint:style": "stylelint --fix \"src/**/*.less\" --syntax less",
    "prettier": "prettier -c --write \"**/*\""
  },
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint-staged"
    }
  },
  "lint-staged": {
    "**/*.less": "stylelint --syntax less",
    "**/*.{js,jsx,tsx,ts,less,md,json}": [
      "prettier --write",
      "git add"
    ],
    "**/*.{js,jsx}": "npm run lint-staged:js",
    "**/*.{js,ts,tsx}": "npm run lint-staged:js"
  },
  "dependencies": {
  },
  "devDependencies": {
    "@umijs/fabric": "^1.2.1",
    "check-prettier": "^1.0.3",
    "husky": "^3.0.0",
    "lint-staged": "^9.0.0",
    "prettier": "^1.17.1",
    "stylelint": "^10.1.0"
  }
}
```
> 备注：以上配置只列出了必须依赖，及相关scripts脚本，具体规范配置参考每个小章节。

