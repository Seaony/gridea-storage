---
title: '🧶 自建 StableDiffusion 流程分享'
date: 2023-04-15 12:34:04
tags: [StableDiffusion,AI]
published: true
hideInList: false
feature: /post-images/zi-jian-stablediffusion-liu-cheng-fen-xiang.png
isTop: false
---

> "The development of full artificial intelligence could spell the end of the human race."   - Stephen Hawking

---

因为 ChatGPT 的原因，最近各种 AI 概念真的很火 XD。

自己手头也正好有一台主机闲置，正好自建一个 StableDiffusion 来玩。

这篇文章可能会持续更新 (如果我有时间)，我会在这篇文章中分享整个过程。

---

## 💭 部署背景

因为平时会玩游戏，所以主机环境为 Windows10。

加上硬盘也不大，懒得去搞 Ubuntu 双系统了。所以目前的想法是直接部署一套 WebUI，

开放给同局域网下的 Mac 使用。这样主机就可以开着不管，所有操作都使用 Mac 完成。

跑完后再去 Windows 下提取跑出来的图片。

> 顺带一提，Windows 和 Mac 文件互通确实是件很头疼的事情...

---

## 🪴 主机配置

- CPU: **Intel 12700KF**

- 内存: **32G 双通道 DDR4**

- 显卡：**RTX 3080 10G DDR6X**

- ... (更多的就懒得写了，和 SD 基本也没太多关系)

> 如果显卡显存低于 4G 可能会比较吃力 🫠，建议使用 [Google Colab](https://colab.research.google.com/) 这类云服务来跑渲染。

---

## 🍻 部署 StableDiffusion

因为我真的很讨厌 Windows 下的编程环境，一点都不想在 Windows 下折腾。

所以我直接用了比较成熟的 WebUI： [AUTOMATIC1111/stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui)。

当然，如果喜欢折腾的话也可以自己去 Clone [源码](https://github.com/Stability-AI/stablediffusion)编译用 Shell 跑。

整个安装过程还是比较简单的，跟着 README 走即可。

1. 安装 Python 和 Git. (Python 要记得加 Path)。

2. Clone WebUI 到本地 `git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git`

3. 打开 Clone 下来的项目文件夹，双击 `webui-user.bat` 启动。

第一次运行需要安装对应的依赖和库，一般需要等 20 ~ 30 分钟。同时，你需要科学上网。

等待安装完成启动后，访问 `127.0.0.1.7860` 即可使用。

---

## 🍓 常见问题

遇到问题可以先查阅官方提供的 [Wiki](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Troubleshooting)。

### 1. 主机服务开放局域网访问

为了使局域网内的电脑可以访问本机服务，你可以做以下设置:

1. **关闭 Windows 防火墙**
   
   在 `设置` - `Windows 安全中心` - `防火墙和网络保护` 中关闭你的防火墙。
    
    > 当然，这种方法其实是有一定风险的，你也可以使用 [添加入站规则](https://www.jianshu.com/p/642be9f0a597)的方式


2. **修改 StableDiffusionWebUI 启动参数**
   
   使用文本编辑工具打开 WebUI 目录下的 `webui-user.bat`，添加如下参数：
   
   ```
   set COMMANDLINE_ARGS=--listen --share --no-gradio-queue --enable-insecure-extension-access
   ```
   
   > 前两个参数是为了局域网可以正常访问服务，后两个参数解决非本机无法安装拓展的问题。



### 2. WebUI 用户界面的汉化

虽然全是名词，但是汉化成中文还是用起来会方便一些。

直接安装这个 Extension 即可完成汉化：[VinsonLaro/stable-diffusion-webui-chinese](https://github.com/VinsonLaro/stable-diffusion-webui-chinese)

> 顺带一提，使用 Chinese-English 会更好一些，有些名词翻译后还是比较迷惑的。






