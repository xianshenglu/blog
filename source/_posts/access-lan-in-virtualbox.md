---
title: 在 VirtualBox 中访问局域网
tags:
  - VirtualBox
  - LAN
  - 192.168.x.x
  - chinese
categories: software
comments: true
date: 2018-08-18 16:18:15
---

## Preface

工作需要，在 MAC 中开发，调试的时候，就装了 virtualBox 虚拟机来调试 IE，但是开发中经常调试的是局域网的网址，比如 `localhost:8080`，我可以用 `192.168.x.x:8080` 在局域网中另一台设备上访问，但是放到虚拟机中就不行了。

## Main

经搜索，方案如下：

在虚拟系统未打开的情况下,在设置》网络属性中设置两个网络连接：

- 网络连接 1 设置成 NAT，
- 网络连接 2 设置成 Bridged Adapter,名称是 eth0（这个我没找到，但是选的另一个）

这样的话就可以联上互联网同时可以连上局域网。

但是在这种情况下，在虚拟机中输入网址时，遇到 老版的 IE ，需要输入在局域网网址前加 http:// 否则，直接输 192.168.x.x 可能会变成自动搜索。

## Reference

[设置 VirtualBox 虚拟机访问局域网](https://blog.csdn.net/ppby2002/article/details/6455892)
