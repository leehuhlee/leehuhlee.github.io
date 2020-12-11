---
layout: post
title: "Unity4 - Coin Crazy"
date: 2018-08-21
excerpt: "Character Action Game"
tags: [Unity, JavaScript]
comments: false
---

<center>See <a href="https://github.com/leehuhlee/Unity"><b>this unity4 games</b></a> in github.</center>


## Game design

* Player character
* Stage
* Coin
* Obstacle
* Generator for coin and obstacle
* Game Controller


## Create stage
  - Create Cube and change Scale to (100, 1, 100)
  - Change Position to (0, -0.5, 0)
  - Add texture
  - Create Cube and change Position to (0, 0.5, 0)

* Create Directional Lights
  - Create Directional Light
  - Change Position to (0, 0, 0) and Rotation to (90, 0, 0)
  - Change Intensity to 0.5
  - Create Directional Light
  - Change Position to (0, 0, 0) and Rotation to (-90, 0, 0)
  - Change Intensity to 0.2

* Create Physic Material
  - Create folder and change Name to `Physic Material`
  - Create Physic Material in the folder and change Name to `Default Physic Material`
  <figure>
	  <a href="/assets/img/posts/unity_coincrazy/stage1.png"><img src="/assets/img/posts/unity_coincrazy/stage1.png"></a>
	  <figcaption>Coin Crazy Stage</figcaption>
  </figure>
  - Change Dynamic Friction to 0.2, Static Friction to 0.2 and Bounciness to 0
  <figure>
	  <a href="/assets/img/posts/unity_coincrazy/stage2.png"><img src="/assets/img/posts/unity_coincrazy/stage2.png"></a>
	  <figcaption>Coin Crazy Stage</figcaption>
  </figure>
  - Edit>Project Settings>Physics
  - Open Physics Manager in Inspector View
  - Drag and drop to `Default Physic Material` in Physics Manager
  <figure>
	  <a href="/assets/img/posts/unity_coincrazy/stage3.png"><img src="/assets/img/posts/unity_coincrazy/stage3.png"></a>
	  <figcaption>Coin Crazy Stage</figcaption>
  </figure>

* Set Camera
  - Change camera Position to (0, 7, -10), Rotation to (30, 0, 0) and Field of View to 30
  - Change Near to 0.3 and Far to 50 in Clipping Planes

* Organize name and hierarchy
  <figure>
	  <a href="/assets/img/posts/unity_coincrazy/stage4.png"><img src="/assets/img/posts/unity_coincrazy/stage34.png"></a>
	  <figcaption>Coin Crazy Stage</figcaption>
  </figure>


## Use character

* Import file
  - Download `Character Pack: Free Samp` in Asset Store
  <figure>
	  <a href="/assets/img/posts/unity_coincrazy/character1.png"><img src="/assets/img/posts/unity_coincrazy/character1.png"></a>
	  <figcaption>Coin Crazy Character</figcaption>
  </figure>

* Deploy character
  - Drag and drop character file in Project View to Hierarchy View
  <figure class="half">
	  <a href="/assets/img/posts/unity_coincrazy/character2.png"><img src="/assets/img/posts/unity_coincrazy/character2.png"></a>
      <a href="/assets/img/posts/unity_coincrazy/character3.png"><img src="/assets/img/posts/unity_coincrazy/character3.png"></a>
	  <figcaption>Coin Crazy Character</figcaption>
  </figure>
  - Change Rotation Y to 180
  <figure>
	  <a href="/assets/img/posts/unity_coincrazy/character4.png"><img src="/assets/img/posts/unity_coincrazy/character4.png"></a>
	  <figcaption>Coin Crazy Character</figcaption>
  </figure>




[Download Game](https://github.com/leehuhlee/Unity){: .btn}
