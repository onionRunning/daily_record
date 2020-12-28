## 整理vscode常用插件～如何去开发一个插件

---

### 整理工作中常用的 vscode 插件

**默默点开了自己的插件库,发现有一些常用的插件 在这里就给大家安利一下**

> 让开我要开始水文啦

1. Beautiful UI 

```
作为一款具有32 beautiful color 主题的插件, 搞一手。 本人用的黑色主题 One Dark
```

2. Chinese (Simplified) Language Pack for Visual Studio Code

```
汉化版语言包, 这个汉化版更加方便
```

3. Code Spell Checker

```
检查你的命名拼写的问题, 编码过程中 我们经常会忽略变量是否拼写错误等问题, 这个可以给小波纹提示（不错）
```

4. ESlint 

```
不多逼逼, 代码检测必备, 代码提示很优秀
```

5. GitLens — Git supercharged

```
还在为哪行代码不知道是谁改的咩, 这款插件 可以告诉你 每一行代码的改动者 和 改动时间, 更清晰的记录(找到bug对应的锅, 就决定是你啦)
```

6. LeetCode

```
算法刷题: 不会不搞一手吧
```

7. Live Server

```
静态html页面 默认用服务启动
```

8. Material Icon Theme

```
不同文件或文件夹 有各自相对应的icon， 看起来更加的好看
```

9. Power Mode

```
你想打代码的时候出现火花吗, 并且出现各种特效吗， 那就用这个插件 让开发更加有趣, 自从用了这个插件后再也 不怕打代码无聊了😁
```

10. Turbo Console Log

```
就是打log 的时候更加方便 , 值得拥有
```

11. 韭菜盒子

```
VSCode 里也可以看股票 & 基金实时数据， 别人推荐过来的, 韭菜人 韭菜魂 , 韭菜才是人上人
```

12. 超越/坤坤 鼓励师

```
自行摸索, 视觉享受 -> 懂我意思吧
```

> 当然还有一大堆插件这个自行探索, 不愧是你

---

> 插件的目的是为了提升 1. 效率 2. 打代码的乐趣 3. 摸鱼 然后看了这么多， 难道自己就不想 开发一个插件吗 不 你不想!!!

**👇下面就教大家怎么开发一个vscode插件, 并且上传**

### 制作一个摸鱼插件

> 说明一下: 插件开发是一个比较庞大的模块, 本人只针对其中很小的一块来说明我们的 小插件开发

> 不是吧不是吧你不知道怎么做一个摸鱼插件, 没关系就在这 教你一手， 从此以后你也是vscode 摸鱼插件开发者

***本次插件是坦克大战, 不依赖网络的那种***

事先申明: 游戏来源 [github 地址](https://github.com/JulesWang/BattleCity.AI) 然后把这个整合到vscode插件中进行使用哈

***简约step:***

```
1. npm install -g yo generator-code / npm i vsce -g 安装依赖

2. yo code 创建项目 然后改动代码 参考如下

3. 核心文件逻辑: extension.js下:  webview + iframe + 读取本地文件 。 

[tank demo项目](https://gitee.com/onionrunning/tank)

4. 发布 vsce publish 

ps. vsce 不能命令行创建账号- 需要到网页去创建账号, 然后vsce login 时 需要有个toke 在参考链接2 中有比较详细的说明

```

然后我就创建了一个插件: game-tank 自己可以在vscode 插件模块进行下载， 离线玩也是没什么问题的哈。


> 写到这里是不是有点懵逼 啊啊

***就是vscode 的插件都是我们自己去进行开发的,很强***

---

1. 上述的简约过程比较简单, 在开发攻略以及对应的源码都有这里就不详细介绍了

2. 当然如果针对工程上有哪些可以做的, 比如常见的snipet 代码片段 自己可以按照自己的想法输出一套模版 然后就比较快的进行开发

3. 我们可以自己去玩一些有意思的东西, 比如交友 哈哈 最近其实是有出一款 看代码的陌陌版本， 就是无形之中提升了乐趣

4. 在下自己给自己打了5星舒服😄

---



参考文章:

1. [vscode 插件开发攻略](https://github.com/sxei/vscode-plugin-demo)
2. [VSCode插件开发入门](https://zhuanlan.zhihu.com/p/99198980)
3. [坦克大战js版本](https://github.com/JulesWang/BattleCity.AI)
4. [插件码云地址](https://gitee.com/onionrunning/tank/tree/master)