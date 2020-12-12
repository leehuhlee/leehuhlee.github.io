---
layout: post
title: "Unity 4 - Block Shooter"
date: 2018-07-31
excerpt: "Block Shooter"
tags: [Unity, JavaScript]
comments: false
---


## Game design

* Room
* Box
* Ball
* Gun
* Box Generator
* Wall
* Score
* GUI Skin
* Time Limitation
* Result Scene
* Start Scene
* Title Scene

## Create room

* Create floor, wall and ceiling 
  - Create Cube and change Name to `Floor`
  - Create three walls and change Name to `Wall1`, `Wall2` and `Wall3`
  - Create Cube and change Name to `Ceiling`
  - Add checker texture
  - Decrease Static Friction and Dynamic Friction and increase Bounciness
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/room1.png"><img src="/assets/img/posts/unity_blockshooter/room1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Deploy light
  - Create Point Light and change Position to (0, 15, 0)
  - Change Range to 25 and Intensity to 2.0 in Light Component
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/light1.png"><img src="/assets/img/posts/unity_blockshooter/light1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Build hierarchy structure
  - Create Empty Game Object and change Position to (0, 0, 0)
  - Input all elements
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/hierarchy1.png"><img src="/assets/img/posts/unity_blockshooter/hierarchy1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Save Scene
  - build Settings>Scenes In Build
  - Create Main Scene
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/scene1.png"><img src="/assets/img/posts/unity_blockshooter/scene1.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>

* Deploy Camera
  - Set Aspect Ratio to 16:9 in Game View
  - Change Angle of Camera to 75
  <figure class="half">
	  <a href="/assets/img/posts/unity_blockshooter/camera1.png"><img src="/assets/img/posts/unity_blockshooter/camera1.png"></a>
    <a href="/assets/img/posts/unity_blockshooter/camera2.png"><img src="/assets/img/posts/unity_blockshooter/camera2.png"></a>
	  <figcaption>Block Shooter Room</figcaption>
  </figure>


## Create box

* Create box
  - Create Cube and change Scale to 2.5
  - Add Rigidbody Component
  - Add Physical Material and Tag Box
  - Copy the `Box`
  - Change Name each boxes to `Red Box` and `Blue Box`
  - Create Material for each boxes
  - Prefab each boxes
  - Drag and drop Prefabs to each boxes
    <figure class="half">
	  <a href="/assets/img/posts/unity_blockshooter/box1.png"><img src="/assets/img/posts/unity_blockshooter/box1.png"></a>
    <a href="/assets/img/posts/unity_blockshooter/box2.png"><img src="/assets/img/posts/unity_blockshooter/box2.png"></a>
	  <figcaption>Block Shooter Box</figcaption>
  </figure>


## Create Gun

* Create bullet Prepeb
  - Create Sphere and change Scale to 0.5
  - Add `Rigidbody` Component
  - Set Shader to Self-Illumin>diffuse
  - Prefab and change Name to `Bullet`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/bullet1.png"><img src="/assets/img/posts/unity_blockshooter/bullet1.png"></a>
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
  - Drag and drop `Bullet` Prefab to `bulletPrefab` in Inspector View
  - Change `initialVelocity` to 50
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
  - Drag and drop to `Bullet` Prefab

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
  - Create Empty Game Object
  - Component>Effects>Legacy Particles
  - Select `Ellipsoid Particle Emitter`, `Particle Animator` and `Particle Render`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/particle1.png"><img src="/assets/img/posts/unity_blockshooter/particle1.png"></a>
	  <figcaption>Block Shooter Gun</figcaption>
  </figure>
  - Set `Ellipsoid Particle Emitter`<br>
    Min Size: 3<br>
    Max Size: 5<br>
    Min Energy and Max Energy: 0.5<br>
    Min Emission and Max Emission: 30<br>
    X, Y and Z of Rnd Velocity: 25<br>
    Rnd Angular Velocity: 360<br>
    Rnd Rotation: On<br>
    One Shot: On<br>
  - Set Particle Animator<br>
    Damping: 0.01<br>
    Autodestruct: On
  - Create `Explosion` Prefab and drag and drop this particle effect
  - `Box.js`(change)
    {% highlight java %}
      function ApplyDamage(){
        Instantiate(explosionPrefab, transform.position, transform.rotation);
        Destroy(gameObject);
      }
    {% endhighlight %}
  - Drag and drop `Explosion` Prefab to `explosionPrefab` in Inspector View

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


