---
layout: post
title:  "TCP/IP详解卷一阅读笔记"
date:   2018-08-27 09:36:54 +0800
categories: tcpip
---

# TCP/IP详解卷一阅读笔记

> 原想深入了解一下tcp/ip的原理，于是翻开了《TCP/IP详解 卷一：协议》，陆陆续续粗糙地看了两个月终于看完了，发现基础的原理还是理解不到位，但是仅仅对TCP/IP协议簇有一定的了解也是很大的收获了

这里对了解到的协议概念做一下索引

## 一、网络分层
- 链路层：设备驱动程序及接口卡
- 网络层：IP、ICMP和IGMP
- 运输层：TCP和UDP
- 应用层：Telnet、FTP和email等

## 二、IP
网际协议

## 三、一些其它协议
### 网路层
- ARP 地址解析协议
- RARP 逆地址解析协议
- ICMP Internet控制报文协议
- IGMP Internet组管理协议
- DNS 域名系统
- RIP 选路信息协议 动态选路协议的一种

### 运输层
- TFTP 简单文件传送协议
- BOOTP 引导程序协议 DHCP前身

### 应用层
- FTP 文件传送协议
- SNMP 简单网络管理协议
- SMTP 简单邮件传送协议
- HTTP 超文本传输协议

## 四、UDP
用户数据报协议

## 五、TCP
传输控制协议

- 连接的建立与终止
- 交互数据流
- 成块数据流
- 超时与重传
- 坚持定时器
- 保活定时器
- 未来与性能

## 六、一些指令和程序
很多程序其实也是协议的实现，比如telnet和rlogin都有对应的telnet协议和rlogin协议
但通常是针对具体目的，具有实用性而通用性不足，这里列举为程序
- ping
- traceroute
- ifconfig
- netstat
- tcpdump
- telnet
- rlogin
- nfs