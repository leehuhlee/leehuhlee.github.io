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
	  <a href="/assets/img/posts/unity_coincrazy/stage4.png"><img src="/assets/img/posts/unity_coincrazy/stage4.png"></a>
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
  - Open DemoScene in character Asset folder
  <figure class>
	  <a href="/assets/img/posts/unity_coincrazy/character2.png"><img src="/assets/img/posts/unity_coincrazy/character2.png"></a>
	  <figcaption>Coin Crazy Character</figcaption>
  </figure>
  - Copy character and paste to game Scene
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/character3.png"><img src="/assets/img/posts/unity_coincrazy/character3.png"></a>
	  <figcaption>Coin Crazy Character</figcaption>
  </figure>
  - Set Bounce Combine to minimum
  - Change Jump Force to 6
  <iframe title="제 6장 Coin Crazy" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v318eeEieuEapY0ziupXzXE@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Implement coin and obstacle

* Create coin and obstacle
  - Download `Old Coin` in Asset Store
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/coin1.png"><img src="/assets/img/posts/unity_coincrazy/coin1.png"></a>
	  <figcaption>Coin Crazy Coin</figcaption>
  </figure>
  - Download `Ball Pack` in Asset Store
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/obstacle1.png"><img src="/assets/img/posts/unity_coincrazy/obstacle1.png"></a>
	  <figcaption>Coin Crazy Obstacle</figcaption>
  </figure>
  - Open Presentation Scene in `Old Coin`
  - Copy coin and paste to game Scene
  - Change Scale and Rotation
  - Open Material folder and change Color to yellow in Inspertor in Coin Object
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/coin2.png"><img src="/assets/img/posts/unity_coincrazy/coin2.png"></a>
	  <figcaption>Coin Crazy Coin</figcaption>
  </figure>

  - Open `Ball Pack`
  - copy `SpikeBall` and paste to game Scene
  - Change Scale and Position

* `ShortMoveController.js`
{% highlight java %}
  var velocity : float = 8.0;
  var moveDelay : float = 1.0;
  var sustainTime : float = 3.0;

  function Start(){
    yield WaitForSeconds(moveDelay);

    var player : GameObject = GameObject.FindWithTag("Player");
    if(player != null){
      var direction : Vector3 = (player.transform.position - transfor.position).normalized;
      rigidbody.AddForce(direction * velocity, ForceMode.VelocityChange);
    }

    yield WaitForSeconds(sustainTime);
    Destroy(gameObject);
  }
{% endhighlight %}
  - Add `ShortMoveController.js` to `Coin`, `SpikeBall`
  - Change Tag of character to Player
  <iframe title="제 6장 Coin Crazy" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v55d4Ofe9KG9XZsYGe9hXfG@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Create generator

* `Generator.js`
{% highlight java %}
  var intervalMin : float = 0.5;
  var intervalMax : float = 1.5;
  var coinRate : float = 0.3;

  var coinPrefab : GameObject;
  var spikeBallPrefab : GameObject;

  function Start(){
    while(true){
      yield WaitForSeconds(Random.Range(intervalMin, intervalMax));

      var Prefab : GameObject = Random.value < coinRate ? coinPrefab : spikeBallPrefab;
      var theta : float = Random.Range(0.0, Mathf.PI * 2.0);
      var position : Vector3 = Vector3(Mathf.Cos(theta), 0.0, Mathf.Sin(theta)) * 5.5;
      position.y = 2.5;
      Instantiate(prefab, position, Quaternion.identity);
    }
  }
{% endhighlight %}
  - Create Empty Game Object and change Name to `Generator`
  - Drag and drop `Generator.js` to `Generator`
  - Prefab `Coin` and `SpikeBall`
  - Drag and drop each prefabs to `coinPrefab` and `spikePrefab` in `Generator.js`
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/generator1.png"><img src="/assets/img/posts/unity_coincrazy/generator1.png"></a>
	  <figcaption>
  </figure>

  <iframe title="제 6장 Coin Crazy" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/ve068FTEeNAFTdmT8emANmF@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Messaging for getting coin and demage

