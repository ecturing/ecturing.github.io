---
title: "vcode+stm32cubemx+clangd配置嵌入式环境"
date: 2024-03-16T22:31:48+08:00
---

# 嵌入式环境配置
## stm32cubemx
stm32cubemx在stm官方网站上进行下载安装
## vscode
插件市场搜索“STM32 VS Code Extension”插件进行安装，你会发现左侧插件栏有一个蝴蝶标志，点击安装会连锁安装其他插件。
继续搜索clangd插件，在linux上使用下列命令，然后输入clangd看是否有输出，有输出则安装成功。
```shell
apt install clang
```
## 编译器
编译器选择arm-none-eabi-gcc,在arm官网下载并解压添加到环境变量，使用下列指令测试是否安装成功
```shell
arm-none-eabi-gcc
```
## 其他选项
其实项目本身还会依赖与ninja，cmake，vcpkg。但是vscode插件安装时会携带对应的可执行文件，基本不需要自行安装。（警告：arm设备（arm服务器或基于arm cpu的开发板服务器）谨慎配置环境，vscode插件携带的vcpkg可执行文件默认结构为x86，需要另外自行配置编译vcpkg环境）

## 开发流程
使用stm32cubemx进行嵌入式板基本代码生成，生成代码形式选择stm32cubeIDE格式，然后使用vscode打开生成项目的目录，打开stm32插件，选择import a local project,点击生成代码目录中的.cproject文件进行导入，若导入选项中不存在.cproject，如linux桌面环境，右键点击显示隐藏文件即可导入。然后在项目主目录的CmakeList.txt中，输入以下选项
```cmake
set(DCMAKE_EXPORT_COMPILE_COMMANDS True)