## Create Box Generator
  - Create Empty Game Object and change Name to `Box Generator`

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
  - Drag and drop `BoxGenerator.js` to `Box Generator` Game Object
  - Change `Interval` to 0.5 and drag and drop each Prefabs to `Red Box Prefab` and `Blue Box Prefab` in Inspector View
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
  - Duplicate `wall`
  - Locate to front of `room`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/room2.png"><img src="/assets/img/posts/unity_blockshooter/room2.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Delete `Renderer` Component
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/room3.png"><img src="/assets/img/posts/unity_blockshooter/room3.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>

* Introduce Layer and Collision filtering
  - Edit>Project Setting>Tags
  - Open `Tag Manager` in Inspector View
  - Set `Bullet` in `User Layer 8` and `Fence` in `User Layer 9`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/layer1.png"><img src="/assets/img/posts/unity_blockshooter/layer1.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Click `Bullet` Prefab in Project View
  - Change `Layer` to `Bullet` in Inspector View
  - Click unvisible wall Game Object
  - Change `Layer` to `Fence`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/layer2.png"><img src="/assets/img/posts/unity_blockshooter/layer2.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  - Edit>Project Settings>Physics
  - Open `Physics Manager` in Inspector View
  - Open `Layer Collision Matrix`
  - Uncheck where is crossing `Bullet` and `Fence`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/layer3.png"><img src="/assets/img/posts/unity_blockshooter/layer3.png"></a>
	  <figcaption>Block Shooter Unvisible Wall</figcaption>
  </figure>
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/vf9d9X1umk6JX8CHmn62XH6@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Count score

* Structure
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/score1.png"><img src="/assets/img/posts/unity_blockshooter/score1.png"></a>
	  <figcaption>Block Shooter Score</figcaption>
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

* Create `Game Controller` Game Object
  - Create Empty Game Object and change Name to `Game Controller`
  - Select `Game Controller` in `Tag` pull down menu in top of Inspector View
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/score2.png"><img src="/assets/img/posts/unity_blockshooter/score2.png"></a>
	  <figcaption>Block Shooter Score</figcaption>
  </figure>
  - Add `Referee.js`
  - change `Reward Point` to 10 and `Penalty Point` to 7
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/score3.png"><img src="/assets/img/posts/unity_blockshooter/score3.png"></a>
	  <figcaption>Block Shooter Score</figcaption>
  </figure>
  - Put `Box Generator` under the `Game controller`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/score4.png"><img src="/assets/img/posts/unity_blockshooter/score4.png"></a>
	  <figcaption>Block Shooter Score</figcaption>
  </figure>
  - Select `Red Box` Prefab in Project View and input `Red` in `Color name` of `Box` Script Component in Inspector View
  - Same in `Blue Box`
  <figure class="half">
	  <a href="/assets/img/posts/unity_blockshooter/score5.png"><img src="/assets/img/posts/unity_blockshooter/score5.png"></a>
    <a href="/assets/img/posts/unity_blockshooter/score6.png"><img src="/assets/img/posts/unity_blockshooter/score6.png"></a>
	  <figcaption>Block Shooter Score</figcaption>
  </figure>
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v7284OKdeTY9erAWEyTYeyY@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Set GUI skin

