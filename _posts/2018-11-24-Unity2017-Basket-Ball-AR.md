---
layout: post
title: "Unity 2017 - Basket Ball AR"
date: 2018-11-24
excerpt: "Giic AR/VR Hackerway in Hansung University"
tags: [Unity, C#, AR, VR, Game]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/Unity"><b>this unity games</b></a> in github.</center>


## Game Design
* Light
* Event System
* Game UI
* Logic
* AR Camera
* Basket Game
* ImageTarget


## Lgith
* Directional Light
- Position: (0, 3, 0)
- Rotation: (50, -30, 0)


## Event System
* Event System
  - To detect input
* Image
* Button
  - Event Trigger
  - Add `Logic` in Event Trigger
  - Text: "Button"
* Score
  - Text: 0
* Text
  - Text: "Score : "

## Logic
* `Basket Control.cs`
{% highlight java %}
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;

  public class BallControl : MonoBehaviour {

	  void Start () {
      this.gameObject.GetComponent<Rigidbody>().useGravity = false;
	  }

    void OnTriggerEnter(Collider other){
      if (other.gameObject.name == "score_box"){
        BasketControl.gamescore += 1;
        print(BasketControl.gamescore);
      }
    }

    void Update () { }
  }
{% endhighlight %}
  - My_score: `score`
  - Basket Ball: `BasketBall`
  - Firepoint: `FirePoint`



## Game UI
* Canvas



## UI
<figure class="half">
  <a href="/assets/img/posts/unity_basketball_ar/basketballar1.jpg"><img src="/assets/img/posts/unity_basketball_ar/basketballar1.jpg"></a>
  <a href="/assets/img/posts/unity_basketball_ar/basketballar2.jpg"><img src="/assets/img/posts/unity_basketball_ar/basketballar2.jpg"></a>
	<figcaption>Basket Ball AR</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/basket_ball_ar/Unity-Basket-Ball-AR.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