* Set layer
  - Create layer and change Name to `Character` and `Moving Object`
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/message1.png"><img src="/assets/img/posts/unity_coincrazy/message1.png"></a>
	  <figcaption>
  </figure>
  - Set Layer of character Game Object to `Character`
  - Set Layer of `Coin` and `SpikeBall` Prefab to `Moving Object`
  - Set Matrix to not collide `Charater` Layer and `Moving Objects` Layer
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/message2.png"><img src="/assets/img/posts/unity_coincrazy/message2.png"></a>
	  <figcaption>
  </figure>
  - Check `Is Trigger` in Inspector of `C_man_1_FBX2013` Game Object of character

* Create sender script
  - `Coin.js`
  {% highlight java %}
    function OnTriggerEnter(other : Collider){
      other.gameObject.SendMessage("CatchCoin", 1);
      Destroy(gmaeObject);
    }
  {% endhighlight %}
  - Drag and drop `Coin.js` to `Coin` Prefab
  - `SpikeBall.js`
  {% highlight java %}
    function OnTriggerEnter(other : Collider){
      other.gameObject.SendMessage("ApplyDamage", 10);
      Destroy(gmaeObject);
    }
  {% endhighlight %}
  - Drag and drop `SpikeBall.js` to `SpikeBall` Prefab

* Create receiver script
  - `PlayerStatus.js`
  {% highlight java %}
    private var life : int = 100;
    private var score : int = 0;

    function CatchCoin(amount : int){
      score += amount;
    }

    function ApplyDamage(amount : int){
      life -= amount;

      if (life <= 0){
        Destroy(transform.parent.gameObject);
      }
    }

    function OnGUI(){
      var rect : Rect = Rect(0, 0, Screen.width, Screen.height);
      GUI.Label(rect, "LIFE: " + life.ToString() + " / SCORE: " + score.ToString());
    }
  {% endhighlight %}

  <iframe title="제 6장 캐릭터 액션 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v21af74Zbm3Z11Asb7NNibh@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>


## Improve visual presentation

* `DamageEffector.js`
{% highlight java %}
  private var effectFlag : boolean;
  private var originalColor : Color;

  function Start(){
    originalColor = render.material.color;
  }

  function Update(){
    if(effectFlag){
      renderer.material.color = Color.red * Mathf.Abs(Mathf.Sin(40.0 * Time.time));
    }
  }

  function ApplyDamage(amount : int){
    effectFlag = true;
    yield WaitForSeconds(0.3);
    effectFlag = false;
    renderer.material.color = originalColor;
  }
{% endhighlight %}
  - Drag and drop `DamageEffector.js` to character Game Object<br>

  <iframe title="제 6장 캐릭터 액션 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/v9a3c6l6FYSHllPqSHhFagS@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>

## Apply sound

* Import sound file
  - Download `Sound Pack Free Pack` in Asset Store
  <figure>
    <a href="/assets/img/posts/unity_coincrazy/sound1.png"><img src="/assets/img/posts/unity_coincrazy/sound1.png"></a>
	  <figcaption>
  </figure>
- Click `C_man_1_FBX2013` Game Object and click Component>Audio>Audio Source
- Change Audio Clip to `buy` and uncheck `Play On Awake` in Audio Source in Inspector

* `PlayerSoundEffector.js`
{% highlight java %}
  function CatchCoin(amount : int){
    audio.Play();
  }
{% endhighlight %}
  - Drag and drop `PlayerSoundEffector.js` to character Game Object<br>

  <iframe title="제 6장 캐릭터 액션 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/ve639zTmdr7mx3kNfh76rdf@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>

* `PlayerSoundEffector.js`(change)
{% highlight java %}
  var coinSE : AudioClip;
  var damageSE : AudioClip;

  function CatchCoin(amount : int){
    audio.PlayOneShot(coinSE);
  }

  function ApplyDamage(amount : int){
    audio.PlayOneShot(damageSE);
  }
{% endhighlight %}
  - Change demage sound<br>

  <iframe title="제 6장 캐릭터 액션 게임" width="640" height="360" src="https://play-tv.kakao.com/embed/player/cliplink/vb852slnsW4k9miKonn4kJk@my?service=player_share" allowfullscreen frameborder="0" scrolling="no" allow="autoplay"></iframe>

[Download Game](https://github.com/leehuhlee/Unity){: .btn}
