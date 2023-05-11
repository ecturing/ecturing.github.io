---
title: Visual Studio Code C/C++
date: 2021-12-03 11:20:09
categories:
- 软件配置
tags:
- C/C++
- VSCode
---
# Visual Studio Code C/CPP配置指南

## 一.准备工作

## 通用

打开Visual Studio Code插件市场，搜索C/C++ Extension Pack，进行安装。

### 可选工作

> 敬告：在VSCode中启用Clangd必须强制关闭C/C++ 的智能感知，同时C/C++ 文件右上角将不会有一键生成配置文件的齿轮按钮，在启用clangd之前请先使用C/C++插件的智能感知生成配置文件，随后禁用C/C++智能感知，记住基础快捷键Ctrl+F5直接生成，F5启用调试用于日常运行。若涉及多文件编译需要可启用Cmake，可与Clangd配合使用，需要自行配置。

Visual Studio Code 的C/C++ 扩展已经为Visual Studio Code 提供了完整一套工具库，但是C/C++ 扩展占用内存大，静态检查有可见延时等缺点。可引入clangd增强VSCode的静态检查和补全体验。

> Clangd官网介绍：
> 即clangd是由llvm团队开发的一个针对C/C++的LSP服务(Language Server Protocol,语言服务器协议)，相当于以独立进程为类似VSCode，Sublime Text，Vim等编辑器提供静态语法分析，错误诊断，转定义引用等功能。

在VSCode中搜索Clangd插件，Windows端点击安装clangd插件后会自动检查Clangd环境变量，若未安装，可点击Clangd插件的弹窗Install进行安装。

#### Linux端

若提示未安装，则输入以下命令。

```bash
sudo pacman -Sy clang
```

同时点击Clangd插件弹窗的禁用C/C++ Intelli Sence Engine，这时Clangd会接管C/C++自带的LSP服务，对代码进行分析补全，此时可以获取到更好的体验。

#### Windows端

请先配置好MinGW编译链，然后于LLVM官网下载最新版LLVM工具，并勾选安装时添加到环境变量，在windows控制台输入命令检查是否安装成功

```bash
clang -v
```

安装成功后，点击VSCode的clangd插件，点击插件设置，在clangd fallback flags中添加下列语句

```bash
--target=x86_64-pc-mingw64
```

意义为指定生成目标为mingw64类型，clangd会自动检测环境变量中的MinGW安装目录。

## Windows

### 安装MinGW编译器

在官网上下载MinGW编译器，并进行环境变量的配置。

win键+s 搜索环境变量，点击系统变量，PATH，编辑，点击新建，然后点击浏览选择MinGW安装路径(选择到bin目录处)

打开终端输入

```bash
gcc -v
```

如果返回版本信息，则安装成功，若未显示，检查安装步骤是否有误

## Linux(以Manjaro Linux为例子)

​	在绝大多数Linux发行版中默认携带了gcc，在终端驶入以下命令验证是否拥有gcc

```bash
gcc -v
```

若有返回版本信息，则说明已经安装，若未显示，则进行安装

```bash
sudo pacman -Sy gcc
```

再次重复以上步骤验证是否安装成功

## 二.Visual Studio Code配置工作

> 切记在配置运行环境时，请始终保持Visual Studio Code将C/CPP源文件在编辑器里处于编辑状态，这样Visual Studio Code会根据打开的源文件类型智能推荐配置工作和配置文件中编译程序路径的自动补全。

### 1.创建C/CPP源文件

创建任意C/CPP源文件，并键入HelloWorld代码

### 2.在已经安装好C/Cpp扩展的情况下

点击编辑器右上角的齿轮，根据文件类型选择合适编译链，如C语言采用Gcc,Cpp采用g++。此时VSCode会自动生成好配置文件。
