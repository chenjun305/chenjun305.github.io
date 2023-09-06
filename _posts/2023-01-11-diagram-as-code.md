---
layout: post
title: disgram as code
date: 2023-01-11
description: 
tags: dev-tools
categories: programming
---
Why diagram with code?
* Simple
* Version Control
* Efficiency

## [ Graphviz ](https://www.graphviz.org/)

[Drawing graphs with Dot](https://graphviz.gitlab.io/pdf/dotguide.pdf)

### dot language
* Simple code generates diagrams
```
digraph G {
	main -> parse -> execute;
	main -> init;
	main -> cleanup;
	execute -> make_string;
	execute -> printf;
	init -> make_string;
	main -> printf;
	execute -> compare;
}
```

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/graphviz-img1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

### 安装
Mac下可通过`brew install graphviz`安装。

* 命令行工具

```
$ dot -Tsvg input.dot

// If no input files are supplied, the program reads from stdin. For example:

$ echo 'digraph { a -> b }' | dot -Tsvg > output.svg
```

### Core concepts

#### Graphs/Subgraphs
* digraph 有向图
* graph 无向图
* subgraph 子图
* cluster 名字以cluster开头的特殊子图


#### Nodes
Three main types:
* Shapes
* Record-based
* User defined

#### Edges
* Connect objects
* Parts:
  * Head
  * Tail
  * Label
  * Shape

#### Attributes
Apply to graphs, nodes, edges



## [diagrams](https://diagrams.mingrammer.com/)
 (using python)
* 通过`pip install diagrams`安装


### [go-diagrams](https://github.com/blushft/go-diagrams)
(using golang)

## other tools 
* [websequencediagrams](https://www.websequencediagrams.com/) 时序图
```
title User Login
actor user
user->App: login with username/password
App->Server: Authentication Request
Server->Database: query user by username & password
note right of Server: authntication logic
Database->Server: user data
Server->App: Authentication Response
App->user: result
```
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/login-sequence-chart.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  web sequence diagrams
</div>

* [js-sequence-diagrams](https://bramp.github.io/js-sequence-diagrams/)
  * A simple javascript library to turn text into vector UML sequence diagrams.
  * Heavily inspired by websequencediagrams.com, who offer a serverside solution.
  * use Jison to parse the text, and [Snap.svg](http://snapsvg.io) to draw the image.
  * [VSCode Sequence diagrams](https://marketplace.visualstudio.com/items?itemName=AleksandarDev.vscode-sequence-diagrams)

* [draw.io](https://www.drawio.com)
  * [web版](https://app.diagrams.net/)
  * [vscode插件 ](https://marketplace.visualstudio.com/items?itemName=hediet.vscode-drawio)
  * [Create diagrams directly in VS Code](https://www.drawio.com/blog/embed-diagrams-vscode)
  * 免费且开源：http://draw.io是一款免费的在线绘图工具，可供个人用户和团队免费使用。它还是开源软件，用户可以根据需要自定义和扩展其功能。
  * 跨平台支持：http://draw.io可以在各种操作系统上使用，包括Windows、Mac和Linux。无论您使用哪种操作系统，都可以方便地访问和使用http://draw.io。
  * 多种导入和导出格式：http://draw.io支持多种文件格式的导入和导出，如XML、PDF、PNG、JPEG等。这使得您可以轻松地与其他流程图工具和文件进行兼容性交互。
  * 离线使用：虽然http://draw.io是基于云的工具，但它也支持离线使用。您可以将http://draw.io应用程序下载到本地计算机，即使没有互联网连接，也可以继续创建和编辑流程图。
  * 多种图表类型支持：除了流程图，http://draw.io还支持其他类型的图表，如组织结构图、网络图、UML图等。这使得http://draw.io成为一个多功能的图表绘制工具。

* [processon](https://www.processon.com/)
  * ProcessOn是一个在线作图工具的聚合平台，它可以在线画流程图、思维导图、UI原型图、UML、网络拓扑图、组织结构图等等，您无需担心下载和更新的问题，不管Mac还是Windows，一个浏览器就可以随时随地的发挥创意，规划工作；



