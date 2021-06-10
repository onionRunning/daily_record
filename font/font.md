## 前端协作规范
### 项目组织规范
#### 通用项目组织规范

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

#### 目录组织风格

- 项目: 统一项目文件目录参考如下(复数文件带上s)

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

#### 脚手架模版项目

> 基础脚手架按照一下模版初始化项目进行开发

**[5e 内部命令集合工具]**(http://dev.moumoux.com:9088/-/web/detail/5e)


---
### 编码规范
#### 命名
##### js变量声明

```markdown
- boolean值类型的申明: isXxx / hasXxx / canXxx ...
- 常量的声明: 采用 UPPER_CASE 这种形式: AAA_BBB / AAA
```

##### 变量声明

```markdown
- 小驼峰命名, 用英文去描述命名,不要出现拼音
- 以下特殊变量可以采用下列书写方式命名: ID, URL, Android, IOS 等
```

##### 函数命名

```markdown
- 小驼峰命名:  用英文去描述命名,
const onChange = () => {}
```

##### 组件命名

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

##### css命名

```markdown
- aa-bb-cc/aa-bb  小写加中划线, 不要拼音命名
- 文件命名: 一般与当前的js/tsx 文件的文件名相同

.pc-task-title {}
```

#### 代码格式化
##### typescript格式化 

> eslint插件: [eslint-config-5e](https://www.npmjs.com/package/eslint-config-5e)

```
1. 编辑器vscode插件分别安装: eslint,prettier等前置插件 通过vscode 报红修改
2. git hook的方式进行拦截代码的commit
```

##### css 风格检测

> 基于umi插件(@umijs/fabric)进行检测, 不作为强制提交标准(vscode编辑器中会有提示)

#### code view

> 分支合并提pr的时候, 开始view 相关代码!

```
- 编程原则、设计思想
- 代码耦合度、重复代码
- 代码健壮性
- 异常边界场景是否考虑
- 代码的性能以及效率
```

[view checklist](./code_view.md)

---
### 开发工作流规范


### 技术栈规范

### 技术沉淀

### 参考文章:

1. [怎么制定前端协作规范?](https://juejin.cn/post/6844903897610321934#heading-27)