* Create GUI skin
  - Create>GUI Skin
  - Change Name to `Game Skin`
  - Open Custom Styles
  - Change Size to 2
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/GUI1.png"><img src="/assets/img/posts/unity_blockshooter/GUI1.png"></a>
	  <figcaption>Block Shooter GUI</figcaption>
  </figure>
  - Open Element 0 and change Name to `score`
  - Open Normal and change Text Color to white
  - Change Font
  - Change Font Size to 25
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/GUI2.png"><img src="/assets/img/posts/unity_blockshooter/GUI2.png"></a>
	  <figcaption>Block Shooter GUI</figcaption>
  </figure>
 - Open Element 1 and change Name to `message`
 - Change Alignment to Middle Center
 - And same with `score`
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/GUI3.png"><img src="/assets/img/posts/unity_blockshooter/GUI3.png"></a>
	  <figcaption>Block Shooter GUI</figcaption>
  </figure>

* Apply GUI skin
  - `Scorekeeper.js`(change)<br>
  Add code in first line
  {% highlight java %}
    var skin : GUISkin;
  {% endhighlight %}
  Add code in first line of `OnGUI` function
  {% highlight java %}
    GUI.skin = skin;
  {% endhighlight %}
  Change `GUI.Label` function
  {% highlight java %}
    GUI.Label(Rect(0, 0, sw/2, sh/4), scoreText, "score");
  {% endhighlight %}
  - Drag and drop `Game Skin` to `Skin` in `Scoorekeeper` Script Component
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/GUI4.png"><img src="/assets/img/posts/unity_blockshooter/GUI4.png"></a>
	  <figcaption>Block Shooter GUI</figcaption>
  </figure>
  - `Referee.js`(change)<br>
  Add code in first line
  {% highlight java %}
    var skin : GUISkin;
  {% endhighlight %}
  Add code in first line of `OnGUI` function
  {% highlight java %}
    GUI.skin = skin;
  {% endhighlight %}
  Change `GUI.Label` function
  {% highlight java %}
    GUI.Label(Rect(0, 0, sw/2, sh/4), scoreText, "message");
  {% endhighlight %}
  - Drag and drop `Game Skin` to `Skin` in `Referee` Script Component
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/GUI5.png"><img src="/assets/img/posts/unity_blockshooter/GUI5.png"></a>
	  <figcaption>Block Shooter GUI</figcaption>
  </figure>
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v83bfgRuu8C8uC0TE2ZK8EI@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Set time limitation

* `Timekeeper.js`
  - Add `Game Controller` Game Object
  {% highlight java %}
    var gameLength : float;

    private var elapsed : float;

    function Update(){
      elapsed += Time.deltaTime;
      if(elapsed >= gameLength){
        BroadcastMessage("TimeUp");
        GameObject.FindWithTag("MainCamera").SendMessage("TimeUp");
        enabled = false;
      }
    }
  {% endhighlight %}
  - Set `Game Length` in `Timekeeper` Script Component
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/timer1.png"><img src="/assets/img/posts/unity_blockshooter/timer1.png"></a>
	  <figcaption>Block Shooter Timer</figcaption>
  </figure>

* Add "TimeUp" message processing
  - `Gun.js`(change), `Referee.js`(change) and `Box Generator.js`(change)<br>
  Add this code in last line
  {% highlight java %}
    function TimeUp(){
      enabled = false;
    }
  {% endhighlight %}
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/vcdd2OjOySOyiwujXouOoXO@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Create result scene

* `ReulstScreen.js`
  - Add `Game Controller` Game Object
  - Structure
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/result1.png"><img src="/assets/img/posts/unity_blockshooter/result1.png"></a>
	  <figcaption>Block Shooter Result Scene</figcaption>
  </figure>
  {% highlight java %}
    @script RequireComponent(Scorekeeper)

    var skin : GUISkin;

    private var scorekeeper : Scorekeeper;
    private var state : String;

    function Start(){
      scorekeeper = GetComponenet(Scorekeeper) as Scorekeeper;
    }

    function TimeUp(){
      state = "Time Up";
      yield WaitForSeconds(3.0);
      state = "";
      yield WaitForSeconds(0.5);
      state = "Show Score";
      while (!Input.GetButtonDown("Fire1")) yield;
      Application.LoadLevel("Main");
    }

    function OnGUI(){
      var sw : int = Screen.width;
      var sh : int = Screen.height;
      GUI.skin = skin;
      If (state == "Time Up"){
        GUI.Label(Rect(0, 0, sw, sh), "Time Up!!", "message");
      }
      else if (state == "Show Score"){
        var scoreText : String = "Your score is " + scorekeeper.score.ToString();
        GUI.Label(Rect(0, sh/4, sw, sh/4), scoreText, "message");
        GUI.Label(Rect(0, sh/2, sw, sh/4), "Click to Exit", "message");
      }
    }
  {% endhighlight %}
  - Drag and drop `Game Skin` in `skin` in `ResultScreen` Script


