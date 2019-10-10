---
layout: post
title: 在mojave深色模式下，禁用chrome的深色主题
category: 工具
tags: mac chrome
keywords: 

---

## 背景

最新的chrome默认跟随mojave系统更改主题模式。

然而chrome的深色主题实在是丑到我了，大部分网页底色都是白色，加上黑色的标题栏，黑色的书签栏，简直酸爽。。

## 禁用方法

1. 打开终端，输入

   osascript -e 'id of app "Google Chrome"'

2. 看到返回**com.google.Chrome** ， 继续输入

   ```
   defaults write com.google.Chrome NSRequiresAquaSystemAppearance -bool yes
   ```

3. 重启chrome

#### 恢复成默认设置

​	defaults delete com.google.Chrome NSRequiresAquaSystemAppearance

