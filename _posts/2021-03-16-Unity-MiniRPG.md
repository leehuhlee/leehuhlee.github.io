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

# Cursor

## Cursor
  * Download Cursor in Asset Store
  * Select cursors and Move those to Resources\Textures\Cursor

<figure class="half">
  <a href="/assets/img/posts/unity_minirpg/8.jpg"><img src="/assets/img/posts/unity_minirpg/8.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/9.jpg"><img src="/assets/img/posts/unity_minirpg/9.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

  * Change Texture type to `Cursor` in all Cursor

<figure class="third">
  <a href="/assets/img/posts/unity_minirpg/10.jpg"><img src="/assets/img/posts/unity_minirpg/10.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/11.jpg"><img src="/assets/img/posts/unity_minirpg/11.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/12.jpg"><img src="/assets/img/posts/unity_minirpg/12.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

## Change Cursor
* PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    ...
    Texture2D _attackIcon;
    Texture2D _handIcon;

    enum CursorType
    {
        None,
        Attack,
        Hand,
    }

    CursorType _cursorType = CursorType.None;

    void Start()
    {
        _attackIcon = Managers.Resource.Load<Texture2D>("Textures/Cursor/Attack");
        _handIcon = Managers.Resource.Load<Texture2D>("Textures/Cursor/Hand");
        ...
    }
    ...

    void UpdateMouseCousor()
    {
       ...
        if (Physics.Raycast(ray, out hit, 100.0f, _mask))
        {
            if (hit.collider.gameObject.layer == (int)Define.Layer.Monster)
            {
                if(_cursorType != CursorType.Attack)
                {
                    Cursor.SetCursor(_attackIcon, new Vector2(_attackIcon.width / 5, 0), CursorMode.Auto);
                    _cursorType = CursorType.Attack;
                }
            }
            else
            {
                if(_cursorType != CursorType.Hand)
                {
                    Cursor.SetCursor(_handIcon, new Vector2(_handIcon.width / 3, 0), CursorMode.Auto);
                    _cursorType = CursorType.Hand;
                }
            }
        }
    }
    ...
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Cursor.mp4" frameborder="0"> </iframe>

# Targeting

* Click vs Press
  - Click: press mouse button shortly
  - Press: press mouse button for several seconds
  - you can define Click and Press

## Mouse Event

