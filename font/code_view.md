<!-- vscode-markdown-toc -->
* 1. [code view](#codeview)
		* 1.1. [减少关于组件内部的逻辑，提取无关代码](#)
		* 1.2. [合理commit message](#commitmessage)
		* 1.3. [合理的提交commit 次数](#commit)
		* 1.4. [添加合理的注释](#-1)
		* 1.5. [不要照着原有的代码去改, 可以适当重构](#-1)
		* 1.6. [控制函数的行数](#-1)
		* 1.7. [关注下命名, 如果不合理的话直接备注](#-1)
		* 1.8. [关于函数的参数](#-1)
		* 1.9. [保持组件 标签的干净](#-1)
		* 1.10. [遇到疑惑🤔代码要提出自己意见](#-1)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->

##  1. <a name='codeview'></a>code view

> code view 到底看的是什么! 我觉得主要的侧重点就是: 1.代码风格 2.实现的逻辑正确性 3.进阶优化 然后针对以上3点我提出了以下几点view建议

**目标**

```markdown
- 代码风格
- 重复代码,提取复用代码,抽象
- 拥抱变化,关闭修改
- 优化代码逻辑与书写风格
- 代码健壮性,检查逻辑漏洞,考虑异常边界
```

**目标是个漫长的过程,下面就是具体量化的一些细节,后续code view就会从这些量化的细节方面进行提出建议**

####  1.1. <a name=''></a>减少关于组件内部的逻辑，提取无关代码

```markdown
- 组件内部会有很多代码, 比如我们对一个数组做数组处理,等等逻辑 像这些代码就可以提出去新建一个该目录下的utils。
- 减少某个组件的代码量, 阅读好看的代码是比较舒适的一件事, 但是看你的逻辑一眼下去都是与本组件内部业务无关的代码就很难去阅读!
```

---

####  1.2. <a name='commitmessage'></a>合理commit message 

```markdown
- commit message: bug fix  what? 这个不是很友好!
- code view有的时候没有重心, 你的message需要描述的更加清晰!
```
---

####  1.3. <a name='commit'></a>合理的提交commit 次数

```markdown
- 不要写一大堆代码再去commit, 这样对view者非常的不友好!
- 建议一个commit 就是一个小功能或feature, 如果你的功能模块比较大那就分解成为小功能然后提交多个commit!
```

---

####  1.4. <a name='-1'></a>添加合理的注释

```markdown
- 讨厌别人不写注释 vs 自己不喜欢写注释
- 注释是真的非常有必要的, 如果没有合理的注释: 很久之后再回头看,这代码写的什么意思(为了自己和他人的安全着想)!
```

---


####  1.5. <a name='-1'></a>不要照着原有的代码去改, 可以适当重构

```markdown
- 习惯了copy代码, 写代码的时候总是喜欢按照已有的代码去写,但是可能以前的写法就是有问题的!
- 我们应该鼓励去重构代码, 让自己的代码看起来更加舒适（前提是你能把控好代码, 别把逻辑都改的出现了bug）!
```

---

####  1.6. <a name='-1'></a>控制函数的行数

```markdown
- 写函数的时候 要注意看看函数的长度, 越长逻辑也复杂, 圈复杂度越高!
- 建议: 函数过长需要去拆分,解耦合!
```

---

####  1.7. <a name='-1'></a>关注下命名, 如果不合理的话直接备注

```markdown
- 单词拼写错误: vscode 可以用插件(Code Spell Checker)进行提示!
- 建议命名错误打回修复!
```

---
####  1.8. <a name='-1'></a>关于函数的参数 

```markdown
- 我们有的时候为了兼容函数的写法无限的往方法里增加形参,函数的参数个数 小于等于3个是比较合适比较方便阅读的
- 如果过多表明函数的逻辑也不简单, 打回重构!
```

---
####  1.9. <a name='-1'></a>保持组件 标签的干净

```markdown
- 如果标签上事件逻辑过长，请提取事件函数到return外面去，保持标签整洁, jsx本来比较丑我们要尽量让代码好看!
- 不要在标签上写内联样式,保持代码的整洁性
```

####  1.10. <a name='-1'></a>遇到疑惑🤔代码要提出自己意见

```markdown
- 良性提出想法,比如组件太大需要进行拆分成小组件等
- 代码优化,边界场景考虑等
```



参考文章:

1. [Code Review最佳实践](https://mp.weixin.qq.com/s?__biz=MzIwMTQwNTA3Nw==&mid=400946871&idx=1&sn=5a125337833768d705f9d87ba8cd9fff&scene=1&srcid=0104FLyeXIS6N0EShgDseIfI&key=41ecb04b051110031290b34976240e650f0169d239c89f125162a89c8d3412f2087198612e71fd7685cae9eebe08e295&ascene=0&uin=MTYyMDMzMTAwMA%3D%3D&devicetype=iMac+MacBookPro11%2C5+OSX+OSX+10.10.5+build(14F1509)&version=11020201&pass_ticket=dc5bBckt1XSthRKTIsukYHIcAvKfv0jninbMlYQ5TWnE6XS%2FrRkdHKlJjNTI2Wsg)