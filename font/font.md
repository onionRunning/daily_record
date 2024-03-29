<!-- vscode-markdown-toc -->
* 1. [项目组织规范](#项目组织规范)
  * 1.1. [通用项目组织规范](#通用项目组织规范)
  * 1.2. [目录组织风格](#目录组织风格)
  * 1.3. [脚手架模版项目](#脚手架模版项目)
* 2. [编码规范](#编码规范)
  * 2.1. [命名](#命名)
    * 2.1.1. [js变量声明](#js)
    * 2.1.2. [变量声明](#变量声明)
    * 2.1.3. [函数命名](#函数命名)
    * 2.1.4. [组件命名](#组件命名)
    * 2.1.5. [css命名](#css)
  * 2.2. [代码格式化](#代码格式化)
    * 2.2.1. [typescript格式化](#typescript)
    * 2.2.2. [css 风格检测](#css-1)
  * 2.3. [code view](#codeview)
* 3. [开发工作流规范](#开发工作流规范)
  * 3.1. [关于技术栈选型](#关于技术栈选型)
  * 3.2. [语言](#语言)
  * 3.3. [框架](#框架)
  * 3.4. [样式](#样式)
  * 3.5. [包管理](#包管理)
* 4. [技术方案沉淀](#技术方案沉淀)
  * 4.1. [相关文档规范](#相关文档规范)
  * 4.2. [eslint库沉淀](#eslint)
  * 4.3. [脚手架库](#脚手架库)
  * 4.4. [ui组件库](#ui)
  * 4.5. [函数库/npm私有库](#npm)
* 5. [编写文档相关](#编写文档相关)
* 6. [参考文章](#参考文章)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

# 前端协作规范

## 1. <a name='项目组织规范'></a>项目组织规范

### 1.1. <a name='通用项目组织规范'></a>通用项目组织规范

> 尽量保持根目录的干净,除非是必须要放到根目录下的

```markdown
- README.md: 项目说明。
  - 简要描述
  - 技术栈相关
  - 运行、打包、部署
  - 文档或文档说明入口

- dist: 项目编译后的文件
- build: 构建 部署相关配置
- src 源代码
- ...
```

### 1.2. <a name='目录组织风格'></a>目录组织风格

* 项目: 统一项目文件目录参考如下(复数文件带上s)

```markdown
...
src 
    - assets     静态资源
    - pages      页面组件
    - components 公共组件 
    - global     公用配置文件夹/ const, utils, ...
    - http       请求服务端数据
    - stores     状态管理
    ...

```

* 关于项目命名: aa-bb 小写加中划线

```md
bosslist-font
eslint-config-5e
```

* 文件夹命名: aa_bb 小写加下划线

```md
pages
    - login
    - project_management
    - user_operate
```

### 1.3. <a name='脚手架模版项目'></a>脚手架模版项目

> 基础脚手架按照一下模版初始化项目进行开发

[5e 内部命令集合工具](http://dev.moumoux.com:9088/-/web/detail/5e)

```md
- next-react-temp
- umi-react-temp
- react-base-temp
- dumi-react-temp
- storybook-temp
```

---

## 2. <a name='编码规范'></a>编码规范

### 2.1. <a name='命名'></a>命名

#### 2.1.1. <a name='js变量声明'></a>js变量声明

```markdown
- boolean值类型的申明: isXxx / hasXxx / canXxx ...
- 常量的声明: 采用 UPPER_CASE 这种形式: AAA_BBB / AAA
```

#### 2.1.2. <a name='变量声明'></a>变量声明

```markdown
- 小驼峰命名, 用英文去描述命名,不要出现拼音
- 以下特殊变量可以采用下列书写方式命名: ID, URL, Android, IOS 等
```

#### 2.1.3. <a name='函数命名'></a>函数命名

```markdown
- 小驼峰命名:  用英文去描述命名,
const onChange = () => {}
```

#### 2.1.4. <a name='组件命名'></a>组件命名

```markdown
- 组件命名(帕斯卡)大驼峰命名: AbCd
- 文件夹下的文件命名小写加下划线

- task
    - index.tsx
    - task_details.tsx
    - index.less
    - utils.ts

const TaskDetails = () => {
    return (
        <div> 
            <p>hello world!</p>
        </div>
    )
}
```

#### 2.1.5. <a name='css命名'></a>css命名

```markdown
- aa-bb-cc/aa-bb  小写加中划线, 不要拼音命名
- 文件命名: 一般与当前的js/tsx 文件的文件名相同

.pc-task-title {}
```

### 2.2. <a name='代码格式化'></a>代码格式化

#### 2.2.1. <a name='typescript格式化'></a>typescript格式化

> eslint插件: [eslint-config-5e](https://www.npmjs.com/package/eslint-config-5e)

```md
1. 编辑器vscode插件分别安装: eslint,prettier等前置插件 通过vscode 报红修改
2. git hook的方式进行拦截代码的commit
```

#### 2.2.2. <a name='css-1'></a>css 风格检测

> 基于umi插件(@umijs/fabric)进行检测, 不作为强制提交标准(vscode编辑器中会有提示)

### 2.3. <a name='codeview'></a>code view

> 分支合并提pr的时候, 开始view 相关代码!

```md
- 编程原则、设计思想
- 代码耦合度、重复代码
- 代码健壮性
- 异常边界场景是否考虑
- 代码的性能以及效率
```

[view checklist](./code_view.md)

---

## 3. <a name='开发工作流规范'></a>开发工作流规范

[工作流程](./git.md)

---

### 3.1. <a name='关于技术栈选型'></a>关于技术栈选型

> 前端以react框架作为基础框架

### 3.2. <a name='语言'></a>语言

    - ts 

    - js

### 3.3. <a name='框架'></a>框架

    - React

    - 备选
        - react-native
        - next
        - umi
    
    - 生态
        - 路由
            - router v5

        - 状态管理
            - redux
            - hook context
            - RxJS
            - mobx(rn 推荐)

        - UI框架
            - antd-design antd-mobile 
            - material-ui
            - ui-component-5e

        - 图标
            - 统一用svg替换 png或jpg    

### 3.4. <a name='样式'></a>样式

    - 命名
        - BEM
    - css-in-js
        - styled-components
    - css 预先处理器
        - less 一般情况下不考虑scss    

### 3.5. <a name='包管理'></a>包管理

    - yarn

> **关于npm包的选择**

```markdown
1. 关于npm包的选择, 除了上诉的这些包以外,确定项目只中是否真的需要这个插件或npm包, 而不是只用到了一个方法就把整个插件引用到项目中

2. 选择插件时看 star数量和npm的下载数量, 否则遇到不知名的包,出现问题好难去解决的!
```

ps. 关于组织项目: 项目一般是多人协作去完成的, 针对项目不要过分的去设计, 你要与你的协同者达成一个共识共同去完成这个东西, 千万不要去钻牛角尖, 齐心协力完成任务!

---

## 4. <a name='技术方案沉淀'></a>技术方案沉淀

### 4.1. <a name='相关文档规范'></a>相关文档规范

* [开发工作流规范](./git.md)

* [code view](./code_view.md)

* [前端协作规范](./font.md)

---

### 4.2. <a name='eslint'></a>eslint库沉淀

> [eslint-config-5e](https://www.npmjs.com/package/eslint-config-5e)

[项目地址](https://www.npmjs.com/package/eslint-config-5e)

---

### 4.3. <a name='脚手架库'></a>脚手架库

> [5e-cli](https://git.dev.moumoux.com/base/5e-cli)

---

### 4.4. <a name='ui组件库'></a>ui组件库

> [ui 库](http://dev.moumoux.com:9080/)

---

### 4.5. <a name='函数库/npm私有库'></a>函数库/npm私有库

* [函数库](https://git.dev.moumoux.com/base/base-5e-utils)

* [npm 仓库](http://dev.moumoux.com:9088/)

**ps. 关于内部私有仓库发包的用法: [how to use](./搭建私有npm.md)**

---

## 5. <a name='编写文档相关'></a>编写文档相关

* 概要设计方案

* 详细设计方案

## 6. <a name='参考文章'></a>参考文章

1. [怎么制定前端协作规范?](https://juejin.cn/post/6844903897610321934#heading-27)
