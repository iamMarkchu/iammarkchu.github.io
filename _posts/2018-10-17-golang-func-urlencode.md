---
layout: post
title: "GO语言中的urlencode和urldecode"
categories: ["golang", "编程语言"]
tags: ["go语言学习笔记"]
---

![golang](http://qiniu.mcgoldfish.com/image/1/Dyybtuf1QfrQDl92Cc7Ww88WixAel5JunOOzgoNW.jpeg)
在php中有urlcode()，urldecode()来处理url中的编码转换，go语言中有没有相同功能的函数呢?

## net/url包介绍
go标准包中有`net/url`包来处理解析url，query转义。

### url.QueryEscape() 
`url.QueryEscape()`方法跟php中urlencode()函数相同, `url.QueryUnescape()`方法跟php中的urldecode相同

``` golang
package main

import (
	"net/url"
	"fmt"
)

func main() {
	str := "https://www.domain.com"
	u, _ := url.Parse(str)
	q := u.Query()

	str2 := url.QueryEscape("https://www.baidu.com")
	fmt.Println("QueryEscape:", str2)
	q.Add("redirect_uri", str2)
	u.RawQuery = q.Encode()
	fmt.Println("url为:", u.String())
	str3, _ := url.QueryUnescape(str2)
	fmt.Println("QueryUnescape:", str3)
}
```

输出:

```
QueryEscape: https%3A%2F%2Fwww.baidu.com
url为: https://www.domain.com?redirect_uri=https%253A%252F%252Fwww.baidu.com
QueryUnescape: https://www.baidu.com
```