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
* ImageTarget
* Basket Game



## Lgith
* Directional Light
- Position: (0, 3, 0)
- Rotation: (50, -30, 0)


## Event System
* EventSystem
  - To detect input

## Game UI(Canvas)
* Image
* Button
  - Event Trigger
  - Add `Logic` in Event Trigger
  - Text
    - Put under `Button`
    - Text: "Button"
* Score(Text)
  - Text: 0
* Text
  - Text: "Score : "


## Logic
* `Basket Control.cs`
{% highlight java %}
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;
  using UnityEngine.UI;
  using UnityEngine.SceneManagement;

  public class BasketControl : MonoBehaviour {
    public Text my_score;
    GameObject A;
    Transform B;

    static public int gamescore = 0;
    
    public GameObject basketBall;
    public Transform firepoint;
    Rigidbody ballrigid;

    public void ShootBall(){
        ballrigid.useGravity = true;
        ballrigid.AddForce(Vector3.up * 600f);
        ballrigid.AddForce(Vector3.forward *500f);
    }

  	void Start () {
      ballrigid = basketBall.GetComponent<Rigidbody>();
    }
	
  	void Update () {
      my_score.text = gamescore.ToString();
      if (basketBall.transform.position.y <= -15f){
        initball();
      }
    }

    void initball(){
      basketBall.transform.position = firepoint.transform.position;
      basketBall.transform.eulerAngles = Vector3.zero;
      ballrigid.velocity = Vector3.zero;
      ballrigid.angularVelocity = Vector3.zero;
      ballrigid.useGravity = false;
    }
  }
{% endhighlight %}
  - My_score: `score`
  - Basket Ball: `BasketBall`
  - Firepoint: `FirePoint`


## AR Camera
* Vuforia
  - Download vuforia and import
  - Open <a hrefr="https://developer.vuforia.com/">Vuforia</a>
  - Register or Login
  - Click Develop Menu
  - Create Development Key
  - Vuforia Behavior>Open Vuforia configutration
  - Copy and Paste License Key of Vuforia
  <figure class="third">
    <a href="/assets/img/posts/unity_basketball_ar/vuforia1.jpg"><img src="/assets/img/posts/unity_basketball_ar/vuforia1.jpg"></a>
    <a href="/assets/img/posts/unity_basketball_ar/vuforia2.jpg"><img src="/assets/img/posts/unity_basketball_ar/vuforia2.jpg"></a>
    <a href="/assets/img/posts/unity_basketball_ar/vuforia3.jpg"><img src="/assets/img/posts/unity_basketball_ar/vuforia3.jpg"></a>
	  <figcaption>Basket Ball AR Vuforia</figcaption>
  </figure>


## Image Target
* ImageTarget
  - Asset>Vuforia>Prefabs>ImageTarget
  - Drag and drop to Hierarchy
  - In <a hrefr="https://developer.vuforia.com/">Vuforia</a>, Develop>Target Manager>Add Database
  - Create Database
  - Develop>Target Manager>Databse Name>Add Target
  - Create target
  - Develop>Target Manager>Database Name>Download Database
  <figure class="half">
    <a href="/assets/img/posts/unity_basketball_ar/imagetarget1.jpg"><img src="/assets/img/posts/unity_basketball_ar/imagetarget1.jpg"></a>
    <a href="/assets/img/posts/unity_basketball_ar/imagetarget2.jpg"><img src="/assets/img/posts/unity_basketball_ar/imagetarget2.jpg"></a>
    <a href="/assets/img/posts/unity_basketball_ar/imagetarget3.jpg"><img src="/assets/img/posts/unity_basketball_ar/imagetarget3.jpg"></a>
	  <figcaption>Basket Ball AR Image Target</figcaption>
  </figure>
  - Import Target and drag and drop to Image Target
* Set Target Behaviour
  - Image Target>Inspector>Image Target Behaviour
  - Change Database and Image Target
  <figure>
    <a href="/assets/img/posts/unity_basketball_ar/imagetarget4.jpg"><img src="/assets/img/posts/unity_basketball_ar/imagetarget4.jpg"></a>
	  <figcaption>Basket Ball AR Image Target</figcaption>
  </figure>


## Basket Game
* Stand
  - Cube
* BasketBall
  - Sphere
  - Physic Material<br>
    Dynamic Friction: 0.6<br>
    Static Friction: 0.6<br>
    Bounciness: 0.6<br>
    Friction Combine: Maximum
  - Rigidbody<br>
    Use Gravity: Check
  - `Ball Control.cs`
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
* FirePoint
  - Empty Object
* basket
  - Import Asset
* score_box
  - Cube
  - Is Trigger: Check
  - Mesh Renderer: Uncheck
  

## UI
<figure class="half">
  <a href="/assets/img/posts/unity_basketball_ar/basketballar1.jpg"><img src="/assets/img/posts/unity_basketball_ar/basketballar1.jpg"></a>
  <a href="/assets/img/posts/unity_basketball_ar/basketballar2.jpg"><img src="/assets/img/posts/unity_basketball_ar/basketballar2.jpg"></a>
	<figcaption>Basket Ball AR</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/basket_ball_ar/Unity-Basket-Ball-AR.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