* Define.cs
{% highlight C# %}
public enum MouseEvent
{
    Press,
    PointerDown,
    PointerUp,
    Click,
}
{% endhighlight %}

* PlayerController.cs
  - when you press mouse button, player still moves

{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    void UpdateMouseCousor()
    {
        if (Input.GetMouseButton(0))
            return;

        ...
        RaycastHit hit;
        if (Physics.Raycast(ray, out hit, 100.0f, _mask))
        {
            if (hit.collider.gameObject.layer == (int)Define.Layer.Monster)
            {
                if(_cursorType != CursorType.Attack)
                {
                    Cursor.SetCursor(_attackIcon, new Vector2(_attackIcon.width / 5, 0), CursorMode.Auto);
                    _cursorType = CursorType.Attack;
                }
            }
            else
            {
                if(_cursorType != CursorType.Hand)
                {
                    Cursor.SetCursor(_handIcon, new Vector2(_handIcon.width / 3, 0), CursorMode.Auto);
                    _cursorType = CursorType.Hand;
                }
            }
        }
    }
    ...

    GameObject _lockTarget;

    void OnMouseEvent(Define.MouseEvent evt)
    {
        if (_state == PlayerState.Die)
            return;

        RaycastHit hit;
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
        bool raycastHit = Physics.Raycast(ray, out hit, 100.0f, _mask);

        switch (evt)
        {
            case Define.MouseEvent.PointerDown:
                {
                    if (raycastHit)
                    {
                        _destPos = hit.point;
                        _state = PlayerState.Moving;

                        if(hit.collider.gameObject.layer == (int)Define.Layer.Monster)
                            _lockTarget = hit.collider.gameObject;
                        else
                            _lockTarget = null;
                    }
                }
                break;
            case Define.MouseEvent.Press:
                {
                    if(_lockTarget != null)
                        _destPos = _lockTarget.transform.position;
                    else if (raycastHit)
                            _destPos = hit.point;
                }
                break;
            case Define.MouseEvent.PointerUp:
                _lockTarget = null;
                break;
        }
    }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Target.mp4" frameborder="0"> </iframe>

# Attack

## Mouse Event
* InputManager.cs
  - Define Click and Press
{% highlight C# %}
public class InputManager
{
    ...
    public Action<Define.MouseEvent> MouseAction = null;

    bool _pressed = false;
    float _pressedTime = 0;

    public void OnUpdate()
    {
        if (EventSystem.current.IsPointerOverGameObject())
            return;

        ...

        if (MouseAction != null)
        {
            if (Input.GetMouseButton(0))
            {
                if (!_pressed)
                {
                    MouseAction.Invoke(Define.MouseEvent.PointerDown);
                    _pressedTime = Time.time;
                }
                MouseAction.Invoke(Define.MouseEvent.Press);
                _pressed = true;
            }
            else
            {
                if (_pressed)
                {
                    if (Time.time < _pressedTime + 0.2f)
                        MouseAction.Invoke(Define.MouseEvent.Click);
                    MouseAction.Invoke(Define.MouseEvent.PointerUp);
                }
                _pressed = false;
                _pressedTime = 0;
            }
        }
    }
    ...
}
{% endhighlight %}

* PlayerController.cs
  - included code organization

{% highlight C# %}
public class PlayerController : MonoBehaviour
{
	public enum PlayerState
	{
		Die,
		Moving,
		Idle,
		Skill,
	}

	int _mask = (1 << (int)Define.Layer.Ground) | (1 << (int)Define.Layer.Monster);

	PlayerStat _stat;
	Vector3 _destPos;

	[SerializeField]
	PlayerState _state = PlayerState.Idle;

	GameObject _lockTarget;

	public PlayerState State
	{
		get { return _state; }
		set
		{
			_state = value;

			Animator anim = GetComponent<Animator>();
			switch (_state)
			{
				case PlayerState.Die:
					break;
				case PlayerState.Idle:
					anim.CrossFade("WAIT", 0.1f);
					break;
				case PlayerState.Moving:
					anim.CrossFade("RUN", 0.1f);
					break;
				case PlayerState.Skill:
					anim.CrossFade("ATTACK", 0.1f, -1, 0);
					break;
			}
		}
	}
  ...

	void UpdateMoving()
	{
		if (_lockTarget != null)
		{
			_destPos = _lockTarget.transform.position;
			float distance = (_destPos - transform.position).magnitude;
			if (distance <= 1)
			{
				State = PlayerState.Skill;
				return;
			}
		}

		Vector3 dir = _destPos - transform.position;
		if (dir.magnitude < 0.1f)
		{
			State = PlayerState.Idle;
		}
		else
		{
			NavMeshAgent nma = gameObject.GetOrAddComponent<NavMeshAgent>();
			float moveDist = Mathf.Clamp(_stat.MoveSpeed * Time.deltaTime, 0, dir.magnitude);
			nma.Move(dir.normalized * moveDist);

			Debug.DrawRay(transform.position + Vector3.up * 0.5f, dir.normalized, Color.green);
			if (Physics.Raycast(transform.position + Vector3.up * 0.5f, dir, 1.0f, LayerMask.GetMask("Block")))
			{
				if (Input.GetMouseButton(0) == false)
					State = PlayerState.Idle;
				return;
			}

			transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 20 * Time.deltaTime);
		}
	}
  ...

	void UpdateSkill()
	{
		if (_lockTarget != null)
		{
			Vector3 dir = _lockTarget.transform.position - transform.position;
			Quaternion quat = Quaternion.LookRotation(dir);
			transform.rotation = Quaternion.Lerp(transform.rotation, quat, 20 * Time.deltaTime);
		}
	}

	void OnHitEvent()
	{
		if (_stopSkill)
			State = PlayerState.Idle;
		else
			State = PlayerState.Skill;
		
	}

	void Update()
    {
		switch (State)
		{
			case PlayerState.Die:
				UpdateDie();
				break;
			case PlayerState.Moving:
				UpdateMoving();
				break;
			case PlayerState.Idle:
				UpdateIdle();
				break;
			case PlayerState.Skill:
				UpdateSkill();
				break;
		}
	}

	bool _stopSkill = false;

	void OnMouseEvent(Define.MouseEvent evt)
	{
		switch (State)
		{
			case PlayerState.Idle:
				OnMouseEvent_IdleRun(evt);
				break;
			case PlayerState.Moving:
				OnMouseEvent_IdleRun(evt);
				break;
			case PlayerState.Skill:
				{
					if (evt == Define.MouseEvent.PointerUp)
						_stopSkill = true;
				}
				break;
		}
	}

	void OnMouseEvent_IdleRun(Define.MouseEvent evt)
	{
		RaycastHit hit;
		Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
		bool raycastHit = Physics.Raycast(ray, out hit, 100.0f, _mask);

		switch (evt)
		{
			case Define.MouseEvent.PointerDown:
				{
					if (raycastHit)
					{
						_destPos = hit.point;
						State = PlayerState.Moving;
						_stopSkill = false;

						if (hit.collider.gameObject.layer == (int)Define.Layer.Monster)
							_lockTarget = hit.collider.gameObject;
						else
							_lockTarget = null;
					}
				}
				break;
			case Define.MouseEvent.Press:
				{
					if (_lockTarget == null && raycastHit)
						_destPos = hit.point;
				}
				break;
			case Define.MouseEvent.PointerUp:
				_stopSkill = true;
				break;
		}
	}
}
{% endhighlight %}

## Animation
  - use CrossFade method
  - delete all parameters in PlayerAnimController
  - add `ATTACK` Animation from Knight animation resources in PlayerAnimController

<figure>
  <a href="/assets/img/posts/unity_minirpg/13.jpg"><img src="/assets/img/posts/unity_minirpg/13.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

## UI
  - Create sword prefab from knight and paste to player

<figure>
  <a href="/assets/img/posts/unity_minirpg/14.jpg"><img src="/assets/img/posts/unity_minirpg/14.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Attack.mp4" frameborder="0"> </iframe>

# Hp
  - reduce monster's Hp gage when player attaks monster

## UI
  - Create Hp bar by `Slider`
  - Create Prefab
<figure>
  <a href="/assets/img/posts/unity_minirpg/15.jpg"><img src="/assets/img/posts/unity_minirpg/15.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

  - Change transform
<figure class="third">
  <a href="/assets/img/posts/unity_minirpg/16.jpg"><img src="/assets/img/posts/unity_minirpg/16.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/17.jpg"><img src="/assets/img/posts/unity_minirpg/17.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/18.jpg"><img src="/assets/img/posts/unity_minirpg/18.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

## Componenets

* UIManager.cs
{% highlight C# %}
public T MakeWorldSpaceUI<T>(Transform parent = null, string name = null) where T : UI_Base
{
    if (string.IsNullOrEmpty(name))
        name = typeof(T).Name;

    GameObject go = Managers.Resource.Instantiate($"UI/WorldSpace/{name}");

    if (parent != null)
        go.transform.SetParent(parent);

    Canvas canvas = go.GetOrAddComponent<Canvas>();
    canvas.renderMode = RenderMode.WorldSpace;
    canvas.worldCamera = Camera.main;

    return Util.GetOrAddComponent<T>(go);
}
{% endhighlight %}

* PlayerController.cs
  - if myStat.Attack - targetStat.Defense is negative, then damage is 0

{% highlight C# %}
void Start()
{
    ...

		Managers.UI.MakeWorldSpaceUI<UI_HPBar>(transform);
}
...

void OnHitEvent()
{
	  if(_lockTarget != null)
        {
			Stat targetStat = _lockTarget.GetComponent<Stat>();
			Stat myStat = gameObject.GetComponent<PlayerStat>();
			int damage = Mathf.Max(0, myStat.Attack - targetStat.Defense);
			targetStat.Hp -= damage;
        }

		if (_stopSkill)
			State = PlayerState.Idle;
		else
			State = PlayerState.Skill;
		
}
{% endhighlight %}

* Scripts\UI\WordSpace\UI_HPBar.cs
  - ratio controlls hp
  - int / int = int, so we need float casting
  - add UI_HPBar.cs component to monster

{% highlight C# %}
public class UI_HPBar : UI_Base
{
    enum GameObjects
    {
        HPBar,
    }

    Stat _stat;

    public override void Init()
    {
        Bind<GameObject>(typeof(GameObjects));
        _stat = transform.parent.GetComponent<Stat>();
    }

    private void Update()
    {
        Transform parent = transform.parent;
        transform.position = parent.position + Vector3.up * (parent.GetComponent<Collider>().bounds.size.y);
        transform.rotation = Camera.main.transform.rotation;

        float ratio = _stat.Hp / (float)_stat.MaxHp;
        SetHpRatio(ratio);
    }

    public void SetHpRatio(float ratio)
    {
        GetObject((int)GameObjects.HPBar).GetComponent<Slider>().value = ratio;
    }
}
{% endhighlight %}

* UI_Base.cs
  - you should remove all start function in this project 

{% highlight C# %}
private void Start()
{
    Init();
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-UI-HPBar.mp4" frameborder="0"> </iframe>

# Monster AI
  - monster can detect player
  - monster can follow player
  - monster can attack player

## State Controll
  - Change state by enum and switch

* PlayerController.cs
  - include code organization
  - change PlayerState to Define.State

{% highlight C# %}
public class PlayerController : BaseController
{
	int _mask = (1 << (int)Define.Layer.Ground) | (1 << (int)Define.Layer.Monster);
	bool _stopSkill = false;

	PlayerStat _stat;

	public  override void  Init()
    {
		_stat = gameObject.GetComponent<PlayerStat>();
		Managers.Input.MouseAction -= OnMouseEvent;
		Managers.Input.MouseAction += OnMouseEvent;

		if (gameObject.GetComponentInChildren<UI_HPBar>() == null)
			Managers.UI.MakeWorldSpaceUI<UI_HPBar>(transform);
	}

	protected override void UpdateMoving()
	{
		if (_lockTarget != null)
		{
			_destPos = _lockTarget.transform.position;
			float distance = (_destPos - transform.position).magnitude;
			if (distance <= 1)
			{
				State = Define.State.Skill;
				return;
			}
		}

		Vector3 dir = _destPos - transform.position;
		if (dir.magnitude < 0.1f)
		{
			State = Define.State.Idle;
		}
		else
		{
			Debug.DrawRay(transform.position + Vector3.up * 0.5f, dir.normalized, Color.green);
			if (Physics.Raycast(transform.position + Vector3.up * 0.5f, dir, 1.0f, LayerMask.GetMask("Block")))
			{
				if (Input.GetMouseButton(0) == false)
					State = Define.State.Idle;
				return;
			}

			float moveDist = Mathf.Clamp(_stat.MoveSpeed * Time.deltaTime, 0, dir.magnitude);
			transform.position += dir.normalized * moveDist;
			transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 20 * Time.deltaTime);
		}
	}

	protected override void UpdateSkill()
	{
		if (_lockTarget != null)
		{
			Vector3 dir = _lockTarget.transform.position - transform.position;
			Quaternion quat = Quaternion.LookRotation(dir);
			transform.rotation = Quaternion.Lerp(transform.rotation, quat, 20 * Time.deltaTime);
		}
	}

	void OnHitEvent()
	{
		if(_lockTarget != null)
        {
			Stat targetStat = _lockTarget.GetComponent<Stat>();
			PlayerStat myStat = gameObject.GetComponent<PlayerStat>();
			int damage = Mathf.Max(0, myStat.Attack - targetStat.Defense);
			targetStat.Hp -= damage;
        }

		if (_stopSkill)
			State = Define.State.Idle;
		else
			State = Define.State.Skill;
		
	}

	void OnMouseEvent(Define.MouseEvent evt)
	{
		switch (State)
		{
			case Define.State.Idle:
				OnMouseEvent_IdleRun(evt);
				break;
			case Define.State.Moving:
				OnMouseEvent_IdleRun(evt);
				break;
			case Define.State.Skill:
				{
					if (evt == Define.MouseEvent.PointerUp)
						_stopSkill = true;
				}
				break;
		}
	}

	void OnMouseEvent_IdleRun(Define.MouseEvent evt)
	{
		RaycastHit hit;
		Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
		bool raycastHit = Physics.Raycast(ray, out hit, 100.0f, _mask);

		switch (evt)
		{
			case Define.MouseEvent.PointerDown:
				{
					if (raycastHit)
					{
						_destPos = hit.point;
						State = Define.State.Moving;
						_stopSkill = false;

						if (hit.collider.gameObject.layer == (int)Define.Layer.Monster)
							_lockTarget = hit.collider.gameObject;
						else
							_lockTarget = null;
					}
				}
				break;
			case Define.MouseEvent.Press:
				{
					if (_lockTarget == null && raycastHit)
						_destPos = hit.point;
				}
				break;
			case Define.MouseEvent.PointerUp:
				_stopSkill = true;
				break;
		}
	}
}
{% endhighlight %}

* Define.cs
{% highlight C# %}
public class Define
{
    public enum State
	{
		Die,
		Moving,
		Idle,
		Skill,
	}
  ...
}
{% endhighlight %}

* Scripts\Controllers\BaseController.cs
{% highlight C# %}
public abstract class BaseController : MonoBehaviour
{
	[SerializeField]
	protected Vector3 _destPos;

	[SerializeField]
	protected Define.State _state = Define.State.Idle;

	[SerializeField]
	protected GameObject _lockTarget;

	public virtual Define.State State
	{
		get { return _state; }
		set
		{
			_state = value;

			Animator anim = GetComponent<Animator>();
			switch (_state)
			{
				case Define.State.Die:
					break;
				case Define.State.Idle:
					anim.CrossFade("WAIT", 0.1f);
					break;
				case Define.State.Moving:
					anim.CrossFade("RUN", 0.1f);
					break;
				case Define.State.Skill:
					anim.CrossFade("ATTACK", 0.1f, -1, 0);
					break;
			}
		}
	}

    private void Start()
    {
		Init();
    }

	void Update()
	{
		switch (State)
		{
			case Define.State.Die:
				UpdateDie();
				break;
			case Define.State.Moving:
				UpdateMoving();
				break;
			case Define.State.Idle:
				UpdateIdle();
				break;
			case Define.State.Skill:
				UpdateSkill();
				break;
		}
	}

	public abstract void Init();

	protected virtual void UpdateDie() { }
	protected virtual void UpdateMoving() { }
	protected virtual void UpdateIdle() { }
	protected virtual void UpdateSkill() { }
}
{% endhighlight %}

* Scripts\Controllers\MonsterController.cs
{% highlight C# %}
public class MonsterController : BaseController
{
    Stat _stat;

    [SerializeField]
    float _scanRange = 10;
    [SerializeField]
    float _attackRange = 2;

    public override void Init()
    {
        _stat = gameObject.GetComponent<Stat>();

        if(gameObject.GetComponentInChildren<UI_HPBar>() == null)
            Managers.UI.MakeWorldSpaceUI<UI_HPBar>(transform);
    }

    protected override void UpdateIdle()
    {
        GameObject player = GameObject.FindGameObjectWithTag("Player");
        if (player == null)
            return;

        float distance = (player.transform.position - transform.position).magnitude;
        if(distance <= _scanRange)
        {
            _lockTarget = player;
            State = Define.State.Moving;
            return;
        }
    }

    protected override void UpdateMoving()
    {
        if (_lockTarget != null)
        {
            _destPos = _lockTarget.transform.position;
            float distance = (_destPos - transform.position).magnitude;
            if (distance <= _attackRange)
            {
                NavMeshAgent nma = gameObject.GetOrAddComponent<NavMeshAgent>();
                nma.SetDestination(transform.position);
                State = Define.State.Skill;
                return;
            }
        }

        Vector3 dir = _destPos - transform.position;
        if (dir.magnitude < 0.1f)
        {
            State = Define.State.Idle;
        }
        else
        {
            NavMeshAgent nma = gameObject.GetOrAddComponent<NavMeshAgent>();
            nma.SetDestination(_destPos);
            nma.speed = _stat.MoveSpeed;

            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 20 * Time.deltaTime);
        }
    }

    protected override void UpdateSkill()
    {
        if (_lockTarget != null)
        {
            Vector3 dir = _lockTarget.transform.position - transform.position;
            Quaternion quat = Quaternion.LookRotation(dir);
            transform.rotation = Quaternion.Lerp(transform.rotation, quat, 20 * Time.deltaTime);
        }
    }

    void OnHitEvent()
    {
        if(_lockTarget != null)
        {
            Stat targetStat = _lockTarget.GetComponent<Stat>();
            Stat myStat = gameObject.GetComponent<Stat>();
            int damage = Mathf.Max(0, myStat.Attack - targetStat.Defense);
            targetStat.Hp -= damage;

            if(targetStat.Hp > 0)
            {
                float distance = (_lockTarget.transform.position - transform.position).magnitude;
                if (distance <= _attackRange)
                    State = Define.State.Skill;
                else
                    State = Define.State.Moving;
            }
            else
            {
                State = Define.State.Idle;
            }
        }
        else
        {
            State = Define.State.Idle;
        }
    }
}
{% endhighlight %}

## Animation

* MonsterAnimController

<figure>
  <a href="/assets/img/posts/unity_minirpg/19.jpg"><img src="/assets/img/posts/unity_minirpg/19.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

## Components
  - Remove NavMeshAgent Component from player
  - Add `player` tag in player

<figure class="half">
  <a href="/assets/img/posts/unity_minirpg/20.jpg"><img src="/assets/img/posts/unity_minirpg/20.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/21.jpg"><img src="/assets/img/posts/unity_minirpg/21.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Monster.mp4" frameborder="0"> </iframe>

# Destroy
  - you cannot make player to null directly
  - because other managers or componenets still use player

## Prefab

* Player
  - remove original player prefab and create new player prefab(Drag and Drop hierarchy object to Prefabs folder)

* Monster
  - remove original monster prefab and create new monster prefab(Drag and Drop hierarchy object to Prefabs folder)

## Componenets

* Define.cs
{% highlight C# %}
public class Define
{
    public enum WorldObject
    {
        Unkown,
        Player,
        Monster,
    }
    ...
}
{% endhighlight %}

* BaseController.cs
{% highlight C# %}
public abstract class BaseController : MonoBehaviour
{
	...

	public Define.WorldObject WorldObjectType { get; protected set; } = Define.WorldObject.Unkown;
  ...
}
{% endhighlight %}

* Manager.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    static Managers s_instance;
    static Managers Instance{ get { Init(); return s_instance; } }

    #region Contents
    GameManagerEx _game = new GameManagerEx();

    public static GameManagerEx Game { get { return Instance._game; } }
    #endregion

    #region Core
    DataManager _data = new DataManager();
    InputManager _input = new InputManager();
    PoolManager _pool = new PoolManager();
    ResourceManager _resource = new ResourceManager();
    SceneManagerEx _scene = new SceneManagerEx();
    SoundManager _sound = new SoundManager();
    UIManager _ui = new UIManager();
    #endregion
    ...
}
{% endhighlight %}

* MonsterController.cs
{% highlight C# %}
public class MonsterController : BaseController
{
    ...
    public override void Init()
    {
        WorldObjectType = Define.WorldObject.Monster;
       ...
    }
    ...

    void OnHitEvent()
    {
        if(_lockTarget != null)
        {
            Stat targetStat = _lockTarget.GetComponent<Stat>();
            int damage = Mathf.Max(0, _stat.Attack - targetStat.Defense);
            targetStat.Hp -= damage;

            if (targetStat.Hp <= 0)
            {
                Managers.Game.Despawn(targetStat.gameObject);
            }
            ...
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : BaseController
{
    public  override void  Init()
    {
		  WorldObjectType = Define.WorldObject.Player;
		  ...
	  }
    ...
}
{% endhighlight %}

* Extension.cs
  - only null checking is not enough
  - because player has poolable component
  - so you need activate cheking

{% highlight C# %}
public static class Extension
{
    ...

    public static bool IsValid(this GameObject go)
    {
        return go != null && go.activeSelf;
    }
}
{% endhighlight %}

* CameraController.cs
{% highlight C# %}
public class CameraController : MonoBehaviour
{
    ...

    [SerializeField]
    GameObject _player = null;

    public void SetPlayer(GameObject player) { _player = player; }
    ...

    void LateUpdate()
    {
        RaycastHit hit;
        if (_mode == Define.CameraMode.QuaterView)
        {
            if(_player.IsValid() == false)
            {
                return;
            }
            ...
        }
    }
    ...
}
{% endhighlight %}

* Scripts\Managers\Contents\GameManagerEx.cs
  - if you make GameManager.cs, then the icon is wheel

{% highlight C# %}
public class GameManagerEx
{
    GameObject _player;
    HashSet<GameObject> _monsters = new HashSet<GameObject>();

    public GameObject Spawn(Define.WorldObject type, string path, Transform parent = null)
    {
        GameObject go = Managers.Resource.Instantiate(path, parent);

        switch (type)
        {
            case Define.WorldObject.Monster:
                _monsters.Add(go);
                break;
            case Define.WorldObject.Player:
                _player = go;
                break;
        }

        return go;
    }

    public Define.WorldObject GetWorldObjectType(GameObject go)
    {
        BaseController bc = go.GetComponent<BaseController>();

        if (bc == null)
            return Define.WorldObject.Unkown;

        return bc.WorldObjectType;
        
    }

    public void Despawn(GameObject go)
    {
        Define.WorldObject type = GetWorldObjectType(go);

        switch (type)
        {
            case Define.WorldObject.Monster:
                {
                    if (_monsters.Contains(go))
                        _monsters.Remove(go);
                }
                break;
            case Define.WorldObject.Player:
                if (_player == go)
                    _player = null;
                break;

        }

        Managers.Resource.Destroy(go);
    }
}
{% endhighlight %}



* Folder Organization

<figure>
  <a href="/assets/img/posts/unity_minirpg/22.jpg"><img src="/assets/img/posts/unity_minirpg/22.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/23.jpg"><img src="/assets/img/posts/unity_minirpg/23.jpg"></a>
  <a href="/assets/img/posts/unity_minirpg/24.jpg"><img src="/assets/img/posts/unity_minirpg/24.jpg"></a>
	<figcaption>Mini RPG Unity</figcaption>
</figure>

### Test

* GameScene.cs
{% highlight C# %}
protected override void Init()
{
    ...

    GameObject player = Managers.Game.Spawn(Define.WorldObject.Player, "UnityChan");
    Camera.main.gameObject.GetOrAddComponent<CameraController>().SetPlayer(player);
    Managers.Game.Spawn(Define.WorldObject.Monster, "Knight");
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Destroy.mp4" frameborder="0"> </iframe>

# Level

## Camera Zoom

* CameraController.cs
  - when player is behind buildings, camera will zoom player
{% highlight C# %}
void LateUpdate()
{
    ...
    if (_mode == Define.CameraMode.QuaterView)
    {
        ...

        if(Physics.Raycast(_player.transform.position, _delta, out hit, _delta.magnitude, 1<<(int)Define.Layer.Block))
        {
            float dist = (hit.point - _player.transform.position).magnitude * 0.8f;
            transform.position = _player.transform.position + _delta.normalized * dist + Vector3.up * 1.5f;
        }
        ...
    }
}
{% endhighlight %}

## Leveling

* GameManagerEx.cs
{% highlight C# %}
public class GameManagerEx
{
    ...
    public GameObject GetPlayer() { return _player; }
    ...
}
{% endhighlight %}

* StatData.json
  - for player
{% highlight json %}
{
  "stats": [
    {
      "level": "1",
      "maxHp": "200",
      "attack": "20",
      "totalExp": "0"
    },
    {
      "level": "2",
      "maxHp": "250",
      "attack": "25",
      "totalExp": "10"
    },
    {
      "level": "3",
      "maxHp": "300",
      "attack": "30",
      "totalExp": "20"
    }
  ]
}
{% endhighlight %}

* Stat.cs
{% highlight C# %}
public class Stat : MonoBehaviour
{
    ...
    public void Start()
    {
        _level = 1;
        _hp = 100;
        _maxhp = 100;
        _attack = 10;
        _defense = 5;
        _moveSpeed = 5.0f;
    }

    public virtual void OnAttacked(Stat attacker)
    {
        int damage = Mathf.Max(0, attacker.Attack - Defense);
        Hp -= damage;

        if (Hp <= 0)
        {
            Hp = 0;
            OnDead(attacker);
        }
    }

    protected virtual void OnDead(Stat attacker)
    {
        PlayerStat playerStat = attacker as PlayerStat;
        if (playerStat != null)
        {
            playerStat.Exp += 15;
        }

        Managers.Game.Despawn(gameObject);
    }
}
{% endhighlight %}

* PlayerStat.cs
{% highlight C# %}
public class PlayerStat : Stat
{
    ...
    public int Exp 
    { 
        get { return _exp; }
        set 
        {
            _exp = value;
            int level = Level;
            while (true)
            {
                Data.Stat stat;
                if (Managers.Data.StatDict.TryGetValue(level + 1, out stat) == false)
                    break;
                if (_exp < stat.totalExp)
                    break;
                level++;
            }

            if(level != Level)
            {
                Debug.Log("Level UP!");
                Level = level;
                SetStat(Level);
            }
        } 
    }
    ...

    public void Start()
    {
        _level = 1;
        _exp = 0;
        _defense = 5;
        _moveSpeed = 5.0f;
        _gold = 0;

        SetStat(_level);
    }

    public void SetStat(int level)
    {
        Dictionary<int, Data.Stat> dict = Managers.Data.StatDict;
        Data.Stat stat = dict[level];

        _hp = stat.maxHp;
        _maxhp = stat.maxHp;
        _attack = stat.attack;
    }

    protected override void OnDead(Stat attacker)
    {
        Debug.Log("Player Dead");
    }
}
{% endhighlight %}

* MonsterController.cs
{% highlight C# %}
public class MonsterController : BaseController
{
    Stat _stat;
    ...

    protected override void UpdateIdle()
    {
        GameObject player = Managers.Game.GetPlayer();
        if (player == null)
            return;

        float distance = (player.transform.position - transform.position).magnitude;
        if(distance <= _scanRange)
        {
            _lockTarget = player;
            State = Define.State.Moving;
            return;
        }
    }

    ...
    void OnHitEvent()
    {
        if(_lockTarget != null)
        {
            Stat targetStat = _lockTarget.GetComponent<Stat>();
            targetStat.OnAttacked(_stat);
            ...
        }
        ...
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : BaseController
{
	  ...

	  void OnHitEvent()
	  {
	  	  if(_lockTarget != null)
        {
		    	Stat targetStat = _lockTarget.GetComponent<Stat>();
			    targetStat.OnAttacked(_stat);
		    }
		    ...
	  }

	  ...
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MiniRPG-Level.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
