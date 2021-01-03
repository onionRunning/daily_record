### eslint 代码检测工具

### 1. 背景

- 13 年开源目标是插件化的js代码检测工具
- 同类插件了解: jsLint/jsHint/tslint/eslint

> jsLint: 规则完全定死开箱即用,拓展性差 必须要按照开发者设置的规则
> jsHint 基于jsLint 二次开发，支持部分规则开关,灵活性有些许提高
> eslint and tslint 支持插件,支持自定规则，规则齐全...

```
eslint vs tslint
- tslint 已经停止更新,
- eslint star 远大于tslint, 官方推荐eslint 
- eslint 搭配 prettier 更加友好
```

为什么要用eslint?
- 规范代码,书写层面统一风格


### 2. 基本配置介绍

2.1 **配置文件类型优先级**

```
- 根目录下配置: .eslintrc.js / .eslintrc.yml / .eslintrc.json / .eslintrc
- package.json 中配置 eslintConfig 属性...
- 优先级: 从左往右,从上往下, 可以针对不同文件目录编写不同的lint规则在该文件目录下编写自己的规则（root: true）, 规则存在的意义针对一个项目而言应该是统一的 
```

2.2 **配置文件常见配置介绍**
可用eslint init 生成相应的配置
``` json
{
  env: {
    browser: true,
    es6: true, 
  },
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: 'tsconfig.json',
    sourceType: 'module',
  },
  extends: ['5e'],
  plugins: ['@typescript-eslint'],
  rules: {
    '@typescript-eslint/adjacent-overload-signatures': 'off',
  }
}
```
- env               设置启用环境
- parser            解析器, eg: ts解析, es6高级语法
- parserOptions     解析对应的配置文件
- plugins           插件
- rules             规则以及错误级别
- extends           用于基础文件配置规则继承

> 我们需要关注的几个配置: plugins, rules, extends

```
plugins: 插件 
很多插件都有自己开发了很多特定的规则,我们可以直接按需去设置 eg: @typescript-eslint / eslint-plugin-react
在plugin中是可以简写的: plugin:['react'] 配置好以后我们可以直接在rule中去设置规则以及规则级别
```

```
rules: 规则 
key - value 形式, 我们引用了插件就可以在下面直接使用
rules: {
  '@typescript-eslint/adjacent-overload-signatures': 'off',
}
off - 0 关闭
warn - 1 警告
error - 2 报错中断程序
当然有些规则是需要按照文档的规则去设置
'@typescript-eslint/array-type': [
  'error',
  {
    default: 'array',
  },
]
```

```
extends: 可以继承你的配置规则

{
  extends: ['5e']
}
我们在项目中直接安装了 eslint-config-5e， 直接引用就可以用 5e 项目中配置的规则

```
ps. plugins, extends, 特定的命名在使用时是可以省略的: plugins:['react']   extends:['5e']


2.2 **我们需要知道的一些问题**

> prettier or eslint 的区别?

```
1. prettier 格式化代码,按照设置的配置文件可以对: js/jsx/ts/css/scss/html/json/markdowm/yaml ... 我们一般用这个插件对我们的js/ts按照配置去格式化,他的侧重点是格式化。

2. eslint 是代码检测工具,检测js/ts 语法是否符合我们定义的规则,当然有些不合法的规则 eslint是可以有修复功能(fix) 他的侧重点是check code or fix,（虽然eslint 是具有fix 功能但是也一些规则他是修复不了的）

3. 常见的流程: 先prettier化代码 -> 然后在进行check/fix -> ...

ps. 可能会出现prettier格式化的代码，eslint check 不合格, 这种情况就是俩者的配置出现了冲突 一般情况下我们添加 prettier/eslint 需要去check 俩者是否出现了小冲突。 
```

> 项目中搭配使用?

```
1. 搭配git-hooks , 一般在git commit 时进行prettier and check 操作, 如果eslint 检测出规则不合法就会中断 git cimmit 操作

2. 利用编辑器下载相应的插件 然后编辑器直接对你的代码进行check报错提示

3. web端与构建工具搭配使用, webpack 开发环境下 每次保存代码时就会 eslint check 代码， 中断webpack dev server 过程

目前比较优秀的方式是第一种和第二种搭配使用
```

> tslint 转eslint规则转换？

```
1. 之前的tslint插件 规则有差不多200 多个规则,一一对应转换比较困难。 
2. eslint 也为我们提供了一个转换插件 ,tslint-to-eslint-config  把tslint 规则转换为相应的eslint规则
3. 当然也会有一些规则可能双方平台不一致,然后需要针对这些差异进行处理,找类似的规则进行代替/或者进行移除。
4. tslint 转 eslint 转换之后 tslint 这个包也需要去掉。
```

> eslint 命令行执行到底做了什么？

```

1. package.json
...
"fix:lint": "eslint --fix --ext .jsx,.js,.tsx,.ts ./src",
"5elint": "5elint"

我们直接在项目下配置后, 可直接用 yarn fix:lint/ npm run 5elint 

node_modules 自身的特性, 我们发布的npm包设置有bin 目录, 并且在 package.json 中设置:   
...
"bin": {
  "5elint": "./bin/command.js"
}

2. command.js:

#! /usr/bin/env node   // 指明js脚本执行的环境是node
const program = require('commander')
const child = require('child_process')

program.action(function (type) {
  if (type === 'v') {
    console.log('当前版本号', require('../package').version)
  }
  try {
    child.exec(`eslint -f table -c ./node_modules/eslint-config-5e/base.js --ext .jsx,.js,.tsx,.ts ${process.cwd()}`, function (err, sto) {
      if (err) {
        console.log(err)
      }
      console.log(sto)
    })
  } catch (error) {
    console.log(error)
  }
})
program.parse(process.argv)


3. eslint.js

if (process.argv.includes("--init")) {
    await require("../lib/init/config-initializer").initializeConfig();
    return;
}
// Otherwise, call the CLI.
process.exitCode = await require("../lib/cli").execute(
    process.argv,
    process.argv.includes("--stdin") ? await readStdin() : null
);

```

### 3. 实现一个简单的规则

> eslint 官方核心规则库就是搞了一套格式规范，大家需要按照相应的格式去编排我们的规则: 介绍一个比较常用的核心规则, no-var

```
// no-var.js 隐藏了一些代码

module.exports = {
    meta: {
        messages: {
            unexpectedVar: "Unexpected var, use let or const instead."
        }
    },
    create(context) {
        return {
            "VariableDeclaration:exit": function(node) {
                if (node.kind === "var") {
                    context.report({
                        node,
                        messageId: "unexpectedVar"
                    });
                }
            }
        };
    }
};

// no-var-test.js
const RuleTester = require("eslint").RuleTester;
const rule = require("./no-var");
const ruleTester = new RuleTester({ parserOptions: { ecmaVersion: 2018 } });

ruleTester.run("my-rule", rule, {
    valid:["const a = '1111'"],
    invalid: [
        {
            code: "var a = '123'",
            errors: [
                {
                    messageId: "unexpectedVar"
                }
            ]
        }
    ]
});

我们可以用官方脚手架打一个base 然后把这源码按照规范丢进去 -> 本地引用插件,然后规则就可以直接使用  'eslint-plugins-onion/no-new-var': 'error'
```

> 写到这里: 其实比较浅显，没有涉及到eslint源码具体的实现，后续在研究。

### 4. summary
- 我们知道eslint基本配置以及基本规则添加
- 我们知道项目中如何搭配eslint 使用
- 具体项目中常见的一些问题
- 如何去写一个简单规则


