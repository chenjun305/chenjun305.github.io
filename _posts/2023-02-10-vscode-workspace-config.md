---
layout: post
title: VS Code 中的 .vscode 文件夹
date: 2023-02-10
description: 
tags: dev-tools
categories: programming
---
`.vscode` 是 VS Code 在项目目录下会特别查找的一个隐藏文件夹。它允许你为某个具体项目自定义 VS Code 设置,而不需要设置全局的用户配置或工作区配置。

## .vscode 文件夹包含什么

`.vscode` 文件夹可以包含以下文件:

- `settings.json` - 项目特定的 VS Code 设置
- `launch.json` - 项目特定的调试配置
- `extensions.json` - 项目特定的扩展推荐
- `tasks.json` - 项目特定的构建任务  
- `snippets` - 项目特定的代码片段

## 如何使用 .vscode

要在一个项目中使用 `.vscode` 文件夹:

1. 在项目根目录下创建一个 `.vscode` 文件夹
2. 添加你需要的任何 `.json` 配置文件,比如 `settings.json`
3. 配置此项目特有的 VS Code 设置、构建任务、调试设置等

现在当你在 VS Code 中打开这个项目时,它会读取和应用 `.vscode` 文件夹中的配置。

`.vscode` 的一些示例用法:

- 为项目覆盖默认的 VS Code 设置
- 定义项目特有的代码片段
- 配置项目中多个应用的调试启动设置

`.vscode` 文件夹是让项目具有可移植的开发环境的好方法,任何人在 VS Code 中打开这个项目都可以工作。

## settings.json
以下为settings.json 文件示例:
```
{
  "go.goroot": "~/.gvm/gos/go1.20.7",
  "go.gopath": "~/.gvm/pkgsets/go1.20.7/global",
  
  // 添加 Python 解释器路径
  "python.pythonPath": "/usr/local/bin/python3",
  
  // 设置默认格式化工具
  "editor.defaultFormatter": "esbenp.prettier-vscode",

  // 修改字体大小
  "editor.fontSize": 14,

  // 修改字体家族
  "editor.fontFamily": "Consolas, 'Courier New', monospace",

  // 显示行号
  "editor.lineNumbers": true,
  
  // 设置文件换行符
  "files.eol": "\n",
  
  // 配置 Emmet 插件
  "emmet.triggerExpansionOnTab": true,

  // 配置预览主题
  "workbench.colorTheme": "One Dark Pro",

}
```
可以根据个人喜好和项目需要,在 settings.json 中添加更多配置。

## launch.json
launch.json文件用于配置VS Code的调试功能。

除了基本的配置外,launch.json还可以进行更多高级定制:

- 可以配置多种调试配置(configurations),例如同时调试客户端和服务端
```
{
  "configurations": [
    {
      "name": "Debug Client",
      // 客户端调试配置
    },
    { 
     "name": "Debug Server",
     // 服务端调试配置
    }
  ]
}
```
- 可以通过"compounds"组合多个调试配置,进行多进程调试
- 可以通过"preLaunchTask"指定调试前执行的任务
- 可以配置调试时自动打开的文件或断点位置
- 可以通过"env"和"envFile"指定环境变量
- 可以配置远程调试设置,调试远程主机上的程序
- 等等

这里给出一些 .vscode/launch.json 文件的示例:

- 调试 Python 应用
```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: Current File",
      "type": "python",
      "request": "launch",
      "program": "${file}",
      "console": "integratedTerminal"
    }
  ]
}
```
- 调试 Node.js 应用
```
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "program": "${workspaceFolder}/index.js"
    }
  ]
}
```
- 调试 C++ 应用
```
{
  "version": "0.2.0", 
  "configurations": [
    {
      "name": "g++ build and debug active file",
      "type": "cppdbg",
      "request": "launch",
      "program": "${fileDirname}/${fileBasenameNoExtension}",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```
- 调试远程容器应用
```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "address": "localhost",
      "localRoot": "${workspaceFolder}",
      "remoteRoot": "/app" 
    }
  ]
}
```

总之,launch.json提供了非常丰富和强大的调试定制功能,可以满足复杂项目的调试需求。合理利用launch.json的各种配置可以极大提高调试效率。

## extensions.json
extensions.json 文件用于在项目级别配置 VS Code 的扩展推荐。

