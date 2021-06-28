### 开发工作流规范

#### Git 分支模型

- 项目有2个主分支: dev/master, 这2个分支的代码只能通过提pr的方式去merge, 不允许强推,也不允许删除

- dev: 开发/测试环境

- master: 正式环境代表线上的版本

- 新版本开始时 dev 和 master 分支代码保持一致

#### PR
 
code view 需要通过以下checklist来进行[view](./code_view.md)

- 新版本开发从 dev分支切出功能分支, 做完后提pr 合到dev分支

- 线上紧急bug 从master 切出分支热修复分支,修复好后提pr合到master分支

- 提pr时要保证 , 当前分支 merge(保持commit 的完整性) 过你要合并的分支 避免冲突


#### 提交信息规范

- commit message 与你的内容要一致

- 关于commit message: 请描述清楚 不允许 'fix: bug' 然后就没了, 合理的需要描述的更加清楚

```
git commit -am 'fix:bug 修复登录页面多次调用auth校验接口的bug'

git commit -am 'add:feature 添加list页面下 list卡片组件的展示以及对应的接口逻辑'

git commie -am 'refactory:components 重构登录页面下的登录逻辑流程'
```

- 拆分commit 不允许太多的逻辑在一个commit 里面

#### CI/CD 流程

项目采用 Gitlab-CI 更新的方式, 我们在项目中去编写 .gitlab-ci.yml 就可以

##### 执行的环境

##### 触发的条件

```
1. 一般只允许, dev/master 主分支发生merge 操作时触发ci操作

2. 当然如果你是要紧急调试但是代码又没有合并到主分支上，允许在测试环境直接指向你要update的分支, 前提是不能影响其它的分支!
```
##### 划分持续集成的阶段

```
1. 环境准备: yarn

2. 构建打包: yarn build

3. 部署: deploy 不同环境的部署脚本不一样 注意进行区分
```



#### 参考文章

1. [Introduction to GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html)

