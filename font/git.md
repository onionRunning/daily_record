### 开发工作流规范


#### Git 分支模型

- 项目有2个主分支: dev/master, 这2个分支的代码只能通过提pr的方式去merge, 不允许强推,也不允许删除

- dev: 开发/测试环境

- master: 正式环境代表线上的版本

- 新版本开始时 dev 和 master 分支代码保持一致

- 新版本开发从 dev分支切出功能分支, 做完后提pr 合到dev分支

- 线上紧急bug 从master 切出分支热修复分支,修复好后提pr合到master分支

- 提pr时要保证 , 当前分支 merge(保持commit 的完整性) 过你要合并的分支 避免冲突



#### 提交信息规范



#### CI/CD







#### 参考文章

1. [Introduction to GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html)

