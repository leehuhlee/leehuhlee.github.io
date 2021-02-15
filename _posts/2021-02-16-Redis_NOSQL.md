---
layout: post
title: "[Redis] NOSQL"
date: 2021-02-10
excerpt: "Redis"
tags: [Database, Redis, NOSQL]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part5/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part5: 데이터베이스</a></center>

# Download

## Redis on Window
* Github
  - Download: <a href="https://github.com/tporadowski/redis/releases/tag/v5.0.9">Redis</a>

* Test
  - execute `redis-server.exe`
  - execute `redis-cli.exe`

<figure>
  <a href="/assets/img/posts/redis_nosql/0.jpg"><img src="/assets/img/posts/redis_nosql/0.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

# Value Type

## String
* set [key] [value]
* get [key]
* append

<figure>
  <a href="/assets/img/posts/redis_nosql/1.jpg"><img src="/assets/img/posts/redis_nosql/1.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

* incr
* decr

<figure>
  <a href="/assets/img/posts/redis_nosql/2.jpg"><img src="/assets/img/posts/redis_nosql/2.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

* mset
* mget

<figure>
  <a href="/assets/img/posts/redis_nosql/3.jpg"><img src="/assets/img/posts/redis_nosql/3.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

* override
<figure>
  <a href="/assets/img/posts/redis_nosql/4.jpg"><img src="/assets/img/posts/redis_nosql/4.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

* ttl [key]
* expire [key] [seconds]

<figure class="half">
  <a href="/assets/img/posts/redis_nosql/5.jpg"><img src="/assets/img/posts/redis_nosql/5.jpg"></a>
  <a href="/assets/img/posts/redis_nosql/6.jpg"><img src="/assets/img/posts/redis_nosql/6.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

## Set
* None Duplicated Set
  - sadd
  - sinter
  - srem

<figure>
  <a href="/assets/img/posts/redis_nosql/7.jpg"><img src="/assets/img/posts/redis_nosql/7.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

* Ordered Set
  - zadd
  - zrange
  - zrevrange

 <figure class="third">
  <a href="/assets/img/posts/redis_nosql/8.jpg"><img src="/assets/img/posts/redis_nosql/8.jpg"></a>
  <a href="/assets/img/posts/redis_nosql/9.jpg"><img src="/assets/img/posts/redis_nosql/9.jpg"></a>
  <a href="/assets/img/posts/redis_nosql/10.jpg"><img src="/assets/img/posts/redis_nosql/10.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure> 

## list
* lpush
* rpush
* lrange
* lpop
* rpop

<figure>
  <a href="/assets/img/posts/redis_nosql/11.jpg"><img src="/assets/img/posts/redis_nosql/11.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>

## Hash
* hset
* hget
* hlen
* hdel
* hgetall

<figure>
  <a href="/assets/img/posts/redis_nosql/12.jpg"><img src="/assets/img/posts/redis_nosql/12.jpg"></a>
	<figcaption>Redis NOSQL</figcaption>
</figure>
