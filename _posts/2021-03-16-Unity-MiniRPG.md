---
layout: post
title: "[Unity] Mini RPG"
date: 2021-03-15
excerpt: "Framework"
tags: [Unity, Game]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/MMORPG-%EC%9C%A0%EB%8B%88%ED%8B%B0/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part3: 유니티 엔진</a></center>

# Setting Environment

* Asset Store
  - Download Medieval Town Exteriors
  - change folder name
<figure class="third">
  <a href="/assets/img/posts/unity_minirpg/0.jpg"><img src="/assets/img/posts/unity_minirpg/0.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/1.jpg"><img src="/assets/img/posts/unity_minirpg/1.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/3.jpg"><img src="/assets/img/posts/unity_minirpg/3.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

* Import from own Unity Framework
  - create player
  - create Scene Manager
  - create Main Camera
<figure>
  <a href="/assets/img/posts/unity_minirpg/2.jpg"><img src="/assets/img/posts/unity_minirpg/2.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

# Move

* Layer
  - Change Layer to `Wall` in Terrain
  - if camera doesn't move, drag and drop player to player of CameraController Component
  - Change Layer to `Block` in Buildings, Components and Fence

* Navigation

<figure class="half">
  <a href="/assets/img/posts/unity_minirpg/4.jpg"><img src="/assets/img/posts/unity_minirpg/4.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/5.jpg"><img src="/assets/img/posts/unity_minirpg/5.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

  - `Bake` means create files before execute
  - Bake is related with static option of objects
  - from Navigation, we can define whether player can go

* Nav Mesh Agent
  - add `Nav Mesh Component` to player

<figure>
  <a href="/assets/img/posts/unity_minirpg/6.jpg"><img src="/assets/img/posts/unity_minirpg/6.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

* PlayerController.cs
{% highlight C# %}
void UpdateMoving()
{
    ...
    if (dir.magnitude < 0.1f)
    {
        _state = PlayerState.Idle;
    }
    else
    {
        NavMeshAgent nma = gameObject.GetOrAddComponent<NavMeshAgent>();
        float moveDist = Mathf.Clamp(_speed * Time.deltaTime, 0, dir.magnitude);

        nma.Move(dir.normalized * moveDist);

        Debug.DrawRay(transform.position + Vector3.up * 0.5f, dir.normalized, Color.green);
        if(Physics.Raycast(transform.position + Vector3.up *0.5f, dir, 1.0f, LayerMask.GetMask("Block")))
        {
            _state = PlayerState.Idle;
            return;
        }

        //transform.position += dir.normalized * moveDist;
        ...
    }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Move.mp4" frameborder="0"> </iframe>

# Stat

## Monster
* Download Monster in Asset Store
  - download any knight model with animation
* organize folder

<figure>
  <a href="/assets/img/posts/unity_minirpg/7.jpg"><img src="/assets/img/posts/unity_minirpg/7.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

## Content

* Scripts\Contents\Stat.cs
{% highlight C# %}
public class Stat : MonoBehaviour
{
    [SerializeField]
    protected int _level;
    [SerializeField]
    protected int _hp;
    [SerializeField]
    protected int _maxhp;
    [SerializeField]
    protected int _attack;
    [SerializeField]
    protected int _defense;
    [SerializeField]
    protected float _moveSpeed;

    public int Level { get { return _level; } set { _level = value; } }
    public int HP { get { return _hp; } set { _hp = value; } }
    public int MaxHp { get { return _maxhp; } set { _maxhp = value; } }
    public int Attack { get { return _attack; } set { _attack = value; } }
    public int Defense { get { return _defense; } set { _defense = value; } }
    public float MoveSpeed { get { return _moveSpeed; } set { _moveSpeed = value; } }

    public void Start()
    {
        _level = 1;
        _hp = 100;
        _maxhp = 100;
        _attack = 10;
        _defense = 5;
        _moveSpeed = 5.0f;
    }
}
{% endhighlight %}

  - change before Stat Class to `Data.Stat`

* Scripts\Contents\PlayerStat.cs
{% highlight C# %}
public class PlayerStat : Stat
{
    [SerializeField]
    protected int _exp;
    [SerializeField]
    protected int _gold;

    public int Exp { get { return _exp; } set { _exp = value; } }
    public int Gold { get { return _gold; } set { _gold = value; } }

    public void Start()
    {
        _level = 1;
        _hp = 100;
        _maxhp = 100;
        _attack = 10;
        _defense = 5;
        _moveSpeed = 5.0f;
        _exp = 0;
        _gold = 0;
    }
}
{% endhighlight %}

* Add Component
  - add PlayerStat.cs component in Player
  - add Stat.cs component in Monster

## Layer

* Change Layer
  - Change Layer name `Wall` to `Ground`
  - Change Terrain layer to `Ground`

* Add Layer
  - add Layer `Monster` in Monster

* PlayerController.cs
  - change `_speed` to `_stat.MoveSpeed`
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    //[SerializeField]
    //float _speed = 10.0f;

    PlayerStat _stat;
    ...

    void Start()
    {
        _stat = gameObject.GetComponent<PlayerStat>();
       ...
    }
    ...

    void UpdateMoving()
    {
        ...
        else
        {
            ...
            float moveDist = Mathf.Clamp(_stat.MoveSpeed * Time.deltaTime, 0, dir.magnitude);
            ...
     
            anim.SetFloat("speed", _stat.MoveSpeed);
        }
    }
    ...

    int _mask = (1 << (int)Define.Layer.Ground) | (1 << (int)Define.Layer.Monster);

    void OnMouseClicked(Define.MouseEvent evt)
    {
        ...
        if (Physics.Raycast(ray, out hit, 100.0f, _mask))
        {
            ...

            if(hit.collider.gameObject.layer == (int)Define.Layer.Monster)
            {
                Debug.Log("Monster Click!");
            }
            else
            {
                Debug.Log("Ground Click!");
            }
        }
    }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Stat.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
