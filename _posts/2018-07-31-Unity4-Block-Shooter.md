---
layout: post
title: "Unity4 - Block Shooter"
date: 2018-12-25
excerpt: "Block Shooter"
tags: [Unity, Java]
comments: false
---

<center>See <a href="https://github.com/leehuhlee/Unity"><b>this unity4 games</b></a> in github.</center>

## Maze game design

* Room
* Box
* Ball
* Gun
* Box Generator
* Wall
* Score
* GUI Skin
* Time Limitation
* Start Scene
* Title Scene

## Create room

* Create floor, wall, and ceiling 
  - Create a cube and change name to `Floor`
  - Create three walls and change name to `Wall1`, `Wall2`, and `Wall3`
  - Create a cube and change name to `Ceiling`
  - Add checker texture
  - Decrease `Static Friction` and `Dynamic Friction` and increase `Bounciness`
  <figure>
	  <a href="/assets/img/posts/unity/room1.jpg"><img src="/assets/img/posts/unity_blockshooter/room1.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Deploy a light
  - Create `point light` and change location to (0, 15, 0)
  - Change `Range` to 25 and `Intensity` to 2.0 in Light Component
  <figure>
	  <a href="/assets/img/posts/unity/light1.jpg"><img src="/assets/img/posts/unity_blockshooter/light1.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Build hierarchy structure
  - Create empty object and change location to (0, 0, 0)
  - Input all elements
  <figure>
	  <a href="/assets/img/posts/unity/hierarchy1.jpg"><img src="/assets/img/posts/unity_blockshooter/hierarchy1.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Save scene
  - [build Settings]-[Scenes In Build]
  - Create `Main` scene
  <figure>
	  <a href="/assets/img/posts/unity/scene1.jpg"><img src="/assets/img/posts/unity_blockshooter/scene1.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Deploy camera
  - Set `aspect ratio` to 16:9 in Game View
  - Change `Camera` angle to 75
  <figure class="half">
	  <a href="/assets/img/posts/unity/camera1.jpg"><img src="/assets/img/posts/unity_blockshooter/camera1.jpg"></a>
    <a href="/assets/img/posts/unity/camera2.jpg"><img src="/assets/img/posts/unity_blockshooter/camera2.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>


## Create box

* Create box
  - Create a cube and change scale to 2.5
  - Add `Rigidbody` component
  - Add Physical material and Tag `Box`
  - Copy the `Box`
  - Change name each boxes to `Red Box` and `Blue Box`
  - Create material for each boxes
  - Prefeb each boxes
  - Drag and drop prefebs to each boxes
    <figure class="half">
	  <a href="/assets/img/posts/unity/box1.jpg"><img src="/assets/img/posts/unity_blockshooter/box1.jpg"></a>
    <a href="/assets/img/posts/unity/box2.jpg"><img src="/assets/img/posts/unity_blockshooter/box2.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>


## Create Gun

* Create bullet prepeb
  - Create a Sphere and change scale to 0.5
  - Add `Rigidbody` component
  - Set shader to Self-Illumin>diffuse
  - Prefeb and change the name to `Bullet`
  <figure>
	  <a href="/assets/img/posts/unity/bullet1.jpg"><img src="/assets/img/posts/unity_blockshooter/bullet1.jpg"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* `Gun.js`
  {% highlight java %}
    var bulletPrefeb : GameObject;
    var initialVelocity : float;

    function Update(){
      if(Input.GetButtonDown("Fire1")){
        var bullet : GameObject = Instantiate(bulletPrefeb, transform.position, transform.rotation);
        var direction : Vector3 = transform.forward;
        bullet.rigidbody.velocity = direction * initialVelocity;
      }
    }
  {% endhighlight %}
  - Drag and drop to `Main Camera`
  - Drag and drop `Bullet` Prefeb to bulletPrefeb in Inspector View
  - Change initialVelocity to 50
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v26ddOwsgIgV5wxuPVxY7iP@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>

* `Gun.js`(change)
  {% highlight java %}
    var bulletPrefeb : GameObject;
    var initialVelocity : float;

    function Update(){
      if(Input.GetButtonDown("Fire1")){
        var bullet : GameObject = Instantiate(bulletPrefeb, transform.position, transform.rotation);
        //here
        var screenPoint = Input.mousePosition;
        screenPoint.z = 10.0;
        var worldPoint = camera.ScreenToWorldPoint(screenPoint);
        var direction = (worldPoint - transform.position).normalized;
        //
        bullet.rigidbody.velocity = direction * initialVelocity;
      }
    }
  {% endhighlight %}

* `Bullet.js`
  {% highlight java %}
    function OnCollisionEnter(collisionInfo : Collision){
      if(collisionInfo.gameObject.tag == "BOX"){
        Destroy(collisionInfo.gameObject);
      }
      Destroy(gameObject);
    }
  {% endhighlight %}
  - Drag and drop to `Bullet` prefeb

* Introduce messaging
  - `Bullet.js`(change)
  {% highlight java %}
    function OnCollisionEnter(collisionInfo : Collision){
      if(collisionInfo.gameObject.tag == "BOX"){
        collisionInfo.gameObject.SendMessage("ApplyDamage");
      }
      Destroy(gameObject);
    }
  {% endhighlight %}


[Download maze game](https://github.com/leehuhlee/Unity){: .btn}
