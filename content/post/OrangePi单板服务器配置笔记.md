---
title: OrangePi单板服务器配置笔记
date: 2022-09-15 14:24:57
categories:
- 服务器运维
tags:
- Ubuntu
---

# Ubuntu-Server配置

## 静态IP设置（可同样配置DNS服务器）

输入指令开启网络可视化配置

```shell
nmtui
```

点击Edit a connection，选择网卡，然后进行静态IP设置

注：配置静态IP一定要遵守局域网IP规则，推荐采用DHCP服务器进行分配IP，在路由器进行mac地址与IP地址进行绑定。

## 配置ZSH

卸载Oh-my-zsh

```shell
uninstall_oh_my_zsh
```

卸载自带ZSH

```shell
apt-get --purge remove zsh
```

重新安装zsh

```shell
apt-get install zsh
```

安装自动补全和指令高亮

```shell
 apt-get install zsh-autosuggestions zsh-syntax-highlighting
```

安装主题插件p10k

```bash
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

配置p10k

```bash
p10k configure
```
启用插件(Ubuntu)

```bash
source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

启用历史记录
	vim写入.zshrc，并新建.zsh_history空文件

```bash
HISTFILE=~/.zsh_history
HISTSIZE=1000
SAVEHIST=1000
```

