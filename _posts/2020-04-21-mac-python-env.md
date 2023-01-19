---
layout: post
title:  Mac下Python开发环境配置
date:   2020-04-21 21:34:07
description: 
tags: python
categories: devops
---

### 安装Homebrew及一些很有用的工具

{% highlight shell %}
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
$ brew install wget // 安装wget工具
$ brew install tree  // 安装tree这个很有用的工具，树结构列出文件夹
{% endhighlight %}

### git配置

Mac终端中输入git命令会触发安装开发工具的提示，按照提示就可以把git安装好。

{% highlight shell %}
$ git config --global alias.ci commit
$ git config --global alias.co checkout
$ git config --global alias.st status
$ git config --global alias.br branch
$ git config --global alias.pl pull
$ git config --global alias.ps push
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
$ git config --global user.name "你的用户名"
$ git config --global user.email "你的邮件地址"
$ git config --global color.ui auto
{% endhighlight %}

配置存储在～/.gitconfg文件里

### 生成SSH Key

用于服务器远程登陆，git clone私有项目等，推荐生成比RSA更安全更高效的ed25519

```
~$ ssh-keygen -t ed25519 -C " 你的邮件地址 "
~$ ls .ssh/
id_ed25519     id_ed25519.pub
```

### 安装bash-completion及git-completion

{% highlight shell %}
$ brew install bash-completion
$ cd /usr/local/opt/bash-completion/etc/bash_completion.d/
$ curl -L -O https://raw.github.com/git/git/master/contrib/completion/git-completion.bash
$ brew unlink bash-completion
$ brew link bash-completion
{% endhighlight %}

按照提示将

```
[[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"
```

放入.bash_profile文件。

### 把终端配置的漂亮一点

编辑.bash_profile文件，加入以下内容

{% highlight shell %}
find_git_branch () {
     local dir=. head
     until [ "$dir" -ef / ]; do
         if [ -f "$dir/.git/HEAD" ]; then
             head=$(< "$dir/.git/HEAD")
             if [[ $head = ref:\ refs/heads/* ]]; then
                 git_branch=" (${head#*/*/}) "
             elif [[ $head != '' ]]; then
                 git_branch=" (detached) "
             else
                 git_branch=" (unknow) "
             fi
             return
         fi
         dir="../$dir"
     done
     git_branch=''
 }
 
 PROMPT_COMMAND="find_git_branch; $PROMPT_COMMAND"
 
 export CLICOLOR=1
 PS1="\w\$git_branch\$ "
 export LSCOLORS=gxfxaxdxcxegedabagacad
{% endhighlight %}

添加命令别名配置，把下面命令加入.bash_profile

```
alias l='ls -lah'
alias grep='grep --color'
```

让配置立即生效的命令是：

```
$ source .bash_profile 
```

### 配置vim

```
$ git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
$ sh ~/.vim_runtime/install_awesome_vimrc.sh
```

### pip & Django安装
Mac自带的Python是2.7版本，通过以下命令安装pip

```
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python get-pip.py
```

.bash_profile加入以下内容

```
export PIP_BIN=/Users/junchen/Library/Python/2.7/bin
export PATH=$PATH:$PIP_BIN
```

安装Django

```
$ pip install Django==1.6.11
```

检查django是否安装成功

```
~$ python
Python 2.7.10 (default, Aug 17 2018, 19:45:58) 
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.0.42)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import django
>>> 
```

没有报错说明安装成功了。
django被安装到以下目录：

```
/Library/Python/2.7/lib/python/site-packages/
```

### 安装pycharm

[https://www.jetbrains.com/pycharm/download/](https://www.jetbrains.com/pycharm/download/)
可以下载免费的社区版安装即可



