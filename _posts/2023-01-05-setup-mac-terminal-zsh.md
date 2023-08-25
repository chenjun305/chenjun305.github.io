---
layout: post
title: mac下zsh终端配置
date: 2023-01-05
description: 
tags: dev-tools
categories: programming
---
### homebrew安装
[Homebrew](https://brew.sh/) 是mac下常用的软件包管理工具,安装zsh需要依赖它。如果还没有安装,请先安装它:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
然后根据运行完成后的提示进行配置。

### zsh 
从 macOS Catalina 开始，zsh (Z shell) 是所有新建用户帐户的默认 Shell。  
bash 是 macOS Mojave 及更早版本中的默认 Shell。  
zsh 相比 bash 更强大,支持更多的功能和配置。  
可通过`echo $0`或`echo $SHELL`命令验证当前用户默认的 Shell。如果不是 zsh,可以通过以下命令切换:
```
$ echo $SHELL

$ chsh -s /bin/zsh
```

### iTerm2
iTerm2 是一款很好的 mac 终端模拟器,支持更多主题和功能。它比默认的 Terminal 更强大。  
可以直接到[iTerm2官网](https://iterm2.com/)下载安装,
也可以通过 Homebrew 安装:
```
brew install --cask iterm2
```
后续的操作请在安装好的iTerm2中进行.

### git安装
git是版本控制工具，是开发必备工具。如果还没有安装,可以通过homebrew安装:
```
brew install git
```

### oh-my-zsh
[oh-my-zsh](https://ohmyz.sh/) 可以让 zsh 更强大,它提供了很多主题和插件。  
可通过以下命令进行安装：
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### PowerLevel10K Theme
[Powerlevel10k](https://github.com/romkatv/powerlevel10k) 是一个很棒的 Zsh 主题,可以让终端界面更加清爽美观。  
安装 Powerlevel10k:
```
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```
打开 ~/.zshrc 文件,找到 ZSH_THEME="主题名称",将主题名称改为 `powerlevel10k/powerlevel10k`
```
ZSH_THEME="powerlevel10k/powerlevel10k"
```
然后使.zshrc生效:
```
source ~/.zshrc
```
完成后会进入 Powerlevel10k 的配置界面,可以进行主题颜色和其他细节的设置。第一步就是询问是否下载 Nerd Fonts,选择 Yes 后回车即可。安装完成后就可以重启终端生效了。  
然后会继续进行PowerLever10k的其他配置,比如修改主题风格、字体等。最后保存并退出即可。  
后续如果想要更改配置,可以通过以下命令重新进入配置界面:
```
p10k configure
```

### 配置iTerm2主题
iTerm2支持很多主题,可以让终端更漂亮。  
进入iTerm2首选项,点击Profiles标签下的Colors,选择一个主题应用。  
也可以从网上下载[iterm2 colors themes](https://iterm2colorschemes.com/)的主题进行下载后导入。  
或者github上找到一些好看的主题：  
```
// coolnight主题
curl https://raw.githubusercontent.com/josean-dev/dev-environment-files/main/coolnight.itermcolors --output ~/Downloads/coolnight.itermcolors
```

### 安装zsh plugins
* zsh-autosuggestions
```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
* zsh-syntax-highlighting
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
打开`~/.zshrc`, 更改plugins配置：
```
plugins=(git zsh-autosuggestions zsh-syntax-highlighting web-search)
```
使`~/.zshrc`配置生效：
```
source ~/.zshrc
```


### 安装colorls
colorls是一个用来给ls命令加上颜色的Ruby gem。colorls可以让ls命令输出的文件列表更加醒目,一眼就可以区分文件类型和特殊文件,大大提高了可读性。它是命令行用户优化终端必备的小工具。  
安装：
```
sudo gem install colorls
```
打开`~/.zshrc`,添加ls的别名
```
alias ls='colorls'
```


### vscode配置
如果日常也使用vscode进行开发，可在vs code的settings.json加上如下配置，使vscode的terminal也使用zsh。
```
"terminal.integrated.fontFamily": "MesloLGS NF",
"terminal.integrated.defaultProfile.osx": "zsh",
```

### 效果
完成配置后的效果图如下所示：
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/mac-terminal-zsh-setup.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>