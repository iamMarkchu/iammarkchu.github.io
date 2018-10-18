---
layout: post
title: "linux中使用date输出时间,格式化时间"
---

![](http://qiniu.mcgoldfish.com/image/1/fohDrs8IXGG7JVCXn9V4lNvjo1fIR130XkC4gUvG.jpeg)

在编写shell脚本的时候，经常有使用时间字符串的场景，比如日志按天分割, 日志时间等

## 命令举例

``` bash
vagrant@homestead:~$ date
Thu Oct 18 06:44:20 UTC 2018

vagrant@homestead:~$ date "+%Y-%m-%d"
2018-10-18

vagrant@homestead:~$ date "+%H:%M:%S"
07:14:08

vagrant@homestead:~$ date "+%Y_%m_%d %H:%M:%S" 
2018_10_18 07:14:29

vagrant@homestead:~$ date -d today
Thu Oct 18 07:15:01 UTC 2018
```

