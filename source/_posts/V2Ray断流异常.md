title: V2Ray断流异常
author: Steady
date: 2020-03-15 23:54:38
tags:
---
1. 问题描述
最近一段时间发现，代理十分不稳定，经常出现“断流”，具体表现为：打开需要代理的站点，需要访问两次，第一次访问失败，需要再刷新一次。
查看错误日志内容：

1. Proxy|HTTP: failed to read response from r16---sn-ni57dn7z.gvt1.com > io: read/write on closed pipe

2. 问题原因
后翻阅issue检查发现，我的代理服务器时间比客户端慢了3分钟，两端时间不一致。

3. 解决办法
同步服务器时间

查看当前服务当前时间
1.  date -R
2.  timedatectl set-ntp no
3.  timedatectl set-time '23:51:00'