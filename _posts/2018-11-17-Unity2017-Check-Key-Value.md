---
layout: post
title: "[Unity 2017] Check Key Value"
date: 2018-11-17
excerpt: "Giic AR/VR Hackerway in Hansung University"
tags: [Unity, C#, AR, VR, Game]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/Unity"><b>this unity games</b></a> in github.</center>


In Joy stick, we don't know what is key value of buttons sometimes. This is to check which key value.


## Design
* Camera
* Light
* Canvas
* Event System
* Key Logic


## Camera
* Main Camera
  - Position: (0, 1, -10)


## Light
* Directional Light
  - Position: (0, 3, 0)
  - Rotation: (50, -30, 0)


## Canvas
* Image
  - To paint Canvas
* Text1
  - Text: "Input value: "
* Text2
  - To show key value


## Event System
* Event System
  - To detect input


## Key Logic
* Empty Object
* `Key Text.cs`
{% highlight java %}
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;

  using UnityEngine.UI;
  public class KeyTest : MonoBehaviour {

    public Text keytext;
    
    void Start () {
      keytext.text = "";
	  }
	
	  void Update () {
	    foreach(KeyCode key in System.Enum.GetValues(typeof(KeyCode))){
        if (Input.GetKeyUp(key)){
          print(key.ToString());
          keytext.text = key.ToString();
        }
      }	
	  }
  }
{% endhighlight %}
  - Keytext: Text2


## UI
<figure class="half">
  <a href="/assets/img/posts/unity_checkkeyvalue/checkkeyvalue1.jpg"><img src="/assets/img/posts/unity_checkkeyvalue/checkkeyvalue1.jpg"></a>
  <a href="/assets/img/posts/unity_checkkeyvalue/checkkeyvalue2.jpg"><img src="/assets/img/posts/unity_checkkeyvalue/checkkeyvalue2.jpg"></a>
	<figcaption>Check Key Value</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/check_key_value/Unity-Check-Key-Value.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
