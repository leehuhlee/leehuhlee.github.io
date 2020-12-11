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
	  <a href="/assets/img/posts/unity/room1.png"><img src="/assets/img/posts/unity_blockshooter/room1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Deploy a light
  - Create `point light` and change location to (0, 15, 0)
  - Change `Range` to 25 and `Intensity` to 2.0 in Light Component
  <figure>
	  <a href="/assets/img/posts/unity/light1.png"><img src="/assets/img/posts/unity_blockshooter/light1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Build hierarchy structure
  - Create empty object and change location to (0, 0, 0)
  - Input all elements
  <figure>
	  <a href="/assets/img/posts/unity/hierarchy1.png"><img src="/assets/img/posts/unity_blockshooter/hierarchy1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Save scene
  - build Settings>Scenes In Build
  - Create `Main` scene
  <figure>
	  <a href="/assets/img/posts/unity/scene1.png"><img src="/assets/img/posts/unity_blockshooter/scene1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Deploy camera
  - Set `aspect ratio` to 16:9 in Game View
  - Change `Camera` angle to 75
  <figure class="half">
	  <a href="/assets/img/posts/unity/camera1.png"><img src="/assets/img/posts/unity_blockshooter/camera1.png"></a>
    <a href="/assets/img/posts/unity/camera2.png"><img src="/assets/img/posts/unity_blockshooter/camera2.png"></a>
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
  - Prefab each boxes
  - Drag and drop prefabs to each boxes
    <figure class="half">
	  <a href="/assets/img/posts/unity/box1.png"><img src="/assets/img/posts/unity_blockshooter/box1.png"></a>
    <a href="/assets/img/posts/unity/box2.png"><img src="/assets/img/posts/unity_blockshooter/box2.png"></a>
	  <figcaption>Block Shooter Box</figcaption>
  </figure>


## Create Gun

* Create bullet prepeb
  - Create a Sphere and change scale to 0.5
  - Add `Rigidbody` component
  - Set shader to Self-Illumin>diffuse
  - Prefab and change the name to `Bullet`
  <figure>
	  <a href="/assets/img/posts/unity/bullet1.png"><img src="/assets/img/posts/unity_blockshooter/bullet1.png"></a>
	  <figcaption>Block Shooter Gun</figcaption>
  </figure>

* `Gun.js`
  {% highlight java %}
    var bulletPrefab : GameObject;
    var initialVelocity : float;

    function Update(){
      if(Input.GetButtonDown("Fire1")){
        var bullet : GameObject = Instantiate(bulletPrefab, transform.position, transform.rotation);
        var direction : Vector3 = transform.forward;
        bullet.rigidbody.velocity = direction * initialVelocity;
      }
    }
  {% endhighlight %}
  - Drag and drop to `Main Camera`
  - Drag and drop `Bullet` Prefab to bulletPrefab in Inspector View
  - Change initialVelocity to 50
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v26ddOwsgIgV5wxuPVxY7iP@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>

