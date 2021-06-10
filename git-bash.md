# 为 win10 打造 Linux 终端（非 wsl）

![img](assets/168b2442a65d1d80)

**本文中的部分内容可能过期，最新配置请前往 github 查看：github.com/xnng/my-git…**

## 1. 前言

这个时候就需要一套好用的 `Linux 终端` + `Linux 环境`。

至于在 Windows 上使用 Linux 环境，无非就是跑项目的时候想跟别的环境保持一致，或者安装某些 windows 上不太好用的软件，比如 Redis、Ngin。总之只要你想折腾，Windows 上不会缺 Linux 环境。

不缺 Linux 环境，难222道缺 Linux 终端吗？

我想说的 Windows 自带的终端其实是 [Git Bash](https://link.juejin.im/?target=https%3A%2F%2Fgit-scm.com%2Fdownload%2Fwin)，严格意义上来说它并非自带，但它是每个程序员必装的软件，所以我且当它是自带的了。


## 2. 我喜欢用的终端是什么样子的？

`Git Bash` 基于 mintty，有 Linux 文件系统，还有常用的一些 Linux 命令。它的性能也很高，还能很方便的嵌入到各种开发工具中。这是我选择它的理由。

但它也有一些缺点，比如太丑了、不能开多标签、缺乏某些重要的命令。只要解决这些问题，那么它就是我心目中的完美终端。

## 3. 从设置快捷键开始

为了能够快速打开 Git Bash，我建议下给它设置一个全局快捷键。

- 按 Win 键，然后搜索 `Git Bash`，点击「打开文件所在位置」

![img](assets/167ea1148774443e)

- 右键快捷方式，点击「属性」，然后在这里设置一个快捷键，同时点击「高级」，把「以管理员身份运行」勾上，后面会在终端上改文件，勾选它可以避免很多麻烦。

![img](assets/167ea114884d956f)

- 解决快捷键延迟问题

  直接按 Win 键，搜索「服务」并打开，找到 `sysMain`，禁用它并重启电脑可解决。

![img](assets/167ea11488552a04)

这个服务如果找不到，就找它的旧名 `superFetch`，此服务其实是针对机械硬盘的缓存服务，对固态没用，可以放心禁用掉。

## 4. 修改命令提示符

默认的 `Git Bash` 长这样，自带的一些主题配色倒还能接受，但是这个命令提示符前的那一长串也太碍眼了，先来简化这个命令提示符吧。

![img](assets/167ea11489846246)

**以下所有命令都需要在 Git Bash 中执行**

下面这个文件是跟命令提示符有关的

```shell
$ vim /etc/profile.d/git-prompt.sh
```

如果不熟悉 vim，并且电脑上装了 VSCode，可以用以下命令打开文件

```shell
$ code /etc/profile.d/git-prompt.sh
```

将其修改为如下内容

```shell
if test -f /etc/profile.d/git-sdk.sh
then
	TITLEPREFIX=SDK-${MSYSTEM#MINGW}
else
	TITLEPREFIX=$MSYSTEM
fi

if test -f ~/.config/git/git-prompt.sh
then
	. ~/.config/git/git-prompt.sh
else
	PS1='\[\033]0;Bash\007\]'      # 窗口标题
	PS1="$PS1"'\n'                 # 换行
	PS1="$PS1"'\[\033[32;1m\]'     # 高亮绿色
	PS1="$PS1"'➜  '               # unicode 字符，右箭头
	PS1="$PS1"'\[\033[33;1m\]'     # 高亮黄色
	PS1="$PS1"'\W'                 # 当前目录
	if test -z "$WINELOADERNOEXEC"
	then
		GIT_EXEC_PATH="$(git --exec-path 2>/dev/null)"
		COMPLETION_PATH="${GIT_EXEC_PATH%/libexec/git-core}"
		COMPLETION_PATH="${COMPLETION_PATH%/lib/git-core}"
		COMPLETION_PATH="$COMPLETION_PATH/share/git/completion"
		if test -f "$COMPLETION_PATH/git-prompt.sh"
		then
			. "$COMPLETION_PATH/git-completion.bash"
			. "$COMPLETION_PATH/git-prompt.sh"
			PS1="$PS1"'\[\033[31m\]'   # 红色
			PS1="$PS1"'`__git_ps1`'    # git 插件
		fi
	fi
	PS1="$PS1"'\[\033[36m\] '      # 青色
fi

MSYS2_PS1="$PS1"
```

效果如下：

![img](assets/167ea11489945f94)

命令提示符被修改成了，右箭头 + 当前目录，这个非常像 oh-my-zsh 的默认主题，文字的颜色也改成了青色，窗口的标题也被简化了。

改的地方不多，就几行，都有注释，如果想自行修改可以参考[这篇文章](https://link.juejin.im/?target=http%3A%2F%2Fwww.voidcn.com%2Farticle%2Fp-wavhthxe-tr.html)，写的很详细。

**解决 Unicode 字符显示异常问题**

这里其实还有个坑。某些版本的 Win 10 存在 Unicode 字符显示异常的问题，比如 1809，具体的表现是，上面的那个右箭头会显示成方框。改编码方式是无效的，修改字体可解决。

点击[这里](https://link.juejin.im/?target=https%3A%2F%2Fraw.githubusercontent.com%2Fpowerline%2Ffonts%2Fmaster%2FDejaVuSansMono%2FDejaVu%2520Sans%2520Mono%2520for%2520Powerline.ttf)下载 DejaVu Sans Mono 字体

执行以下命令，会打开字体文件夹，将字体托进去会自动安装，然后将修改 Git Bash 的字体就能正常显示 Unicode 字符了。(start 是 cmd 所提供的命令)

```
$ start c://Windows//Fonts
复制代码
```

![img](https://user-gold-cdn.xitu.io/2018/12/26/167ea126b306e2ef?imageslim)

## 5. 修改主题

`Git Bash` 自带的那一套主题我是不太喜欢的，所以我自己改了一套，在用户目录下创建 `.minttyrc` 并写入以下内容即可

> 注意：这个文件中的第一行设置了字体，此字体如果上面没有安装的话，就把这行删了，否则会报错

```
$ vim ~/.minttyrc
复制代码
```

```
Font=DejaVu Sans Mono for Powerline
FontHeight=14
Transparency=low
FontSmoothing=default
Locale=C
Charset=UTF-8
Columns=88
Rows=26
OpaqueWhenFocused=no
Scrollbar=none
Language=zh_CN

ForegroundColour=131,148,150
BackgroundColour=0,43,54
CursorColour=220,130,71

BoldBlack=128,128,128
Red=255,64,40
BoldRed=255,128,64
Green=64,200,64
BoldGreen=64,255,64
Yellow=190,190,0
BoldYellow=255,255,64
Blue=0,128,255
BoldBlue=128,160,255
Magenta=211,54,130
BoldMagenta=255,128,255
Cyan=64,190,190
BoldCyan=128,255,255
White=200,200,200
BoldWhite=255,255,255

BellTaskbar=no
Term=xterm
FontWeight=400
FontIsBold=no
复制代码
```

效果如下：

![img](assets/167ea11593f3866b)

你也可以在[这里](https://link.juejin.im/?target=http%3A%2F%2Fciembor.github.io%2F4bit%2F)自己设计一套。

## 6. 使用 alias 强化 Git Bash

alias 是命令的别名，可以把多条命令设置一个简写。在用户目录创建 `.bash_profile` 文件，将 alias 写在此文件中。

下面是我列举的部分 alias，例如键盘敲出 `gitauto` 就能自动提交一次代码、敲出 `e` 然后回车就能退出终端，节省了很多时间。

可以把自己的常用操作放到这里面来可以大大提高效率。

```
$ vim ~/.bash_profile
复制代码
```

```
alias bashalias='code ~/.bash_profile'
alias bashcolor='code ~/.minttyrc'
alias bashconfig='code /etc/profile.d/git-prompt.sh'
alias gitconfig='code ~/.gitconfig'

alias .='cd ~'
alias ..='cd ..'
alias ...='cd ../..'
alias e='exit'
alias cls='clear'

alias gs='git status'
alias ga='git add .'
alias gc='git commit -m'
alias gp='git push'
alias gitauto='git add . && git commit -m "auto deploy" && git push'

alias sysoff='shutdown -s -t 0'
alias sysre='shutdown -r -t 0'

alias host='code /c/Windows/System32/drivers/etc/hosts'
复制代码
```

## 7. 使用 tmux 打造高效终端

tmux 是终端复用神器，可以解决 Git Bash 没有多标签功能的问题

首先感谢 hongwenjun 提取的 [tmux for windows](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fhongwenjun%2Ftmux_for_windows)，让 windows 安装 tmux 变得如此简单，只需要执行以下几条命令即可：

```
$ git clone https://github.com/xnng/bash.git
$ cd bash
$ cp tmux/bin/* /usr/bin
$ cp tmux/share/* /usr/share -r
复制代码
```

创建配置文件支持鼠标拖动窗口分隔线

```
$ vim ~/.tmux.conf
复制代码
```

```
setw -g mouse
set-option -g history-limit 20000
set-option -g mouse on
bind -n WheelUpPane select-pane -t= \; copy-mode -e \; send-keys -M
bind -n WheelDownPane select-pane -t= \; send-keys -M
复制代码
```

如果你还不了解 tmux，建议看下掘金的[这篇文章](https://juejin.im/post/5a8917336fb9a0633e51ddb9)学习下。

我在这里演示下 tmux 的两个重要操作，来看下使用它的好处：

- 首先是使用会话代替多标签功能，只需要几个快捷键就能完成

![img](assets/167ea115a86dc1e8)

- 然后是分屏功能，还支持鼠标自由拖动和点击

![img](https://user-gold-cdn.xitu.io/2018/12/26/167ea1160e5a5f86?imageslim)

在 tmux 中,先按下`shift`后,鼠标的所有操作都正常使用.

* 选择: `Shift` +`光标选择`
* 复制:`ctrl`+`c`
* 粘贴:`shift`+`鼠标中键`
* 功能对话框:`shift`+`鼠标右键`



## 8. 拓展命令

Git Bash 有时可能没有自己需要的某些命令，比如 `tree` 和 `wget`。

我的建议是，如果能找到这些命令的 windows 版，就尽量找一下。把命令的 exe 文件放到 `/usr/bin` 目录下即可。

可以通过下面的方式快速安装我找的这两个命令

```
$ git clone https://github.com/xnng/bash.git
$ cd bash
$ cp tools/* /usr/bin
复制代码
```

如果没找到某些命令的 windows 版怎么办？我不建议安装一个包管理器，例如 `Chocolatey`，它在国内的网络环境上并不好用，反而徒增烦恼。

微软推出的 Linux 子系统 WSL 是一个很好的选择。这里就不介绍怎么安装 WSL 了。

拥有了 WSL 就相当于拥有了一个 Linux 操作系统的包管理器，间接的就相当于拥有了一切 Linux 命令。

执行以下命令可以进入到 WSL：

```
$ winpty wsl
复制代码
```

![img](https://user-gold-cdn.xitu.io/2018/12/26/167ea115b711b9ea?imageslim)

我个人认为 WSL 适合用来拓展 `Git Bash` 的命令，但不适合用来做为开发环境使用，性能跟不上是一方面，坑多又是另一方面。



####  查询所有可用命令:compgen

- `compgen -c` 将列出你可以运行的所有命令。
- `compgen -a` 将列出你可以运行的所有别名。
- `compgen -b` 将列出你可以运行的所有 built-ins 。
- `compgen -k` 将列出你可以运行的所有关键字。
- `compgen -A function` 将列出你可以运行的所有函数。
- `compgen -A function -abck` 将逐一列出上所有内容。

## 9. 在开发工具中使用 Git Bash

- 在 VSCode 中使用

  在 `settings.json` 中添加如下配置，其中 `bash.exe` 的路径 要改成自己的。

  ```
  {
    "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
    "terminal.integrated.shellArgs.windows": ["--login", "-i"],
  }
  复制代码
  ```

  效果如下：

![img](https://user-gold-cdn.xitu.io/2018/12/26/167ea118e5886945?imageslim)

- 如果你用的是 JetBrain 家的软件，那也很简单，在设置里把 shell 的路径改一下就可以了。

![img](https://user-gold-cdn.xitu.io/2018/12/26/167ea117e01681c4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

其它的就不一一列举了。