## Create start scene

* `GameStarter.js`
  - Add `Game Controller` Object
  {% highlight java %}
    var skin : GUISkin;

    private var timer : float;

    function Start(){
      timer = 3.5;
    }

    function Update(){
      timer -= Time.deltaTime;
      if(timer <= 0.0){
        BroadcastMessage("StartGame");
        enabled = false;
      }
    }

    function OnGUI(){
      If (timer > 3.0 || timer <= 0.0) return;

      var sw : int = Screen.width;
      var sh : int = Screen.height;
      var text : String = Mathf.CeilToInt(timer).ToString();
      GUI.skin = skin;
      GUI.color = Color(1, 1, 1, timer - Mathf.FloorToInt(timer));
      GUI.Label(Rect(0, sh/4, sw, sh/2), text, "message");
    }
  {% endhighlight %}

* Create `StartGame` message processing
  - `Referee.js`(change), `Timekeeper.js`(change) and `BoxGenerator.js`(change)<br>
  Add this code in last line
  {% highlight java %}
    function StartGame(){
      enabled = true;
    }
  {% endhighlight %}

* Set GUI and check action
  - Drag and drop `Game Skin` in `skin` in `GameStarter` Script
  - Uncheck `Referee.js`, `Timekeeper.js` and `BoxGenerator.js` in Inspector View
  <figure class="half">
	  <a href="/assets/img/posts/unity_blockshooter/start1.png"><img src="/assets/img/posts/unity_blockshooter/start1.png"></a>
    <a href="/assets/img/posts/unity_blockshooter/start2.png"><img src="/assets/img/posts/unity_blockshooter/start2.png"></a>
	  <figcaption>Block Shooter Start Scene</figcaption>
  </figure>
  <iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v44a9x5aU2AaARSyAURSA5a@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Create Title Scene

* Create `Title ` Scene
  - Save `Main` Scene first
  - File>Save Scene as
  - Save Name to `Title`
  - Add `Title` by press `Add Current` to add in `Scene In Build`
  - Put `Title` in the top
  <figure>
	  <a href="/assets/img/posts/unity_blockshooter/title1.png"><img src="/assets/img/posts/unity_blockshooter/title1.png"></a>
	  <figcaption>Block Shooter Title Scene</figcaption>
  </figure>
  - Delete `Game Controller` Game Object
  - Delete `Gun.js` in `Main Camera` Game Object

* `TitleScreen.js`
{% highlight java %}
  var skin : GUISkin;

  function Update(){
    if (Input.GetButtonDown("Fire1")){
      Application.LoadLevel("Main");
    }
  }

  function OnGUI(){
    var sw : int = Screen.width;
    var sh : int = Screen.height;
    GUI.skin = skin;
    GUI.Label(Rect(0, 0, sw, sh), "B L O C K S H O O T E R", "message");
    GUI.Label(Rect(0, sh/2, sw, sh/2), "Click to Start", "message");
  }
{% endhighlight %}
  - Create Empty Game Object
  - Change Name to `Title`
  - Add `Title.js` and change skin to `Game Skin`
  - `ResultScreen.js`(change)
  {% highlight java %}
    //here
    Application.LoadLevel("Title");
    //
  {% endhighlight %}
<iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v53b4xVt46V56cmgnxYmYno@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Demo Video

<iframe title="유니티 입문 5장 블록 슈터 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v8a7ctypNLLNK718rNBVOKO@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>