* `Gun.js`(change)
  {% highlight java %}
    var bulletPrefab : GameObject;
    var initialVelocity : float;

    function Update(){
      if(Input.GetButtonDown("Fire1")){
        var bullet : GameObject = Instantiate(bulletPrefab, transform.position, transform.rotation);
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
  - Drag and drop to `Bullet` prefab

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
  - `Box.js`
  {% highlight java %}
    function ApplyDamage(){
      Destroy(gameObject);
    }
  {% endhighlight %}
  -  Drag and drop `Box.js` to `Red Box` Prefab and `Blue Box` Prefab

* Improve visual presentation
  - Create empty game object
  - Component>Effects>Legacy Particles
  - Select `Ellipsoid Particle Emitter`, `Particle Animator`, and `Particle Render`
  <figure>
	  <a href="/assets/img/posts/unity/particle1.png"><img src="/assets/img/posts/unity_blockshooter/particle1.png"></a>
	  <figcaption>Block Shooter Gun</figcaption>
  </figure>
  - Set `Ellipsoid Particle Emitter`<br>
    `Min Size`: 3<br>
    `Max Size`: 5<br>
    `Min Energy` and `Max Energy`: 0.5<br>
    `Min Emission` and `Max Emission`: 30<br>
    `x`, `y`, and `z` of `Rnd Velocity`: 25<br>
    `Rnd Angular Velocity`: 360<br>
    `Rnd Rotation`: On<br>
    `One Shot`: On<br>
  - Set `Particle Animator`<br>
    `Damping`: 0.01<br>
    `Autodestruct`: On
  - Create `Explosion` Prefab and drag and drop this particle effect
  - `Box.js`(change)
    {% highlight java %}
      function ApplyDamage(){
        Instantiate(explosionPrefab, transform.position, transform.rotation);
        Destroy(gameObject);
      }
    {% endhighlight %}
  - Drag and drop `Explosion` Prefab to `explosionPrefab` parameter in Inspector View

* Improve to delay of destroy
  - `Box.js`(change)
    {% highlight java %}
      var explosionPrefab : GameObject;
      private var damaged : boolean;
      private var killTimer : float;
      
      function ApplyDamage(){
        if (!damaged){
          damaged = true;
          killTimer = 0.4;
        }
      }

      function Update(){
        if (!damaged) return;
        killTimer -= Time.deltaTime;
        if(killTimer <= 0.0){
          Instantiate(explosionPrefab, transform.position, transform.rotation);
          Destroy(gameObject);
        }
      }
    {% endhighlight %}
  - `Box.js`(change)
    {% highlight java %}
      function ApplyDamage(){
        if (!damaged){
          damaged = true;
          killTimer = 0.4;
          //here
          rigidbody.AddForce(Vector3.up * 15.0, ForceMode.Impulse);
          //
        }
      }
    {% endhighlight %}
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/vd8c0131Eil163r6wlEBlpz@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Box Generator
  - Create empty game object and change name to `Box Generator`

* `BoxGenerator.js`
  {% highlight java %}
      var interval : float;
      var redBoxPrefab : GameObject;
      var blueBoxPrefab : GameObject;

      private var nextIsRed : boolean;
      private var timer : float;

      function Start(){
        nextIsRed = true;
        timer = 0.0;
      }

      function Update(){
        timer -= Time.deltaTime;
        if (timer < 0.0){
          var prefab : Gameobject = nextIsRed ? redBoxPrefab : blueBoxPrefab;
          Instantiate(prefab, transform.position, transform.rotation);
          timer = interval;
          nextIsRed= !nextIsRed;
        }
      }
    {% endhighlight %}
  - Drag and drop `BoxGenerator.js` to `Box Generator` game object
  - Change `Interval` to 0.5 and drag and drop each prefab to `Red Box Prefab` and `Blue Box Prefab` in Inspector View
  - `BoxGenerator.js`(change)
  {% highlight java %}
      function Update(){
        timer -= Time.deltaTime;
        if (timer < 0.0){
          // here
          var offsx : float = Random.Range(-8.0, 8.0);
          var offsz : float = Random.Range(-4.0, 4.0);
          var position : Vector3 = transform.position + Vector3(offsx, 0, offsx);
          //
          var prefab : Gameobject = nextIsRed ? redBoxPrefab : blueBoxPrefab;
          // here
          Instantiate(prefab, position, Random.rotation);
          //
          timer = interval;
          nextIsRed= !nextIsRed;
        }
      }
    {% endhighlight %}
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v93b9nlmUUSz6uSy6Oml6Dm@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Create unvisible wall

* Create univisible wall with normal wall
  - Duplicate wall
  - Locate to front of `room`
  <figure>
	  <a href="/assets/img/posts/unity/room2.png"><img src="/assets/img/posts/unity_blockshooter/room2.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Delete `Renderer` component
  <figure>
	  <a href="/assets/img/posts/unity/room3.png"><img src="/assets/img/posts/unity_blockshooter/room3.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>

* Introduce layer and collision filtering
  - Edit>Project Setting>Tags
  - Open `Tag Manager` in Inspector View
  - Set `Bullet` in `User Layer 8` and `Fence` in `User Layer 9`
  <figure>
	  <a href="/assets/img/posts/unity/layer1.png"><img src="/assets/img/posts/unity_blockshooter/layer1.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Click `Bullet` Prefab in Project View
  - Change `Layer` to `Bullet` in Inspector View
  - Click unvisible wall game object
  - Change `Layer` to `Fence`
  <figure>
	  <a href="/assets/img/posts/unity/layer2.png"><img src="/assets/img/posts/unity_blockshooter/layer2.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Edit>Project Settings>Physics
  - Open `Physics Manager` in Inspector View
  - Open `Layer Collision Matrix`
  - Uncheck where is crossing `Bullet` and `Fence`
  <figure>
	  <a href="/assets/img/posts/unity/layer3.png"><img src="/assets/img/posts/unity_blockshooter/layer3.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/vf9d9X1umk6JX8CHmn62XH6@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Count score

* Structure
  <figure>
	  <a href="/assets/img/posts/unity/score1.png"><img src="/assets/img/posts/unity_blockshooter/score1.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>

* `Scorekeeper.js`: Manage score by number
  {% highlight java %}
    @HideInInspector
    var score : int;
    function OnGUI(){
      var sw : int = Screen.width;
      var sh : int = Screen.height;
      var scoreText : String = "SCORE: " + score.ToString();
      GUI.Label(Rect(0, 0, sw/2, sh/4), scoreText);
    }    
  {% endhighlight %}

* `Referee.js`: Process to manage rule and change target
  {% highlight java %}
    @script RequireComponent(Scorekeeper)
    var switchInterval : int;
    var rewardPoint : int;
    var penaltyPoint : int;

    private var scorekeeper : Scorekeeper;
    private var targetIsRed : boolean;
    private var switchTimer : float;

    private function GetRatgetColorName() : String{
      return targetIsRed ? "Red" : "Blue";
    }   

    function Start(){
      scorekeeper = GetComponent(Scorekeeper) as Scorekeeper;
      targetIsRed = true;
      switchTimer = switchInterval;
    }

    function Update(){
      switchTimer -= Time.deltaTime;
      if(switchTimer < 0.0){
        targetIsRed = !targetIsRed;
        switchTimer = switchInterval;
      }
    }

    function OnDestroyBox(boxColorName : String){
      If (boxColorName == GetTargetColorName()){
        scorekeeper.score += rewardPoint;
      } else {
        scorekeeper.score -= penaltyPoint;
      }
    }

    function OnGUI(){
      If (switchTimer < 1.5) return;
      var sw : int = Screen.width;
      var sh : int = Screen.height;
      var message : String "Shoot" + GetTargetColorName() + " Boxes";
      GUI.color = targetIsRed ? Color.red : Color.blue;
      GUI.Label(Rect(0, sh/4, sw, sh/2), message);
    }
  {% endhighlight %}

* `Box.js`(change): Save which box is desproyed<br>
  - Add this code first line
  {% highlight java %}
    var colorName : String;
  {% endhighlight %}
  - Change `Update()`
  {% highlight java %}
    function Update(){
      if (!damaged) return;
      killTimer -= Time.deltaTime;
      if(killTimer <= 0.0){
        // here
        var gameController : GameObject = GameObject.FindWithTag("GameController");
        gameController.SendMessage("OnDestroyBox", colorName);
        //
        Instantiate(explosionPrefab, transform.position, transform.rotation);
        Destroy(gameObject);
      }
  {% endhighlight %}

* Create `Game Controller` game object
  - Create empty game object and change name to `Game Controller`
  - Select `Game Controller` in `Tag` pull down menu in Top of Inspector View
  <figure>
	  <a href="/assets/img/posts/unity/score2.png"><img src="/assets/img/posts/unity_blockshooter/score2.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Add `Referee.js`
  - change `Reward Point` to 10 and `Penalty Point` to 7
  <figure>
	  <a href="/assets/img/posts/unity/score3.png"><img src="/assets/img/posts/unity_blockshooter/score3.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Put `Box Generator` under the `Game controller`
  <figure>
	  <a href="/assets/img/posts/unity/score4.png"><img src="/assets/img/posts/unity_blockshooter/score4.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Select `Red Box` Prefab in Project View and input `Red` in `Color name` of `Box` script component in Inspector View
  - Same in `Blue Box`
  <figure class="half">
	  <a href="/assets/img/posts/unity/score5.png"><img src="/assets/img/posts/unity_blockshooter/score5.png"></a>
    <a href="/assets/img/posts/unity/score6.png"><img src="/assets/img/posts/unity_blockshooter/score6.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v7284OKdeTY9erAWEyTYeyY@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>

[Download block shooter game](https://github.com/leehuhlee/Unity){: .btn}
