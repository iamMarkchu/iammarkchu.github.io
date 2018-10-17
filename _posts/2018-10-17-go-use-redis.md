---
layout: post
title: "Go操作Redis"
categories: ["Redis", "Golang"]
tags: ["Redis客户端", "Go包"]
---

![go redis](http://qiniu.mcgoldfish.com/image/1/cDHxhDhK3jB2yWfo02xA3S1v6fNhPsAYuqvQRZre.png)
redis在生成环境中应用非常广泛，它优秀的性能，使构建高并发，高性能的应用变得更加容易。
使用go进行程序开发时，免不了和redis打交道，所以掌握在go中操作redis是很有必要的

## go-redis
	这是一个golang的redis client, [github地址](https://github.com/go-redis/redis)

### 安装
``` golang
go get -u github.com/go-redis/redis

import "github.com/go-redis/redis"  // 在具体代码中引入
```

### 示例代码

``` golang
package main

import (
	"github.com/go-redis/redis"
	"log"
)

func main() {
	client := redis.NewClient(&redis.Options{
		Addr:     "127.0.0.1:6379",
		Password: "",
		DB:       0,
	})

	// set, key,value,expiration
	client.Set("name", "mark", 0).Result()

	// get
	name, err := client.Get("name").Result()
	if err != nil {
		log.Println(err)
	}
	log.Println("name为", name)

	// 年龄增长
	_, err =client.Set("mark:age", 26, 0).Result()
	if err != nil {
		log.Println(err)
	}

	// 自增1
	age, err := client.Incr("mark:age").Result()
	if err != nil {
		log.Println(err)
	}
	log.Println("年龄:", age)

	// 增加指定数
	age, err = client.IncrBy("mark:age", 10).Result()
	log.Println("年龄:", age)
}
```

### 后续

[redis命令参考](https://redis.io/commands)，go-redis基本支持所有的redis命令.