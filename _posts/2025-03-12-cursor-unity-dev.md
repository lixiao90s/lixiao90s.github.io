---
title: 使用 Cursor 作为 Unity 开发环境
date: 2025-03-12 13:00:00 +0800
categories:
- unity
tags:
- unity
- cursor
- 开发工具
- 调试
author: lixiao
layout: post
toc: true
---

* TOC
{:toc}

# 使用 Cursor 作为 Unity 开发环境

## 一、环境配置

### 1.1 前期准备
- Unity 项目已创建
- Cursor IDE 已安装
- Unity 编辑器已配置外部编辑器为 Cursor
- 科学上网

### 1.2 配置步骤
1. 在 Unity 中设置外部编辑器
   - 在包管理器中 粘贴 https://github.com/boxqkrtm/com.unity.ide.cursor.git
   - 进入 Edit > Preferences > External Tools
   - 选择 External Script Editor 为 Cursor 并单击 Regenerate project files。
   -    ![cursor]({{ "/assets/images/unity/cursor_2.png" | relative_url }})

2. 配置 Cursor 的 Unity 开发环境
   - 安装 C# 扩展
   - 安装 Unity 插件
   - 配置调试环境

### 1.3 调试
   - 在unity 中 选择 Assets-> open c# project
   - 在cursor 中 ctrl+shift+p 输入 attach，选择对应的unity进程
   -  ![cursor]({{ "/assets/images/unity/cursor_4.png" | relative_url }})