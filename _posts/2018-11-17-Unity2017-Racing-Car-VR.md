---
layout: post
title: "Unity 2017 - Racing Car VR"
date: 2018-11-17
excerpt: "Giic AR/VR Hackerway in Hansung University"
tags: [Unity, C#, AR, VR, Game]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/Unity"><b>this unity games</b></a> in github.</center>


## Game Design
* Nature Park
* Light
* Car
* VR Cam


## Nature Park
* Nature Park
  - Nature Park in Asset


## Light
* Directional Light
  - Position: (0, 3, 0)


## Car
* Rigidbody
  - Freeze Rotation: X, Z
* `Move_craft.cs`
{% highlight java %}
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;

  public class move_craft : MonoBehaviour {

	  void Start () {	}
	
	  void Update () {
      float xrot_rate = 120 * Time.deltaTime; //회전
      float zmoverate = 5 * Time.deltaTime;
      float dirx = Input.GetAxis("Horizontal"); // 키입력을받는다.
      float dirz = Input.GetAxis("Vertical");
      
      this.transform.Translate(Vector3.forward * zmoverate * dirz);
      this.transform.Rotate(Vector3.up*dirx*xrot_rate); 
	  }

    void OnCollisionEnter(Collision other){
      print("hit with "+other.gameObject.name);
      Debug.Log("avoid "+ other.gameObject.tag);
      if (other.gameObject.tag == "obstacle")
        Destroy(other.gameObject);
    }

    void OnTriggerEnter(Collider other){
      print("Pass");
    }
  }
{% endhighlight %}


## VR Cam
* Empty Object
* `Smooth Fallow.cs`
{% highlight java %}
  using UnityEngine;

  namespace UnityStandardAssets.Utility{
	  
    public class SmoothFollow : MonoBehaviour{

  		[SerializeField]
  		private Transform target;

	  	[SerializeField]
		  private float distance = 10.0f;

	  	[SerializeField]
		  private float height = 5.0f;

  		[SerializeField]
	  	private float rotationDamping;

		  [SerializeField]
  		private float heightDamping;

	  	void Start() { }

		  void LateUpdate(){
			  if (!target)
				  return;

			  var wantedRotationAngle = target.eulerAngles.y;
			  var wantedHeight = target.position.y + height;
			  var currentRotationAngle = transform.eulerAngles.y;
			  var currentHeight = transform.position.y;

			  currentRotationAngle = Mathf.LerpAngle(currentRotationAngle, wantedRotationAngle, rotationDamping * Time.deltaTime);
			  currentHeight = Mathf.Lerp(currentHeight, wantedHeight, heightDamping * Time.deltaTime);

			  var currentRotation = Quaternion.Euler(0, currentRotationAngle, 0);

			  transform.position = target.position;
			  transform.position -= currentRotation * Vector3.forward * distance;

			  transform.position = new Vector3(transform.position.x ,currentHeight , transform.position.z);

			  transform.LookAt(target);
		  }
	  }
  }
{% endhighlight %}
  - Target: Car
  - Distance: 3
  - Rotation Damping: 10
  - Height Damping: 10
* Left Camera
  - Put under VR Cam
  - Position: (-0.2, 0, 0)
* Right Camera
  - Put under VR Cam
  - Position: (0.2, 0, 0)


## UI
<figure class="half">
  <a href="/assets/img/posts/unity_racingcarvr/racingcarvr1.jpg"><img src="/assets/img/posts/unity_racingcarvr/racingcarvr1.jpg"></a>
  <a href="/assets/img/posts/unity_racingcarvr/racingcarvr2.jpg"><img src="/assets/img/posts/unity_racingcarvr/racingcarvr2.jpg"></a>
	<figcaption>Racing Car VR</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/racing_car_vr/Unity-Racing-Car-VR.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
