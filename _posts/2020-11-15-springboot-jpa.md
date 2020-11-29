---
layout: post
title: "Java web application v1"
date: 2020-11-15
excerpt: "How to make an online shopping mall with spring boot and JPA "
tags: [web, java, spring, JPA]
comments: false
---

Reference by <a href="https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-%ED%99%9C%EC%9A%A9-1/dashboard">실전! 스프링 부트와 JPA 활용 1 - 웹 애플리케이션 개발</a>

## Settings
* Create project
  - <a href ="https://Start.spring.io/">Spring boot starter</a>
  - dependencies : web, thymeleaf, jpa, h2, lombok, validation, devtools
  <figure>
	<img src="/assets/img/posts/spring/springboot1.jpg">
	<figcaption>Spring boot Setting</figcaption>
  </figure>

* Download lombok plugin

* build.gradle
  - Add spring-boot-devtools(for recompile)
  - Add <a href="https://github.com/gavlyukovskiy/spring-boot-data-source-decorator">P6Spy</a>

* h2 database
  - <a href="https://h2database.com/h2-setup-2019-10-14.exe">Download </a>
  - Go to h2/bin and excute h2-1.4.200.jar(os : window)
  - Connect DB
  <figure class="half">
	<img src="/assets/img/posts/spring/springboot2.jpg">
  <img src="/assets/img/posts/spring/springboot3.jpg">
	<figcaption>H2 Databse Setting</figcaption>
  </figure>

* Domain
  <figure class="third">
	<img src="/assets/img/posts/spring/domainAnalyze1.jpg">
  <img src="/assets/img/posts/spring/domainAnalyze2.jpg">
  <img src="/assets/img/posts/spring/domainAnalyze3.jpg">
	<figcaption>Domain Analyze</figcaption>
  </figure>
  - Connect DB
  <figure>
	<img src="/assets/img/posts/spring/domainChecking.jpg">
	<figcaption>H2 Databse Checking</figcaption>
  </figure>


  
