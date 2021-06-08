### 前端编码规范

#### 项目文件目录规范

- 项目: 统一项目文件目录参考如下(复数文件带上s)

```
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

- 关于项目命名: aa-bb 小写加中划线

```
bosslist-font
eslint-config-5e
```

- 文件夹命名: aa_bb 小写加下划线

```
pages
    - login
    - project_management
    - user_operate
```

#### 命名

> ***js变量声明***

- boolean值类型的申明: isXxx / hasXxx / canXxx ...

- 常量的声明: 采用 UPPER_CASE 这种形式: AAA_BBB / AAA

> ***变量声明***

- 小驼峰命名, 用英文去描述命名,不要出现拼音

- 以下特殊变量可以采用下列书写方式命名

```
ID 
URL 
Android 
IOS
```


> ***函数命名***


> ***组件命名***

- 组件命名(帕斯卡)大驼峰命名: AbCd

- 文件夹下的文件命名小写加下划线

```
- task
    - index.tsx
    - task_details.tsx
    - index.less
    - utils.ts
```

> ***css命名***

- aa-bb-cc  小写加中划线

- 文件命名: 一般与当前的js/tsx 文件的相同

```
1. index.less/index.scss/index.css

2. global.less/list_item.less
```


#### 代码格式检测

- eslint插件: [eslint-config-5e](https://www.npmjs.com/package/eslint-config-5e)

```
1. 编辑器vscode插件分别安装: eslint,prettier等前置插件 通过vscode 报红修改
2. git hook的方式进行拦截代码的commit
```

- css 属性顺序

```
const fabric = require('@umijs/fabric');
module.exports = {
  ...fabric.stylelint, // css 会有报错提示
};
```

#### typescript


#### 项目设计


#### 脚手架项目模版


参考文章:

1. [怎么制定前端协作规范?](https://juejin.cn/post/6844903897610321934#heading-27)