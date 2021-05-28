---
title: rm特殊删除
date: 2018-10-29 
desc:
keywords: linux
categories: [linux-command]
---

# 特殊删除

## 删除所有-p开头的文件

rm -- -P.* 

需要用--进行特殊处理

## 自定义回收站功能

myrm(){ D=/tmp/$(date +%Y%m%d%H%M%S); mkdir -p $D;  mv "$@" $D && echo "moved to $D ok"; }

alias rm='myrm'
