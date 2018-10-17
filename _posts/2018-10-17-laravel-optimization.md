---
layout: post
title: "laravel优化"
categories: ["laravel", "php"]
tags: ["系统优化"]
---

![laravel](http://qiniu.mcgoldfish.com/image/1/kwgALnRPA6RZDTqtSUp88fRiQkWjcMMVb8peV4Y3.png)
在使用laravel时，没有人不被它优雅的语法以及高效率的开发所折服，但是它框架重，性能比较一般也是为人所诟病的点，我在实际项目中，前期开发程序，美滋滋，可突然有一天，流量突增，导致cpu，内存压力很大，php-fpm进程处理不过来，nginx返回499以及502等状态码

## 典型场景
  一个典型的laravel应用流程是，请求到达负载均衡，分发到nginx，nginx pass到php-fpm，laravel框架加载类(核心)，加载配置，路由分发，控制器代码，数据库操作，响应返回
  
## 可以优化的地方

### 增加机器, 增加单台机器的配置
物理方法，也是最直接，最有效，改动不大的操作。缺点就是金钱成本的增加，运维成本的增加

### php-fpm参数优化

### 开启Opcache
  开启Opcache也是改动比较小，提升性能效果比较显著的操作。 据说php7可以提升 50%之多，什么也不用干.

### laravel 优化加载类
  其实是composer加载类的优化.
  执行命令 `composer auto-dump`

### laravel 配置缓存

执行命令 `php artisan config:cache`
**注意** : 配置缓存是有限制的，如果执行了配置缓存命令，在config目录以外使用env()返回的结果都是null,所以在执行命令前，请先将env()函数改为config()调用

### laravel 路由缓存

执行命令 `php artisan route:cache`
**注意** : 路由缓存不支持有重复的路由定义，不支持闭包路由写法，所以使用路由缓存之前，所有的路由必须指向一个控制器，请先修改路由的写法。

官方文档在这里用这么一句话来说明:
> If your application is exclusively using controller based routes, you should take advantage of Laravel's route cache, Closure based routes cannot be cached. To use route caching, you must convert any Closure routes to controller classes.
出自[官方文档](https://laravel.com/docs/5.7/controllers#route-caching)

### 数据库查询操作缓存到redis
  比如你需要查询用户的信息，然后每个请求，你的代码都会到数据库查询。

``` php

function getUser($id) {
	$sql = "SELECT * FROM `users` WHERE id = '$id'";
	$result = $dbObject->query($sql);
	return $result;
}
```
  修改为如果取不到缓存，则查询数据库，如果取得到，则返回数据

``` php

function getUser($id) {
	$key = "p:userInfo:$id";
	$result = $redisObject->get($key);
	if (!$result) {
		$sql = "SELECT * FROM `users` WHERE id = '$id'";
		$result = $dbObject->query($sql);
		$result = json_encode($result);
		$redisObject->set($key, $result, 86400);
	}

	return json_decode($result);
}
```
### 耗时操作，改为异步队列执行
 	异步队列，可以快速缩短请求所需要的时间，释放占用的服务器资源，让其他请求可以得到handle。 比如耗时的mysql插入或者更新操作。你可以将需要插入或者更新的数据丢到队列里，然后马上返回响应，然后另外的有队列服务的机器消费队列。