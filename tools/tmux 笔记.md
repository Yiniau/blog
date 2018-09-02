> ---
>
> title: tmux 笔记
>
> date: 2018-9-2  0:48
>
> categories: tools, 笔记
>
> tags: tools, note, 笔记, 备忘
>
> auther: [Yiniau](https://yiniau.com/)
>
> ---

# tmux 笔记

--- 

## 前言

### 什么是 tmux

官方措辞将tmux描述为屏幕多路复用器，类似于GNU Screen。本质上，这意味着tmux允许你在命令行环境中平铺窗口窗格。这反过来允许您在一个终端内运行或关注多个程序。

tmux的一个常见用例是在远程服务器上，您可以使用常用的布局，并希望有一种快速跳入和跳出的方法。例如，如果您通过跳转服务器进行连接并且希望同时连接其他远程SSH会话。同样，如果您必须跳到Vim，您可以使用tmux在同一个终端窗口中访问您的shell或REPL，以获得类似IDE的体验。

### 内容概述

tmux的安装和基本用法，以帮助启动和运行tmux，同时作为操作备忘录和踩坑记录存在

## 安装

### mac os

1. 使用 `brew` 安装

```bash
brew install tmux
```

### ubuntu / debian linux

1. 使用 `apt-get` 安装

```bash
sudo apt-get update && sudo apt-get install tmux
```

### confirm

```bash
tmux -V
```

## Getting In & Getting Out

tmux基于session。要在tmux中启动新会话，只需在终端中键入 `tmux new` 即可。
一旦你进入tmux，唯一明显不同的是底部永远存在的绿色条（请参阅 *自定义主题* 部分获取定制选项）

![](https://cdn-images-1.medium.com/max/1600/1*cASl09DFzTKKKptbiFs5RQ.png)

to getting out，如果你在一个单一的窗格(panel)中，你可以输入exit，下次回来的时候会从退出的地方开始。

需要注意的是 `exit` 并不是唯一的方式，通常也不是最好的方式。为此我们使用 `detach` 。然而，我们首先必须覆盖前缀...

### 使用前缀

tmux中的所有命令都需要前缀快捷方式，默认情况下为 `ctrl(^) + b` 。我们会经常使用快捷键前缀，所以最好将它提交到内存。输入 `ctrl + b` 后，就进入了快捷键监听模式，可以运行tmux命令，或键入 `:` 以进入命令行模式。

### Attach, Detach & Kill

如上所述，在不退出所有内容的情况下退出session更好的方法是 `detach` session。要执行此操作，首先输入prefix命令，然后输入 `d` 的detach快捷方式：
```
ctrl+b d
```
这将分离当前会话并返回到您的正常shell。

但是，仅仅跳出并不意味着你的session已经关闭。detach的会话仍然可用，并且允许你从中断的地方继续。

要检查哪些会话处于活动状态，可以运行：

```bash
tmux ls
```

tmux会话将在左侧具有与它们相关联的数字（零作为自然意图索引）。此号码可用于附加并返回此同一会话。例如，对于会话编号3，我们将键入：

```bash
tmux attach-session -t 3
or 
tmux a -t 3
```

或者我们可以使用以下命令转到上次创建的会话：

```bash
tmux #
```

### 具名的session

现在我们可以依靠session序号了，但如果我们根据用途提供session名称，它会让我们的更愉快。

要使用特定名称开始新session，我们可以执行以下操作：

```bash
tmux new -s 'name here'
```

使用已命名的session，现在当我们执行tmux时，我们会看到session名称。同样，我们可以使用以下名称附加session：

```bash
tmux a -t 'name here'
```

## 管理窗口

在GUI桌面环境中，您有Windows。在tmux中，你有窗格(panes)。与GUI中的窗口一样，这些窗格允许您与多个应用程序进行交互，同样可以打开，关闭，调整大小和移动。

与标准GUI桌面不同，这些窗格是平铺的，主要由tmux快捷方式管理而不是鼠标（尽管可以添加鼠标功能）。要创建新窗格，只需水平或垂直分割屏幕即可。

horizontally 水平：
```
ctrl+b "
```

vertically 垂直：
```
ctrl+b %
```

![tmux水平分割，下方窗格垂直分割](https://cdn-images-1.medium.com/max/1600/1*Pp6sjIL0Xcn14qNjeRJPfA.png)

你可以使用相同的方法进一步拆分窗格。例如，在上面的屏幕截图中，屏幕首先使用 `ctrl+b "` 水平分割，然后使用 `ctrl+b %` 在下部窗格中垂直分割。

要在窗格之间移动，只需使用前缀后跟箭头键：
```
ctrl+b [arrow key]
```

### Resizing Panes

当我们想要调整窗格的大小的时候，就需要先开启命令行

```
ctrl+b :
```

在那里我们可以输入 `resize-pane`，后跟方向标志：`-U` 表示向上， `-D` 表示向下 `-L` 表示左表示， `-R` 表示向右表示。最后一部分是移动它的行数。

例如，如果我们位于顶部窗格中并希望将其向下展开2行，我们将执行以下操作：

```
ctrl+b :
resize-pane -D 2
```

![](https://cdn-images-1.medium.com/max/1600/1*4OdjTvOOPtHbrYADXQxQ0Q.png)

## 定制你的主题吧！

自定义tmux主要通过 `.tmux.conf` 文件完成。

从头开始创建一个自定义主题变得非常耗费时间。因此，最好只使用预制主题作为起点。 Jim Myhrberg的 [tmux-themepack](https://github.com/jimeh/tmux-themepack) 回购中可以找到一个特别好的系列。

只需选择您想要的那个并将配置复制到 `~/.tmux.conf` 中，然后使用 `tmux source-file ~/.tmux.conf` 来源它。

![tmux running Cyan theme with 3-pane layout](https://cdn-images-1.medium.com/max/1600/1*bbDl1iLQNz6CMrkOUd_i_w.png)

## plugin

### installation

Requirements: tmux version 1.9 (or higher), git, bash.

1. Clone TPM

```
$ git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

2. 把它放在 .tmux.conf 的底部:

```
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'git@github.com/user/plugin'
# set -g @plugin 'git@bitbucket.com/user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

3. 重新加载TMUX环境，以便获取TPM:

```
# type this in terminal if tmux is already running
$ tmux source ~/.tmux.conf
```

### 安装plugins

1. 使用 `set -g @plugin '...'` 为 `~/.tmux.conf` 添加新插件
2. 按前缀 + I(大写I，如安装中)来获取插件。

干的好！该插件被克隆到 `~/.tmux/plugins/` 并且sourced。

### 卸载plugins

1. 从列表中删除（或注释掉）插件。
2. 按前缀 + alt + u(卸载时小写u)删除插件。

所有的插件都安装在 `~/.tmux/plugins/` 上，所以你可以在那里找到插件目录并将其删除。

### 键位

`prefix` + I
- 从GitHub或任何其他git存储库安装新插件
- 刷新tmux环境

`prefix` + U
- 更新插件

`prefix` + alt + u
- 删除/卸载不在插件列表上的插件

### 使用 tpm 安装 tmux-themepack

1. 将插件添加到 `.tmux.conf` 中的TPM插件列表中:

```
set -g @plugin 'jimeh/tmux-themepack'
```

2. 点击 `prefix` + I 来获取插件并获取它。该插件就会正常工作。

3. 你可以通过 `.tmux.conf` 选项选择主题：

```
set -g @themepack 'basic' (default)
set -g @themepack 'powerline/block/blue'
set -g @themepack 'powerline/block/cyan'
set -g @themepack 'powerline/default/gray'
set -g @themepack 'powerline/double/magenta'
...
```


## 主要的命令

开始一个新的具名session:
tmux new -s [session name]

Detach session:
ctrl+b d

列出sessions:
tmux ls

Attach到那个具名的session:
tmux a -t [name of session]

kill到目标session:
tmux kill-session -t [name of session]

水平分割窗格:
ctrl+b "

垂直分割窗格:
ctrl+b %

Kill当前的窗格:
ctrl+b x

移动到其他窗格:
ctrl+b [arrow key]

kill掉tmux的server:
tmux kill-server

switch session:
ctrl+b w

## 定制～

### 改变 prefix 键位

```
# remap prefix from 'C-b' to 'C-f'
unbind C-b
set-option -g prefix C-f
bind-key C-f send-prefix
```

### 重设 pane split 快捷方式

C-b % => C-b |
C-b " => C-b -

```
# split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %
```

### 重设 pane close 快捷方式

prifex x => prifex c

```
# split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %
```

### 设置 config reload 快捷方式

```
# reload config file (change file location to your the tmux.conf you want to use)
bind r source-file ~/.tmux.conf
```

### 新增 pane 选择快捷方式

使用tmux时，在窗格之间切换是最常见的任务之一。因此它应该尽可能简单。我不太喜欢一直触发前缀键。我希望能够简单地说 `M-<direction>` 去我想去的地方（记住：M代表Meta，通常是你的 `Alt` 键）。通过这个修改，我只需按 `Alt-left` 转到左侧窗格（和其他方向）, 同时支持vim的上下左右描述（U == k, D == j, L == h, R == l）

```
# switch panes using Alt-arrow without prefix
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

bind -n M-h select-pane -L
bind -n M-l select-pane -R
bind -n M-k select-pane -U
bind -n M-j select-pane -D
```

### 开启鼠标模式

```
# Enable mouse mode (tmux 2.1 and above)
set -g mouse on
```

### 禁止自动命名窗口

```
# don't rename windows automatically
set-option -g allow-rename off
```