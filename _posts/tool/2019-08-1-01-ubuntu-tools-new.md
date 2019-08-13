---
layout: post
title: Ubuntu工具配置手册
category: 工具
tags: Ubuntu
description: Ubuntu
---

最近又配置了一台Ubuntu的开发环境，真的折(tong)腾(ku)，给未来铺下路。

## ss

```
https://github.com/shadowsocks/shadowsocks-qt5/releases
```

装好之后先开全局，然后装chrome，登录google同步，这是最快最省事的。

## Terminal

terminal 的基本快捷键全部改为和os x保持一致，防止切换系统，经常按错。
这里还有个取巧的方法是将键盘上win(command)键 与 alt键 换个位置，这样就可以完全保持一致了。

##  增强终端

和mac的配置保持一致

#### Zsh

```
# 安装 Zsh
sudo apt install zsh

# 将 Zsh 设置为默认 Shell
chsh -s /bin/zsh

# 可以通过 echo $SHELL 查看当前默认的 Shell，如果没有改为 /bin/zsh，那么需要重启 Shell。
```

#### oh my zsh
```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
bash ./install.sh
```

#### 主题选择

spaceship theme 

```
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
```

#### 字体

meslo font

```
https://github.com/powerline/fonts/blob/master/Meslo%20Slashed/Meslo%20LG%20M%20Regular%20for%20Powerline.ttf
```

### 插件

#### autojump 快速跳转目录

```
sudo apt install autojump
```

#### fasd 快速访问文件或目录，功能比autojump更强大，但没有j方便。

```
sudo apt install fasd
```

#### fzf 模糊搜索

```
sudo apt install fasdf
```

#### 语法高亮

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

#### 历史命令提示

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

#### zsh个性化配置

修改配置文件：

```
vim ~/.zshrc
```

#### 添加配置


```
ZSH_THEME="spaceship"
plugins=(robbyrussell git zsh-syntax-highlighting autojump zsh-autosuggestions)
alias py3="source /Users/fank/code/py3.7/bin/activate"
```

## 数据库图形工具

MySQL workbench

redis desktop manager

## VScode

配置查看[vscode配置](http://fankcoder.com/%E5%B7%A5%E5%85%B7/2019/03/08/vscode.html)

## Docky 仿osx的快速启动工具

```
sudo apt-get install docky
```

## 状态栏显示系统信息

```
sudo apt-get install indicator-sysmonitor
```

配置让其显示内存占用与实时网速

Preference-Advanced add: mem:{mem} net:{net}

## 配置代理走PAC

使用最新的18.04走系统配置自动加载pac文件，Chrome代理失败，Firefox配置后成功。

解决方法是安装SwitchyOmega插件来增强Chrome代理设置功能