可以通过 extensions.json 来指定在打开该项目时,VS Code 应该推荐安装哪些扩展。

extensions.json 的基本结构如下:
```json
{
  "recommendations": [
    "extensionId1",
    "extensionId2",
    ...
  ] 
}
```
"recommendations" 是一个数组,包含要推荐的扩展的 ID。

例如,在一个 Python 项目中,可以这样配置:
```
{
  "recommendations": [
    "ms-python.python",
    "ms-toolsai.jupyter",
    "ms-python.vscode-pylance"
  ]
}
```
这样当别人打开这个项目时,VS Code 就会提示推荐安装这些对 Python 开发有帮助的扩展。

extensions.json 可以方便地帮助团队统一开发环境,也可以减少新人配置开发环境的工作。

## tasks.json
tasks.json文件用于在VS Code中配置项目的构建任务和测试任务。

除了基本的编译、构建命令配置外,tasks.json还支持更多高级功能:
- 可以通过"group"给任务分组,进行批量执行
- 可以配置"problemMatcher"来解析任务的输出,实现错误链接、问题面板集成
- 支持变量替换,例如${workspaceFolder}表示工作区文件夹
- 支持定义任务依赖关系
- 支持配置自定义命令、内联命令
- 支持配置任务Presentation信息,如图标、弹出面板等
- 可以配置触发任务重新运行的文件监视
- 等等

合理利用tasks.json,可以极大提升在VS Code中构建和调试应用的效率。所有任务执行和输出都集成在编辑器中,无需切换窗口。

这里给出几个 .vscode/tasks.json 文件的示例:

- Go语言编译和运行
```
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Build Go File",
      "type": "shell",
      "command": "go build",
      "group": "build"
    },
    {
      "label": "Run Go File",
      "type": "shell",
      "command": "go run ${file}",
      "group": {
        "kind": "build",
        "isDefault": true  
      }
    }
  ]
}
```

- Python运行和调试
```
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Run Python File",
      "type": "shell",
      "command": "python ${file}",
      "group": "test"
    },
    {
      "label": "Debug Python File",
      "type": "python",
      "request": "launch",
      "program": "${file}"
    }
  ]
}
```
- npm构建和测试
```
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "npm",
      "script": "build",
      "group": "build",
      "problemMatcher": []
    },
    {
      "type": "npm",
      "script": "test",
      "group": "test",
      "problemMatcher": []
    }
  ]
}
```

tasks.json非常强大,可以实现项目的编译、运行、调试、测试等功能。

## snippets
snippets文件用于在VS Code中自定义代码片段,可以极大提高编写重复代码的效率。

.vscode/snippets文件夹下可以包含不同语言的代码片段文件,文件名通常是语言名称加.json后缀,如:

- javascript.json - JavaScript代码片段
- python.json - Python代码片段
- html.json - HTML代码片段

每个snippet文件内包含多个代码片段的定义,结构如下:
```
{
    "Print to console": {
        "prefix": "log",
        "body": ["console.log('$1');"],
        "description": "Log output to console"
    }
}
```
主要字段说明:
- prefix - 触发代码片段的缩写
- body - 代码片段内容,可以包含变量$1、$2等
- description - 对代码片段的描述

在编辑器中,输入snippet前缀如log,然后按Tab,就可以自动扩展成完整的代码片段,极大提高了编码效率。

下面再举一些例子：
- Python
```json
{
    "For loop": {
        "prefix": "for",
        "body": [
            "for ${1:item} in ${2:items}:",
            "\t${3:print(item)}"
        ],
        "description": "For Loop"
    }
}
```
- HTML
```json
{
  "HTML Boilerplate": {
    "prefix": "html",
    "body": [
      "<!DOCTYPE html>",
      "<html lang=\"en\">",
      "<head>",
      "\t<meta charset=\"UTF-8\">",
      "\t<title>$1</title>",
      "</head>",
      "<body>",
      "\t$2",
      "</body>",
      "</html>"
    ],
    "description": "Boilerplate HTML"
  }
}
```
- Markdown
```json
{
  "Bold": {
    "prefix": "bold",
    "body": ["**${1:text}**"],
    "description": "Bold markdown syntax"
  }
} 
```
自定义snippet可以极大提升个人或团队的开发效率。