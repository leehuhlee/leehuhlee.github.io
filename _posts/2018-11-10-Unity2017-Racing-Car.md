---
layout: post
title: "Unity 2017 - Racing Car"
date: 2018-11-10
excerpt: "Giic AR/VR Hackerway in Hansung University"
tags: [Unity, JavaScript, AR, VR, Game]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/Unity"><b>this unity games</b></a> in github.</center>


## Game Design
* Light
* Craft
* Stage
* Car
* Wall
* Barrier
* Obstacle
* Pass


## Light
* Directional Light
  - Position: (0, 3, 0)
  - Rotation: (50, -30, 0)


## Craft
* Empty Game Object
* `Move_craft.js`
{% highlight java %}
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;

  public class move_craft : MonoBehaviour {

	  void Start () {
	  }
	
	  void Update () {
      float xrot_rate = 120 * Time.deltaTime;
      float zmoverate = 5 * Time.deltaTime;
      float dirx = Input.GetAxis("Horizontal");
      float dirz = Input.GetAxis("Vertical");

      this.transform.Translate(Vector3.forward * zmoverate * dirz);
      this.transform.Rotate(Vector3.up*dirx*xrot_rate);
	  }

    void OnCollisionEnter(Collision other){
      print("hit with "+other.gameObject.name);
      Debug.Log("avoid "+ other.gameObject.tag);
      if (other.gameObject.name == "Pass")
        print("Game End");
    }

    void OnTriggerEnter(Collider other){
      print("Pass");
    }
  }
{% endhighlight %}


## Stage
* Plane
  - Position: (0, 0, 0)
  - Scale:(5, 5, 5)
  - Color: black


## Car
* Asset
  - CartoonCarFree
* Physic Material
  - Name: ttb
  - Dynamic Friction: 0.6
  - Static Friction: 0.6
  - Bounciness: 0.9
* Rigidbody
  - Freeze Position: Y
  - Freeze Rotation: Y
* Camera
  - Put under Car in Hierarchy to follow car
  - Field of View: 60
  - Near: 0.3
  - Far: 1000


## Wall
* Cube
  - Position: (25, 0.5, 0), (-25, 0.5, 0), (0, 0.5, 25), (0, 0.5 -25)
  - Scale: (1, 1, 50), (1, 1, 50), (50, 1, 1), (50, 1, 1)


## Barrier
* Cube
  - Color: white
* Cube for `Pass`
  - Color: red


## Obstacle
* Sphere
* Physic Material
  - Name: ttb


## Pass
* Cube
* Is Trigger


## UI
<figure class="half">
  <a href="/assets/img/posts/unity_racingcar/racingcar1.jpg"><img src="/assets/img/posts/unity_racingcar/racingcar1.jpg"></a>
  <a href="/assets/img/posts/unity_racingcar/racingcar2.jpg"><img src="/assets/img/posts/unity_racingcar/racingcar2.jpg"></a>
	<figcaption>Racing Car</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/racing_car/Unity-Racing-Car.mp4" frameborder="0"> </iframe>

[Download Game](https://github.com/leehuhlee/Unity){: .btn}
