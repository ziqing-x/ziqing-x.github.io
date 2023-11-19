---
title: 使用 figlet 制作 banner
tags: [banner]
category: other
image:
    path: /assets/img/headers/figlet.webp
---

## 一、简介
linux 下使用很多工具时，在执行命令的时候都能输出一个漂亮的banner，如果你也想自己制作一些漂亮的banner，赶紧用 figlet 试试。

## 二、例子
```bash
showfigfonts               > 列出所有banner字体
figlet -f bubble TPTINC    > 生成bubble字体的banner

    _   _   _   _   _   _  
   / \ / \ / \ / \ / \ / \ 
  ( T | P | T | I | N | C )
   \_/ \_/ \_/ \_/ \_/ \_/ 
```