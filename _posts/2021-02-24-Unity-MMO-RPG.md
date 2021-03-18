---
layout: post
title: "[Unity] MMO RPG"
date: 2021-02-24
excerpt: "Framework"
tags: [Unity, Game Framework]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/MMORPG-%EC%9C%A0%EB%8B%88%ED%8B%B0/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part3: 유니티 엔진</a></center>

# Patterns

## Monobehavior
  - to perceive it is `Component`

## Singleton
  - Uniqueness guaranteed

# Managers

* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    static Managers s_instance;
    static Managers Instance{ get { Init(); return s_instance; } }

    InputManager _input = new InputManager();
    ResourceManager _resource = new ResourceManager();
    public static InputManager Input { get { return Instance._input; } }
    public static ResourceManager Resource { get { return Instance._resource; } }

    void Start()
    {
        Init();
    }

    void Update()
    {
        _input.OnUpdate();
    }

    static void Init()
    {
        if (s_instance == null)
        {
            GameObject go = GameObject.Find("@Managers");
            if (go == null)
            {
                go = new GameObject { name = "@Managers" };
                go.AddComponent<Managers>();
            }
            DontDestroyOnLoad(go);
            s_instance = go.GetComponent<Managers>();
        }
    }
}
{% endhighlight %}
  - if there is not `Managers` Object, make new one
  - this new `Managers` Object stay during play
  - it links other Manager objects with each Managers

* InputManager.cs
{% highlight C# %}
public class InputManager
{
    public Action KeyAction = null;
    
    public void OnUpdate()
    {
        if (Input.anyKey == false)
            return;
        if (KeyAction != null)
            KeyAction.Invoke();
    }
}
{% endhighlight %}
  - If there is key input during play, receive key input

* ResourceManager.cs
{% highlight C# %}
public class ResourceManager
{
    public T Load<T>(string path) where T: Object
    {
        return Resources.Load<T>(path);
    }

    public GameObject Instantiate(string path, Transform parent = null)
    {
        GameObject prefab = Load<GameObject>($"Prefabs/{path}");

        if (prefab == null)
        {
            Debug.Log($"Failed to load prefab: {path}");
            return null;
        }

        return Object.Instantiate(prefab, parent);
    }

    public void Destroy(GameObject go)
    {
        if (go == null)
            return;
        Object.Destroy(go);
    }
}
{% endhighlight %}
  - `Instantiate` can make GameObject
  - `Destroy` can remove GameObject
  - `Load` can load GameObject
  - to use `Resources.Load`, there should be Resources folder

# Controller

* PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    [SerializeField]
    float _speed = 10.0f;


    void Start()
    {
        Managers.Input.KeyAction -= OnKeyboard;
        Managers.Input.KeyAction += OnKeyboard;
    }

    void Update()
    {
    }

    void OnKeyboard()
    {
        if (Input.GetKey(KeyCode.W))
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.forward), 0.2f);
            transform.position += Vector3.forward * Time.deltaTime * _speed;
        }
        if (Input.GetKey(KeyCode.S))
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.back), 0.2f);
            transform.position += Vector3.back * Time.deltaTime * _speed;
        }
        if (Input.GetKey(KeyCode.A))
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.left), 0.2f);
            transform.position += Vector3.left * Time.deltaTime * _speed;
        }
        if (Input.GetKey(KeyCode.D))
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.right), 0.2f);
            transform.position += Vector3.right * Time.deltaTime * _speed;
        }
    }
}
{% endhighlight %}
  - `Quaternion.Slerp` can change direction

# Collision

## Gravity

* Player
<figure>
  <a href="/assets/img/posts/unity_mmorpg/0.jpg"><img src="/assets/img/posts/unity_mmorpg/0.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - Add `Rigidbody` and check `Use Gravity`
  - Add `Capsule Collider`
  - `Is Kinematic` can ignore all physical laws in Unity

* Plane
<figure>
  <a href="/assets/img/posts/unity_mmorpg/1.jpg"><img src="/assets/img/posts/unity_mmorpg/1.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - Add `Mesh Collider`

## Collider and Trigger

* Collider
<figure>
  <a href="/assets/img/posts/unity_mmorpg/2.jpg"><img src="/assets/img/posts/unity_mmorpg/2.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - There is `Rigidbody` on me (Is Kinematic : Off)
  - There is `Collider` on me (Is Trigger : Off)
  - There is `Collider` on other (Is Trigger : Off)

{% highlight C# %}
private void OnCollisionEnter(Collision collision)
{
    Debug.Log($"Collision @ {collision.gameObject.name} !");
}
{% endhighlight %}

* Trigger
<figure>
  <a href="/assets/img/posts/unity_mmorpg/3.jpg"><img src="/assets/img/posts/unity_mmorpg/3.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>
  
  - There are `Collider` on me and other
  - There is `Rigidbody` on one
  - There is `Is Trigger` on one

{% highlight C# %}
private void OnTriggerEnter(Collider other)
{
    Debug.Log($"Trigger @ {other.gameObject.name}!");
}
{% endhighlight %}

## Raycasting
  - shoot a raser and detect object

{% highlight C# %}
void Update()
{
        
    Vector3 look = transform.TransformDirection(Vector3.forward);
    Debug.DrawRay(transform.position + Vector3.up, look * 10, Color.red);

    RaycastHit[] hits;
    hits = Physics.RaycastAll(transform.position + Vector3.up, look, 10);

    foreach(RaycastHit hit in hits)
        Debug.Log($"Raycast {hit.collider.gameObject.name}!");
}
{% endhighlight %}

  - `TransformDirection` can change player's direction to world direction
  - `RaycastHit` can save all objects that is hitted by player's raser
  - `Physics.RaycastAll` can shoot a raser and detect objects
  - if you want to detect only one object, use `Physics.Raycast`

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Raycasting.mp4" frameborder="0"> </iframe>

## Projection
  - Local ↔ World ↔ Viewport ↔ Screen

* Screen
  - Pixel standard
{% highlight C# %}
void Update()
{
    Debug.Log(Input.mousePosition);    
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Screen.mp4" frameborder="0"> </iframe>

* Viewport
  - Pixel ratio standard
{% highlight C# %}
void Update()
{
    Debug.Log(Camera.main.ScreenToViewportPoint(Input.mousePosition));
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Viewport.mp4" frameborder="0"> </iframe>

* Camera
  - You can change view range in `Clipping Planes`
  - `Near` can change where the camera range start
  - `Far` can change where the camera range end

  <a href="/assets/img/posts/unity_mmorpg/4.jpg"><img src="/assets/img/posts/unity_mmorpg/4.jpg"></a>

## Camera Raycasting
  - camera can shoot raser and detect object

{% highlight C# %}
void Update()
{
    if (Input.GetMouseButtonDown(0))
    {
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Debug.DrawRay(Camera.main.transform.position, ray.direction * 100.0f, Color.red, 1.0f);

        RaycastHit hit;
        if (Physics.Raycast(ray, out hit, 100.0f))
            Debug.Log($"Raycast Camera @ {hit.collider.gameObject.name}");
    }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Raycasting2.mp4" frameborder="0"> </iframe>

## LayerMask
  - you can set layers(also you can se Tags)
  - from layers, you can define whether to detect object(s) or not

{% highlight C# %}
void Update()
{
    if (Input.GetMouseButtonDown(0))
    {
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Debug.DrawRay(Camera.main.transform.position, ray.direction * 100.0f, Color.red, 1.0f);

        LayerMask mask = LayerMask.GetMask("Monster") | LayerMask.GetMask("Wall");

        RaycastHit hit;
        if (Physics.Raycast(ray, out hit, 100.0f, mask))
            Debug.Log($"Raycast Camera @ {hit.collider.gameObject.tag}");
    }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-LayerMask.mp4" frameborder="0"> </iframe>

# Camera

## Follow Player
  - if camera is in player's hierarchy, camera will follow palyer
  - but the rotation is also changed, this is really messy for user

* Scripts\Utils\Define.cs
{% highlight C# %}
public class Define
{
    public enum CameraMode
    {
        QuaterView,
    }
}
{% endhighlight %}

* CameraController.cs
{% highlight C# %}
public class CameraController : MonoBehaviour
{
    [SerializeField]
    Define.CameraMode _mode = Define.CameraMode.QuaterView;

    [SerializeField]
    Vector3 _delta = new Vector3(0.0f, 6.0f, -5.0f);

    [SerializeField]
    GameObject _player = null;

    void LateUpdate()
    {
        if (_mode == Define.CameraMode.QuaterView)
        {
            transform.position = _player.transform.position + _delta;
            transform.LookAt(_player.transform);
        }
    }

    public void SetQuaterView(Vector3 delta)
    {
        _mode = Define.CameraMode.QuaterView;
        _delta = delta;
    }
}
{% endhighlight %}

  - KeyInput and Camera transform is crushed by their update time. `LateUpdate` can delay Camera transform updating time. So Camera transform is excuted after player's move


## Click Moving
  - by clicking map, player can move to destination.

* Scripts\Utils\Define.cs
{% highlight C# %}
public class Define
{
    public enum MouseEvent
    {
        Press,
        Click,
    }

    public enum CameraMode
    {
        QuaterView,
    }   
}
{% endhighlight %}

* InputManager.cs
{% highlight C# %}
public class InputManager
{
    public Action KeyAction = null;
    public Action<Define.MouseEvent> MouseAction = null;

    bool _pressed = false;
    
    public void OnUpdate()
    {
        if (Input.anyKey && KeyAction != null)
            KeyAction.Invoke();

        if(MouseAction != null)
        {
            if (Input.GetMouseButton(0))
            {
                MouseAction.Invoke(Define.MouseEvent.Press);
                _pressed = true;
            }
            else
            {
                if (_pressed)
                    MouseAction.Invoke(Define.MouseEvent.Click);
                _pressed = false;
            }
        }
    }
}
{% endhighlight %}

  - Left Mouse Click is 0 and Right Mouse Click is 1

* PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    ...
    bool _moveToDest = false;
    Vector3 _desPos;

    void Start()
    {
        ...
        Managers.Input.MouseAction += OnMouseClicked;
        Managers.Input.MouseAction -= OnMouseClicked;
    }

    void Update()
    {
        if (_moveToDest)
        {
            Vector3 dir = _desPos - transform.position;
            if(dir.magnitude < 0.0001f)
            {
                _moveToDest = false;
            }
            else
            {
                float moveDist = Mathf.Clamp(_speed * Time.deltaTime, 0, dir.magnitude);
                transform.position += dir.normalized * moveDist;
                transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 10 * Time.deltaTime);
                transform.LookAt(_desPos);
            }
        }
    }

    void OnKeyboard()
    {
        ...
        _moveToDest = false;
    }

    void OnMouseClicked(Define.MouseEvent evt)
    {
        if (evt != Define.MouseEvent.Click)
            return;

        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Debug.DrawRay(Camera.main.transform.position, ray.direction * 100.0f, Color.red, 1.0f);

        RaycastHit hit;
        if (Physics.Raycast(ray, out hit, 100.0f, LayerMask.GetMask("Wall")))
        {
            _desPos = hit.point;
            _moveToDest = true;
        }
    }
}
{% endhighlight %}

  - `magnitude` returns length of vector
  - `hit.point` returns impact point in world space where the ray hit the collider

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Click-Move.mp4" frameborder="0"> </iframe>

## Camera Position
  - if player is hidden by wall, camera position will be change

* CameraController.cs
{% highlight C# %}
void LateUpdate()
{
    if (_mode == Define.CameraMode.QuaterView)
    {
        RaycastHit hit;
        if(Physics.Raycast(_player.transform.position, _delta, out hit, _delta.magnitude, LayerMask.GetMask("Wall")))
        {
            float dist = (hit.point - _player.transform.position).magnitude * 0.8f;
            transform.position = _player.transform.position + _delta.normalized * dist;
        }
        else
        {
            transform.position = _player.transform.position + _delta;
            transform.LookAt(_player.transform);
        }
    }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Camera-Wall.mp4" frameborder="0"> </iframe>

# Animation

## WAIT & RUN

* Animation Controller
  - `Animation Controller` can make animation
  - customize Animation
  - Add controller in Animator

<figure class="third">
  <a href="/assets/img/posts/unity_mmorpg/5.jpg"><img src="/assets/img/posts/unity_mmorpg/5.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/6.jpg"><img src="/assets/img/posts/unity_mmorpg/6.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/7.jpg"><img src="/assets/img/posts/unity_mmorpg/7.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* PlayerController.cs
{% highlight C# %}
void Update()
{
    ...
    if (_moveToDest)
    {
        Animator anim = GetComponent<Animator>();
        anim.Play("RUN");
    }
    else
    {
        Animator anim = GetComponent<Animator>();
        anim.Play("WAIT");
    }
}

void OnMouseClicked(Define.MouseEvent evt)
{
    //if (evt != Define.MouseEvent.Click)
    //    return;
    ...
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Animation-Wait-Run.mp4" frameborder="0"> </iframe>

## Animation Blending
  - player can move sequencialy, not suddenly

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/8.jpg"><img src="/assets/img/posts/unity_mmorpg/8.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/9.jpg"><img src="/assets/img/posts/unity_mmorpg/9.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Blend Tree
  - blend sevral animations
  - `Threshold` sets blend values
  - when the value comes closer to threshold value, the animation will be excuted

* PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
  float wait_run_ratio;
  ...
  void Update()
  {
    ...
    if (_moveToDest)
    {
      wait_run_ratio = Mathf.Lerp(wait_run_ratio, 1, 10.0f * Time.deltaTime);
      Animator anim = GetComponent<Animator>();
      anim.SetFloat("wait_run_ratio", wait_run_ratio);
      anim.Play("WAIT_RUN");
    }
    else
    {
      wait_run_ratio = Mathf.Lerp(wait_run_ratio, 0, 10.0f * Time.deltaTime);
      Animator anim = GetComponent<Animator>();
      anim.SetFloat("wait_run_ratio", wait_run_ratio);
      anim.Play("WAIT_RUN");
    }   
  }
}
{% endhighlight %}

## State Pattern
  - manage Player state
  - from this section, we don't use keyboard KeyInput

* PlayerController.cs

{% highlight C# %}
public class PlayerController : MonoBehaviour
{
  ...
  PlayerState _state = PlayerState.Idle;

  void Start()
  {
    //Managers.Input.KeyAction -= OnKeyboard;
    //Managers.Input.KeyAction += OnKeyboard;
    ...
  }

  public enum PlayerState
  {
      Die,
      Moving,
      Idle,
  }
    
  void UpdateDie()
  {

  }

  void UpdateMoving()
  {
    Vector3 dir = _desPos - transform.position;

    if (dir.magnitude < 0.0001f)
    {
      _state = PlayerState.Idle;
    }
    else
    {
      float moveDist = Mathf.Clamp(_speed * Time.deltaTime, 0, dir.magnitude);

      transform.position += dir.normalized * moveDist;
      transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 10 * Time.deltaTime);

      wait_run_ratio = Mathf.Lerp(wait_run_ratio, 1, 10.0f * Time.deltaTime);
      Animator anim = GetComponent<Animator>();
      anim.SetFloat("wait_run_ratio", wait_run_ratio);
      anim.Play("WAIT_RUN");
    }
  }

  void UpdateIdle()
  {
    wait_run_ratio = Mathf.Lerp(wait_run_ratio, 0, 10.0f * Time.deltaTime);
    Animator anim = GetComponent<Animator>();
    anim.SetFloat("wait_run_ratio", wait_run_ratio);
    anim.Play("WAIT_RUN");
  }

  void Update()
  {
    switch (_state)
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
    }
  }

  void OnMouseClicked(Define.MouseEvent evt)
  {
    if (_state == PlayerState.Die)
      return;

    Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
    Debug.DrawRay(Camera.main.transform.position, ray.direction * 100.0f, Color.red, 1.0f);

    RaycastHit hit;
    if (Physics.Raycast(ray, out hit, 100.0f, LayerMask.GetMask("Wall")))
    {
      _desPos = hit.point;
      _state = PlayerState.Moving;
    }
  }
}
{% endhighlight %}

## State Machine
  - you can set your animation in GUI

<figure>
  <a href="/assets/img/posts/unity_mmorpg/10.jpg"><img src="/assets/img/posts/unity_mmorpg/10.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* PlayerController.cs

{% highlight C# %}
public class PlayerController : MonoBehaviour
{
  ...
  float wait_run_ratio;
  ...
  void UpdateMoving()
  {
    ...
    else
    {
      float moveDist = Mathf.Clamp(_speed * Time.deltaTime, 0, dir.magnitude);

      transform.position += dir.normalized * moveDist;
      transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 10 * Time.deltaTime);

      //wait_run_ratio = Mathf.Lerp(wait_run_ratio, 1, 10.0f * Time.deltaTime);
      Animator anim = GetComponent<Animator>();
      //anim.SetFloat("wait_run_ratio", wait_run_ratio);
      //anim.Play("WAIT_RUN");
    }
  }

  void UpdateIdle()
  {
    //wait_run_ratio = Mathf.Lerp(wait_run_ratio, 0, 10.0f * Time.deltaTime);
    Animator anim = GetComponent<Animator>();
    //anim.SetFloat("wait_run_ratio", wait_run_ratio);
    //anim.Play("WAIT_RUN");
  }
  ...
}
{% endhighlight %}

* Ordring animations
  - you can order animations in `Transitions` section

<figure>
  <a href="/assets/img/posts/unity_mmorpg/11.jpg"><img src="/assets/img/posts/unity_mmorpg/11.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Conditioning
  - you can make condition in animations

<figure class="third">
  <a href="/assets/img/posts/unity_mmorpg/12.jpg"><img src="/assets/img/posts/unity_mmorpg/12.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/13.jpg"><img src="/assets/img/posts/unity_mmorpg/13.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/14.jpg"><img src="/assets/img/posts/unity_mmorpg/14.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

## KeyFrame Animation
  - you can make KeyFrame Animation

* Animation Tool

<figure>
  <a href="/assets/img/posts/unity_mmorpg/15.jpg"><img src="/assets/img/posts/unity_mmorpg/15.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - Click object

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/16.jpg"><img src="/assets/img/posts/unity_mmorpg/16.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/17.jpg"><img src="/assets/img/posts/unity_mmorpg/17.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - Add all Transform Properties

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/18.jpg"><img src="/assets/img/posts/unity_mmorpg/18.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/19.jpg"><img src="/assets/img/posts/unity_mmorpg/19.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - change position at 60 sec and 120 sec
  
<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/20.jpg"><img src="/assets/img/posts/unity_mmorpg/20.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/21.jpg"><img src="/assets/img/posts/unity_mmorpg/21.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Animation-KeyFrame.mp4" frameborder="0"> </iframe>

<figure>
  <a href="/assets/img/posts/unity_mmorpg/22.jpg"><img src="/assets/img/posts/unity_mmorpg/22.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Animation-KeyFrame-Curve.mp4" frameborder="0"> </iframe>

  - keyframe recording mode

<figure>
  <a href="/assets/img/posts/unity_mmorpg/23.jpg"><img src="/assets/img/posts/unity_mmorpg/23.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Animation-KeyFrame-Recording.mp4" frameborder="0"> </iframe>

## Animation Event

* Animation Event
  - Add Animation Event

<figure>
  <a href="/assets/img/posts/unity_mmorpg/24.jpg"><img src="/assets/img/posts/unity_mmorpg/24.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Scripts\CubeEventTest.cs
{% highlight C# %}
public class CubeEventTest : MonoBehaviour
{
    void TestEventCallback()
    {
        Debug.Log("Evnet Received!");
    }
}
{% endhighlight %}

  - in added animation event, change function

<figure>
  <a href="/assets/img/posts/unity_mmorpg/25.jpg"><img src="/assets/img/posts/unity_mmorpg/25.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* change `unitychan_RUN00_F`
  - Add Event

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/26.jpg"><img src="/assets/img/posts/unity_mmorpg/26.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/27.jpg"><img src="/assets/img/posts/unity_mmorpg/27.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* PlayerController.cs
{% highlight C# %}
void OnRunEvent()
{
    Debug.Log("walk walk");
}
{% endhighlight %}


## send reference value
* change `unitychan_RUN00_F`
  - change string

<figure>
  <a href="/assets/img/posts/unity_mmorpg/28.jpg"><img src="/assets/img/posts/unity_mmorpg/28.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* PlayerController.cs
{% highlight C# %}
void OnRunEvent(string a)
{
    Debug.Log($"walk{a} walk{a}");
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Animation-KeyFrame-Event.mp4" frameborder="0"> </iframe>

# UI

## Pivot
  - it is central point of UI object
  - object rotate round pivot when rotation is changed

## Anchor
  - set ratio and size of object when screen size is changed

## UI Anomatification

### Object Detection

* Detect Objects
  - change object name

<figure>
  <a href="/assets/img/posts/unity_mmorpg/30.jpg"><img src="/assets/img/posts/unity_mmorpg/30.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Utils\Util.cs

{% highlight C# %}
public class Util
{
    public static T FindChild<T>(GameObject go, string name = null, bool recursive = false) where T: UnityEngine.Object
    {
        if (go == null)
            return null;

        if(recursive == false)
        {
            for (int i = 0; i < go.transform.childCount; i++)
            {
                Transform transform = go.transform.GetChild(i);
                if(string.IsNullOrEmpty(name) || transform.name == name)
                {
                    T component = transform.GetComponent<T>();
                    if (component != null)
                        return component;
                }
            }
        }
        else
        {
            foreach(T component in go.GetComponentsInChildren<T>())
            {
                if (string.IsNullOrEmpty(name) || component.name == name)
                    return component;
            }
        }

        return null;
    }
}
{% endhighlight %}

* Scripts\UI\UI_Button.cs
{% highlight C# %}
public class UI_Button : MonoBehaviour
{
    // Text _text;
    Dictionary<Type, UnityEngine.Object[]> _objects = new Dictionary<Type, UnityEngine.Object[]>();

    enum Buttons
    {
        PointButton,
    }

    enum Texts
    {
        PointText,
        ScoreText,
    }

    private void Start()
    {
        Bind<Button>(typeof(Buttons));
        Bind<Text>(typeof(Texts));
    }

    void Bind<T>(Type type) where T: UnityEngine.Object
    {
        string [] names = Enum.GetNames(type);

        UnityEngine.Object[] objects = new UnityEngine.Object[names.Length];
        _objects.Add(typeof(T), objects);

        for(int i=0; i<names.Length; i++)
        {
            objects[i] = Util.FindChild<T>(gameObject, names[i], true);
        }
    }

    int _score = 0;

    public void OnButtonClicked()
    {
        _score++;
        //_text.text = $"Score : {_score}";
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/unity_mmorpg/29.jpg"><img src="/assets/img/posts/unity_mmorpg/29.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

### Base UI

* Create Base UI
  - use prefab

* Delete in Hierarchy
  - first, delete `UI_Button` in prefeb
  - drag `UI_Button` in Hierarchy to prefab
  - delete `UI_Button` in Hierarchy
  - this step is to change `UI_Button` prefeb

<figure>
  <a href="/assets/img/posts/unity_mmorpg/31.jpg"><img src="/assets/img/posts/unity_mmorpg/31.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* UI_Button.cs

{% highlight C# %}
public class UI_Button : UI_Base
{
  ...
    enum GameObjects
    {
        TestObject,
    }

    private void Start()
    {
        ...
        Bind<GameObject>(typeof(GameObjects));

        GetText((int)Texts.ScoreText).text = "Bind Test";
    }
    ...
}
{% endhighlight %}

* Script\UI\UI_Base.cs
{% highlight C# %}
public class UI_Base : MonoBehaviour
{
    Dictionary<Type, UnityEngine.Object[]> _objects = new Dictionary<Type, UnityEngine.Object[]>();

    protected void Bind<T>(Type type) where T : UnityEngine.Object
    {
        string[] names = Enum.GetNames(type);

        UnityEngine.Object[] objects = new UnityEngine.Object[names.Length];
        _objects.Add(typeof(T), objects);

        for (int i = 0; i < names.Length; i++)
        {
            if (typeof(T) == typeof(GameObject))
                objects[i] = Util.FindChild(gameObject, names[i], true);
            else
                objects[i] = Util.FindChild<T>(gameObject, names[i], true);

            if (objects[i] == null)
                Debug.Log($"Faild to bind{name[i]}");
        }
    }

    protected T Get<T>(int idx) where T : UnityEngine.Object
    {
        UnityEngine.Object[] objects = null;
        if (_objects.TryGetValue(typeof(T), out objects) == false)
            return null;

        return objects[idx] as T;
    }

    protected Text GetText(int idx) { return Get<Text>(idx); }
    protected Button GetButton(int idx) { return Get<Button>(idx); }
    protected Image GetImage(int idx) { return Get<Image>(idx); }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    Managers.Resource.Instantiate("UI/UI_Button");
}
{% endhighlight %}

### Event Handler

* Drag Image
  - add `UI_Button` in Hierarchy
  - add `ItemIcon` Image in `UI_Button`

<figure>
  <a href="/assets/img/posts/unity_mmorpg/32.jpg"><img src="/assets/img/posts/unity_mmorpg/32.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    //Managers.Resource.Instantiate("UI/UI_Button");
}
{% endhighlight %}

* Script\UI\UI_EventHandler.cs
  - add this component in `ItemIcon` Image

{% highlight C# %}
public class UI_EventHandler : MonoBehaviour, IBeginDragHandler, IDragHandler
{
    public Action<PointerEventData> OnBeginDragHandler = null;
    public Action<PointerEventData> OnDragHandler = null;

    public void OnBeginDrag(PointerEventData eventData)
    {
        if (OnBeginDragHandler != null)
            OnBeginDragHandler.Invoke(eventData);
    }

    public void OnDrag(PointerEventData eventData)
    {
        if (OnDragHandler != null)
            OnDragHandler.Invoke(eventData);
    }
}
{% endhighlight %}

* UI_Button.cs
{% highlight C# %}
enum Images
{
    ItemIcon,
}

private void Start()
{
    ...
    Bind<Image>(typeof(Images));

    ...
    GameObject go = GetImage((int)Images.ItemIcon).gameObject;
    UI_EventHandler evt = go.GetComponent<UI_EventHandler>();
    evt.OnDragHandler += ((PointerEventData data) => { evt.gameObject.transform.position = data.position; });
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-UI-Drag.mp4" frameborder="0"> </iframe>

### UI Event

* Add Script Component
  - by code, add script component in each game object atomatically

* UI_Base.cs
{% highlight C# %}
public static void AddUIEvent(GameObject go, Action<PointerEventData> action, Define.UIEvent type = Define.UIEvent.Click)
    {
        UI_EventHandler evt = Util.GetOrAddComponent<UI_EventHandler>(go);

        switch (type)
        {
            case Define.UIEvent.Click:
                evt.OnClickHandler -= action;
                evt.OnClickHandler += action;
                break;
            case Define.UIEvent.Drag:
                evt.OnDragHandler -= action;
                evt.OnDragHandler += action;
                break;
        }
        evt.OnDragHandler += ((PointerEventData data) => { evt.gameObject.transform.position = data.position; });
    }
{% endhighlight %}

* Define.cs
{% highlight C# %}
public class Define
{
    public enum UIEvent
    {
        Click,
        Drag,
    }
    ...
}
{% endhighlight %}

* Util.cs
{% highlight C# %}
public class Util
{
    public static T GetOrAddComponent<T>(GameObject go) where T : UnityEngine.Component
    {
        T component = go.GetComponent<T>();
        if (component == null)
            component = go.AddComponent<T>();
        return component;
    }
    ...
}
{% endhighlight %}

* UI_EventHandler.cs
{% highlight C# %}
public class UI_EventHandler : MonoBehaviour, IPointerClickHandler,IDragHandler
{
    public Action<PointerEventData> OnClickHandler = null;
    public Action<PointerEventData> OnDragHandler = null;

    public void OnPointerClick(PointerEventData eventData)
    {
        if (OnClickHandler != null)
            OnClickHandler.Invoke(eventData);
    }

    public void OnDrag(PointerEventData eventData)
    {
        if (OnDragHandler != null)
            OnDragHandler.Invoke(eventData);
    }
}
{% endhighlight %}

* UI_Button.cs
{% highlight C# %}
private void Start()
{
    ...
    GetButton((int)Buttons.PointButton).gameObject.AddUIEvent(OnButtonClicked);
    GameObject go = GetImage((int)Images.ItemIcon).gameObject;
    AddUIEvent(go, (PointerEventData data) => { go.transform.position = data.position; }, Define.UIEvent.Drag);
    }

    ...

public void OnButtonClicked(PointerEventData data)
{
    _score++;
    GetText((int)Texts.ScoreText).text = $"Score: {_score}";
}
{% endhighlight %}

* Extension.cs
  - extense function

{% highlight C# %}
public static class Extension
{
    public static void AddUIEvent(this GameObject go, Action<PointerEventData> action, Define.UIEvent type = Define.UIEvent.Click)
    {
        UI_Base.AddUIEvent(go, action, type);
    }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-UI-Click.mp4" frameborder="0"> </iframe>

## UI Manager

### Create Popup

* Popup UI
  - create Popup in code

* organize folder
  - Create `Popup` folder in Prefabs\UI and Scripts\UI
  - Dreate `Scene` folder in Prefabs\UI and Scripts\UI
  - Move `UI_Button` to  Prefabs\UI\Popup and Scripts\UI\Popup

* Scripts\UI\Popup\UI_Popup.cs
{% highlight C# %}
public class UI_Popup : UI_Base{ }
{% endhighlight %}

* Managers\UIManagers.cs
  - use stack to manage popup
{% highlight C# %}
public class UIManager
{
    int _order = 0;

    Stack<UI_Popup> _popupStack = new Stack<UI_Popup>();

    public T ShowPopupUI<T>(string name = null) where T : UI_Popup
    {
        if (string.IsNullOrEmpty(name))
            name = typeof(T).Name;

        GameObject go = Managers.Resource.Instantiate($"UI/Popup/{name}");

        T popup = Util.GetOrAddComponent<T>(go);
        _popupStack.Push(popup);

        return popup;
    }

    public void ClosePopupUI(UI_Popup popup)
    {
        if (_popupStack.Count == 0)
            return;
        if(_popupStack.Peek() != popup)
        {
            Debug.Log("Close Popup Failed!");
            return;
        }

        ClosePopupUI();
    }

    public void ClosePopupUI()
    {
        if (_popupStack.Count == 0)
            return;

        UI_Popup popup = _popupStack.Pop();
        Managers.Resource.Destroy(popup.gameObject);
        popup = null;

        _order--;
    }

    public void CloseAllPopupUI()
    {
        while (_popupStack.Count > 0)
            ClosePopupUI();
    }
}
{% endhighlight %}

* UI_Button.cs
{% highlight C# %}
public class UI_Button : UI_Popup
{
  ...
}
{% endhighlight %}

* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
  ...
  UIManager _ui = new UIManager();

  ...
  public static UIManager UI { get { return Instance._ui; } }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    UI_Button ui = Managers.UI.ShowPopupUI<UI_Button>();
    Managers.UI.ClosePopupUI(ui);
}
{% endhighlight %}

### Popup ordering

* Popup Order
  - Popup Ordering is essential when showing and closing popup
  - Popup Blocker is essential if there is several Popups behind

* UIManager.cs
{% highlight C# %}
public class UIManager
{
    int _order = 10;
    ...
    UI_Scene _sceneUI = null;

    public GameObject Root
    {
        get
        {
            GameObject root = GameObject.Find("@UI_Root");
            if (root == null)
                root = new GameObject { name = "@UI_Root" };
            return root;
        }
    }
    public void SetCanvas(GameObject go, bool sort = true)
    {
        Canvas canvas = Util.GetOrAddComponent<Canvas>(go);
        canvas.renderMode = RenderMode.ScreenSpaceOverlay;
        canvas.overrideSorting = true;

        if (sort)
        {
            canvas.sortingOrder = _order;
            _order++;
        }
  }

    public T ShowSceneUI<T>(string name = null) where T : UI_Scene
    {
        if (string.IsNullOrEmpty(name))
            name = typeof(T).Name;

        GameObject go = Managers.Resource.Instantiate($"UI/Scene/{name}");
        T sceneUI = Util.GetOrAddComponent<T>(go);
        _sceneUI = sceneUI;

        go.transform.SetParent(Root.transform);

        return sceneUI;
    }

    public T ShowPopupUI<T>(string name = null) where T : UI_Popup
    {
        if (string.IsNullOrEmpty(name))
            name = typeof(T).Name;

        GameObject go = Managers.Resource.Instantiate($"UI/Popup/{name}");
        T popup = Util.GetOrAddComponent<T>(go);
        _popupStack.Push(popup);

        go.transform.SetParent(Root.transform);

        return popup;
    }
}
{% endhighlight %}

* UI_Popup.cs
{% highlight C# %}
public class UI_Popup : UI_Base
{
    public virtual void init()
    {
        Managers.UI.SetCanvas(gameObject, true);
    }

    public virtual void ClosePopupUI()
    {
        Managers.UI.ClosePopupUI(this);
    }
}
{% endhighlight %}

* Scripts\UI\UI_Scene.cs
{% highlight C# %}
public class UI_Scene : UI_Base
{
    public virtual void init()
    {
        Managers.UI.SetCanvas(gameObject, false);
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    //Managers.UI.ClosePopupUI(ui);
}
{% endhighlight %}

* UI_Button.cs
{% highlight C# %}
void Start()
{
    init();
}
    
public override void init()
{
    base.init();

    Bind<Button>(typeof(Buttons));
    Bind<Text>(typeof(Texts));
    Bind<GameObject>(typeof(GameObjects));
    Bind<Image>(typeof(Images));

    GetButton((int)Buttons.PointButton).gameObject.AddUIEvent(OnButtonClicked);
    GameObject go = GetImage((int)Images.ItemIcon).gameObject;
    AddUIEvent(go, (PointerEventData data) => { go.transform.position = data.position; }, Define.UIEvent.Drag);
}
{% endhighlight %}

* Blocker
  - Blocker blocks behind popup moving
  - Create Image and set size to cover whole UI_Button
  - set alpha value of color to 0
  - set Hierarchy like below

<figure>
  <a href="/assets/img/posts/unity_mmorpg/33.jpg"><img src="/assets/img/posts/unity_mmorpg/33.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-UI-Popup.mp4" frameborder="0"> </iframe>

## Inventory

### Panel
  - Before starting, you should delete `UI_Button` in Hierarchy
  - and change PlayerController.cs

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    //UI_Button ui = Managers.UI.ShowPopupUI<UI_Button>();
}
{% endhighlight %}

* Asset Store
  - search `Unity Samples : UI`
  - import only `Textures and Sprites`

<figure class="third">
  <a href="/assets/img/posts/unity_mmorpg/34.jpg"><img src="/assets/img/posts/unity_mmorpg/34.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/35.jpg"><img src="/assets/img/posts/unity_mmorpg/35.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/36.jpg"><img src="/assets/img/posts/unity_mmorpg/36.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - and down load `2D Sprite` in [window]-[Package Manager]

<figure>
  <a href="/assets/img/posts/unity_mmorpg/37.jpg"><img src="/assets/img/posts/unity_mmorpg/37.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

### Binding

* Inventory Panel
  - Create Pannel and Change Canvas name to `UI_Inven`
  - and use `UIPanel` in  [Textures and Sprite]-[Rounded UI] to Panel

<figure>
  <a href="/assets/img/posts/unity_mmorpg/38.jpg"><img src="/assets/img/posts/unity_mmorpg/38.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - create Panel in Panel and change name to `UI_Inven_Item`
  - create Image in `UI_Inven_Item`
  - use `icon1` in [Textures and Sprite]-[Decoration] to Image

<figure>
  <a href="/assets/img/posts/unity_mmorpg/39.jpg"><img src="/assets/img/posts/unity_mmorpg/39.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - Add `Grid Layout Group` in Panel

<figure>
  <a href="/assets/img/posts/unity_mmorpg/40.jpg"><img src="/assets/img/posts/unity_mmorpg/40.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - prefab `UI_Inven_Item` and Duplicate this
  - prefab `UI_Inven`

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/42.jpg"><img src="/assets/img/posts/unity_mmorpg/42.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/41.jpg"><img src="/assets/img/posts/unity_mmorpg/41.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Change Prefab
  - in `UI_Inven` prefab, change panel name to `GridPanel`

<figure>
  <a href="/assets/img/posts/unity_mmorpg/43.jpg"><img src="/assets/img/posts/unity_mmorpg/43.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - in `UI_Inven_Item` prefab, change image name to `ItemIcon`
  - create Text and change name to `ItemNameText`

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/44.jpg"><img src="/assets/img/posts/unity_mmorpg/44.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/45.jpg"><img src="/assets/img/posts/unity_mmorpg/45.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Scripts\UI\Scene\UI_Inven.cs
{% highlight C# %}
public class UI_Inven : UI_Scene
{
    enum GameObjects
    {
        GridPanel,
    }

    void Start()
    {
        Init();
    }

    public override void Init()
    {
        base.Init();

        Bind<GameObject>(typeof(GameObjects));
        GameObject gridPanel = Get<GameObject>((int)GameObjects.GridPanel);

        foreach (Transform child in gridPanel.transform)
            Managers.Resource.Destroy(child.gameObject);

        for(int i=0; i<8; i++)
        {
            GameObject item = Managers.Resource.Instantiate("UI/Scene/UI_Inven_Item");
            item.transform.SetParent(gridPanel.transform);

            UI_Inven_Item invenItem = Util.GetOrAddComponent<UI_Inven_Item>(item);
            invenItem.SetInfo($"Sward {i}");
        }
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    Managers.UI.ShowSceneUI<UI_Inven>();
}
{% endhighlight %}

* UI_Base.cs
  - Making Init in here is better

{% highlight C# %}
public abstract class UI_Base : MonoBehaviour
{
    ...
    public abstract void Init();
    ...
}
{% endhighlight %}

* UI_Popup.cs
{% highlight C# %}
public class UI_Popup : UI_Base
{
    public override void Init()
    {
        Managers.UI.SetCanvas(gameObject, true);
    }
}
{% endhighlight %}

* UI_Scene.cs
{% highlight C# %}
public class UI_Scene : UI_Base
{
    public override void Init()
    {
        Managers.UI.SetCanvas(gameObject, false);
    }
}
{% endhighlight %}

* Scripts\UI\Scene\UI_Inven_Item.cs
{% highlight C# %}
public class UI_Inven_Item : UI_Base
{
    enum GameObjects
    {
        ItemIcon,
        ItemNameText,
    }

    string _name;

    void Start()
    {
        Init();
    }

    public override void Init()
    {
        Bind<GameObject>(typeof(GameObjects));

        Get<GameObject>((int)GameObjects.ItemNameText).GetComponent<Text>().text = _name;
        Get<GameObject>((int)GameObjects.ItemIcon).AddUIEvent((PointerEventData) => { Debug.Log($"{_name} Item clicked!"); } );
    }

    public void SetInfo(string name)
    {
        _name = name;
    }
}
{% endhighlight %}

* Add Component
  - in `UI_Inven` prefab, add Componenet `UI_Inven.cs`
  - in `UI_Inven_Item` prefab, add Component `UI_Inven_Item.cs`

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/46.jpg"><img src="/assets/img/posts/unity_mmorpg/46.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/47.jpg"><img src="/assets/img/posts/unity_mmorpg/47.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-UI-Inven.mp4" frameborder="0"> </iframe>

## Fix Codes

* folder organization
  - move `UI_Inven_Item` prefab and script

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/48.jpg"><img src="/assets/img/posts/unity_mmorpg/48.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/49.jpg"><img src="/assets/img/posts/unity_mmorpg/49.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* ResourceManager.cs
  - it removes "(Clone)" string when creating new object by prefab

{% highlight C# %}
public GameObject Instantiate(string path, Transform parent = null)
{
    ...
    GameObject go = Object.Instantiate(prefab, parent);
    int index = go.name.IndexOf("(Clone)");
    if (index > 0)
        go.name = go.name.Substring(0, index);

    return go;
}
{% endhighlight %}

* UIManager.cs
{% highlight C# %}
public T MakeSubItem<T>(Transform parent = null, string name = null) where T : UI_Base
{
    if (string.IsNullOrEmpty(name))
        name = typeof(T).Name;

    GameObject go = Managers.Resource.Instantiate($"UI/SubItem/{name}");

    if (parent != null)
        go.transform.SetParent(parent);
        
    return Util.GetOrAddComponent<T>(go);
}
{% endhighlight %}

* UI_Base.cs
{% highlight C# %}
public abstract class UI_Base : MonoBehaviour
{
    ...
    protected GameObject GetObject(int idx) { return Get<GameObject>(idx); }

    public static void BindEvent(GameObject go, Action<PointerEventData> action, Define.UIEvent type = Define.UIEvent.Click)
    {
      ...
    }
}
{% endhighlight %}

  - and change all `AddUIEvent` to `BindEvent` in the project

* Extension.cs
{% highlight C# %}
public static class Extension
{
    public static T GetOrAddComponent<T>(this GameObject go) where T : UnityEngine.Component
    {
        return Util.GetOrAddComponent<T>(go);
    }
    ...
}
{% endhighlight %}

# Scene
  
## organize Scenes

* PlayerController.cs
{% highlight C# %}
void Start()
{
    ...
    //Managers.UI.ShowSceneUI<UI_Inven>();
}
{% endhighlight %}

* Scripts\Scenes\BaseScene.cs
{% highlight C# %}
public abstract class BaseScene : MonoBehaviour
{
    public Define.Scene SceneType { get; protected set; } = Define.Scene.Unknown;

    void Awake()
    {
        Init();
    }

    protected virtual void Init()
    {
        Object obj = GameObject.FindObjectOfType(typeof(EventSystem));
        if (obj == null)
            Managers.Resource.Instantiate("UI/EventSystem").name = "@EventSystem";
    }

    public abstract void Clear();
}
{% endhighlight %}

* Scripts\Scenes\GameScene.cs
{% highlight C# %}
public class GameScene : BaseScene
{
    protected override void Init()
    {
        base.Init();

        SceneType = Define.Scene.Game;

        Managers.UI.ShowSceneUI<UI_Inven>();
    }

    public override void Clear() { }
}
{% endhighlight %}

* Scripts\Scenes\LoginScene.cs
{% highlight C# %}
public class LoginScene : BaseScene
{
    protected override void Init()
    {
        base.Init();

        SceneType = Define.Scene.Login;
    }

    public override void Clear() { }
}
{% endhighlight %}

* Define.cs
{% highlight C# %}
public class Define
{
    public enum Scene
    {
        Unknown,
        Login,
        Lobby,
        Game,
    }
    ...
}
{% endhighlight %}

* Game
  - save `SampleScene` to `Game`
  - Create Empty GameObject and change name to `@Scene`
  - Add `GameScene.cs` Componenet to `@Scene`

<figure>
  <a href="/assets/img/posts/unity_mmorpg/50.jpg"><img src="/assets/img/posts/unity_mmorpg/50.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - prefab EventSystem in Prefabs\UI
  - Remove EventSystem object in Hierarchy
<figure>
  <a href="/assets/img/posts/unity_mmorpg/51.jpg"><img src="/assets/img/posts/unity_mmorpg/51.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Login
  - Create New Scene and save name to `Login`
  - Create Empty GameObject and change name to `@Scene`
  - Add `LoginScene.cs` Componenet to `@Scene`
<figure>
  <a href="/assets/img/posts/unity_mmorpg/52.jpg"><img src="/assets/img/posts/unity_mmorpg/52.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

## Changing Scenes

* Build Settings
  - [Files]-[Build Settings]
  - You can add Scene by Drag in Asset

<figure>
  <a href="/assets/img/posts/unity_mmorpg/53.jpg"><img src="/assets/img/posts/unity_mmorpg/53.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

  - it is for just setting, dont' click build button, just exit this window

* Scripts\Managers\SceneManagerEx.cs
{% highlight C# %}
public class SceneManagerEx
{
    public BaseScene CurrentScene { get { return GameObject.FindObjectOfType<BaseScene>(); } }

    public void LoadScene(Define.Scene type)
    {
        CurrentScene.Clear();
        SceneManager.LoadScene(GetSceneName(type));
    }

    string GetSceneName(Define.Scene type)
    {
        string name = System.Enum.GetName(typeof(Define.Scene), type);
        return name;
    }
}
{% endhighlight %}

* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
  ...
  SceneManagerEx _scene = new SceneManagerEx();
  ...
  public static SceneManagerEx Scene { get { return Instance._scene; } }
}
{% endhighlight %}

* LoginScene.cs
  - scene will change when you press Q

{% highlight C# %}
private void Update()
{
    if(Input.GetKeyDown(KeyCode.Q))
    {
        Managers.Scene.LoadScene(Define.Scene.Game);
    }
}

public override void Clear()
{
    Debug.Log("LoginScene Clear!");
}
{% endhighlight %}

# Sound

## Sound ingredients
* Audio Source
  - Player
* Audio Clip
  - Sound file
* Audio Listener
  - audience

## Sound Manager

* move sound clips
  - Create [Sounds] folder
  - if player meet box, then sounds will play

<figure>
  <a href="/assets/img/posts/unity_mmorpg/55.jpg"><img src="/assets/img/posts/unity_mmorpg/55.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

* Add Sound Source Component
  - Delete Cube Two
  - Add Sound Source Component on Cube One
  - check `Is Trigger` on Cube One
  - and Audio Listner is already in Main Camera, so you don't have to add Audio Listner
  - if you want, you can add Aduio Listner on player

<figure>
  <a href="/assets/img/posts/unity_mmorpg/56.jpg"><img src="/assets/img/posts/unity_mmorpg/56.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>


* Scripts\Managers\SoundManager.cs
{% highlight C# %}
public class SoundManager
{
    AudioSource[] _audioSources = new AudioSource[(int)Define.Sound.MaxCount];

    Dictionary<string, AudioClip> _audioClips = new Dictionary<string, AudioClip>();

    public void Init()
    {
        GameObject root = GameObject.Find("@Sound");

        if (root == null)
        {
            root = new GameObject { name = "@Sound" };
            Object.DontDestroyOnLoad(root);

            string[] soundNames = System.Enum.GetNames(typeof(Define.Sound));
            for(int i=0; i<soundNames.Length -1; i++)
            {
                GameObject go = new GameObject { name = soundNames[i] };
                _audioSources[i] = go.AddComponent<AudioSource>();
                go.transform.parent = root.transform;
            }

            _audioSources[(int)Define.Sound.Bgm].loop = true;
        }
    }

    public void Clear()
    {
        foreach(AudioSource audioSource in _audioSources)
        {
            audioSource.clip = null;
            audioSource.Stop();
        }

        _audioClips.Clear();
    }

    public void Play(string path, Define.Sound type = Define.Sound.Effect,  float pitch = 1.0f)
    {
        AudioClip audioClip = GetOrAddAudioClip(path, type);
        Play(audioClip, type, pitch);
    }

    public void Play(AudioClip audioClip, Define.Sound type = Define.Sound.Effect, float pitch = 1.0f)
    {
        if (audioClip == null)
            return;

        if (type == Define.Sound.Bgm)
        {
            AudioSource audioSource = _audioSources[(int)Define.Sound.Bgm];
            if (audioSource.isPlaying)
                audioSource.Stop();

            audioSource.pitch = pitch;
            audioSource.clip = audioClip;
            audioSource.Play();
        }
        else
        {
            AudioSource audioSource = _audioSources[(int)Define.Sound.Effect];
            audioSource.pitch = pitch;
            audioSource.PlayOneShot(audioClip);
        }
    }

    AudioClip GetOrAddAudioClip(string path, Define.Sound type = Define.Sound.Effect)
    {
        if (path.Contains("Sounds/") == false)
            path = $"Sounds/{path}";

        AudioClip audioClip = null;

        if (type == Define.Sound.Bgm)
        {
            audioClip = Managers.Resource.Load<AudioClip>(path);
        }
        else
        {
            if (_audioClips.TryGetValue(path, out audioClip) == false)
            {
                audioClip = Managers.Resource.Load<AudioClip>(path);
                _audioClips.Add(path, audioClip);
            }
        }

        if (audioClip == null)
        {
            Debug.Log($"AudioClip Missing! {path}");
        }
        return audioClip;
    }
}
{% endhighlight %}

* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...
    SoundManager _sound = new SoundManager();
    ...
    public static SoundManager Sound { get { return Instance._sound; } }
    ...
}
{% endhighlight %}

* Define.cs
{% highlight C# %}
public class Define
{
    ...
    public enum Sound
    {
        Bgm,
        Effect,
        MaxCount,
    }
    ...
}
{% endhighlight %}

* TestSound.cs
{% highlight C# %}
public class TestSound : MonoBehaviour
{
    public AudioClip audioClip;
    public AudioClip audioClip2;

    int i = 0;

    private void OnTriggerEnter(Collider other)
    {
        i++;

        if(i%2==0)
            Managers.Sound.Play(audioClip, Define.Sound.Bgm);
        else
            Managers.Sound.Play(audioClip2, Define.Sound.Bgm);
    }
}
{% endhighlight %}

### Memory Optimization
* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...
    public static void Clear()
    {
        Input.Clear();
        Sound.Clear();
        Scene.Clear();
        UI.Clear();
    }
}
{% endhighlight %}

* InputManager.cs
{% highlight C# %}
public void Clear()
{
    KeyAction = null;
    MouseAction = null;
}
{% endhighlight %}

* UIManager.cs
{% highlight C# %}
public void Clear()
{
    CloseAllPopupUI();
    _sceneUI = null;
}
{% endhighlight %}

* SceneManagerEX.cs
{% highlight C# %}
public void LoadScene(Define.Scene type)
{
    Managers.Clear();
    //CurrentScene.Clear();
    ...
}

...

public void Clear()
{
    CurrentScene.Clear();
}
{% endhighlight %}

* LoginScene.cs
{% highlight C# %}
public override void Clear()
{
    Debug.Log("LoginScene Clear!");
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Sound.mp4" frameborder="0"> </iframe>

## 3D Sound

* make 3D Sound on Cube One
  - change Spatial Blend to 1. This mean this Sound Source is 3D Sound.
  - change min and max distance

<figure>
  <a href="/assets/img/posts/unity_mmorpg/54.jpg"><img src="/assets/img/posts/unity_mmorpg/54.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Sound-3D.mp4" frameborder="0"> </iframe>

  - for Next chapter, you need to remove TestSounc.cs and Sound Source Component on Cube One

# Pool

* Pooling
  - If there is so much object in game, it will make some trouble when loading.
  - to decrese this overloading, pool manager will work
  - pool manager loads object before they are activate setting non-activate
  - when the are needed in game, pool manager will switch their status to activate from non-activate

## Pool Manager

* Manager.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...
    PoolManager _pool = new PoolManager();
    ...
    public static PoolManager Pool { get { return Instance._pool; } }
    ...

    static void Init()
    {
        if (s_instance == null)
        {
            ...
            s_instance._pool.Init();
            ...
        }
    }

    public static void Clear()
    {
        ...
        Pool.Clear();
    }
}
{% endhighlight %}


* Scripts\Managers\Poolable.cs
{% highlight C# %}
public class Poolable : MonoBehaviour
{
    public bool IsUsing;
}
{% endhighlight %}

* Scripts\Managers\PoolManager.cs
{% highlight C# %}
public class PoolManager
{
    #region Pool
    class Pool
    {
        public GameObject Original { get; private set; }
        public Transform Root { get; set; }

        Stack<Poolable> _poolStack = new Stack<Poolable>();

        public void Init(GameObject original, int count = 5)
        {
            Original = original;
            Root = new GameObject().transform;
            Root.name = $"{original.name}_Root";

            for(int i=0; i<count; i++)
                Push(Create());
        }

        Poolable Create()
        {
            GameObject go = Object.Instantiate<GameObject>(Original);
            go.name = Original.name;
            return go.GetOrAddComponent<Poolable>();
        }

        public void Push(Poolable poolable)
        {
            if (poolable == null)
                return;

            poolable.transform.parent = Root;
            poolable.gameObject.SetActive(false);
            poolable.IsUsing = false;

            _poolStack.Push(poolable);
        }

        public Poolable Pop(Transform parent)
        {
            Poolable poolable;

            if (_poolStack.Count > 0)
                poolable = _poolStack.Pop();
            else
                poolable = Create();

            poolable.gameObject.SetActive(true);

            if (parent == null)
                poolable.transform.parent = Managers.Scene.CurrentScene.transform;

            poolable.transform.parent = parent;
            poolable.IsUsing = true;

            return poolable;
        }
    }
    #endregion

    Dictionary<string, Pool> _pool = new Dictionary<string, Pool>();
    Transform _root;

    public void Init()
    {
        if (_root == null)
        {
            _root = new GameObject { name = "@Pool_Root" }.transform;
            Object.DontDestroyOnLoad(_root);
        }
    }

    public void CreatePool(GameObject original, int count = 5)
    {
        Pool pool = new Pool();
        pool.Init(original, count);
        pool.Root.parent = _root;

        _pool.Add(original.name, pool);
    }

    public void Push(Poolable poolable)
    {
        string name = poolable.gameObject.name;
        if (_pool.ContainsKey(name) == false)
        {
            GameObject.Destroy(poolable.gameObject);
            return;
        }

        _pool[name].Push(poolable);
    }

    public Poolable Pop(GameObject original, Transform parent = null)
    {
        if (_pool.ContainsKey(original.name) == false)
            CreatePool(original);
               
        return _pool[original.name].Pop(parent);
    }

    public GameObject GetOriginal(string name)
    {
        if (_pool.ContainsKey(name) == false)
            return null;
        return _pool[name].Original;
    }

    public void Clear()
    {
        foreach (Transform child in _root)
            GameObject.Destroy(child.gameObject);

        _pool.Clear();
    }
}
{% endhighlight %}

* ResourceManager.cs
{% highlight C# %}
public T Load<T>(string path) where T: Object
{
    if (typeof(T) == typeof(GameObject))
    {
        ...

        GameObject go = Managers.Pool.GetOriginal(name);
        if (go != null)
            return go as T;
    }
    ...
}
    
public GameObject Instantiate(string path, Transform parent = null)
{
    GameObject original = Load<GameObject>($"Prefabs/{path}");

    if (original == null)
    {
        Debug.Log($"Failed to load prefab: {path}");
        return null;
    }
    
    if (original.GetComponent<Poolable>() != null)
        return Managers.Pool.Pop(original, parent).gameObject;

    GameObject go = Object.Instantiate(original, parent);
    go.name = original.name;

    return go;
}

public void Destroy(GameObject go)
{
    if (go == null)
        return;

    Poolable poolable = go.GetComponent<Poolable>();
    if(poolable != null)
    {
        Managers.Pool.Push(poolable);
        return;
    }

    Object.Destroy(go);
}
{% endhighlight %}

### Test

* GameScene.cs
{% highlight C# %}
protected override void Init()
{
    ...
    for (int i = 0; i < 5; i++)
        Managers.Resource.Instantiate("UnityChan");
}
{% endhighlight %}

* LoginScene.cs
{% highlight C# %}
protected override void Init()
{
    ...

    List<GameObject> list = new List<GameObject>();
    for (int i = 0; i < 5; i++)
        list.Add(Managers.Resource.Instantiate("UnityChan"));

    foreach(GameObject obj in list)
    {
        Managers.Resource.Destroy(obj);
    }
}
{% endhighlight %}

* Test
  - create new prefab for player
  - remove player in hierarchy
  - add player in CameraController component

<figure class="half">
  <a href="/assets/img/posts/unity_mmorpg/57.jpg"><img src="/assets/img/posts/unity_mmorpg/57.jpg"></a>
  <a href="/assets/img/posts/unity_mmorpg/58.jpg"><img src="/assets/img/posts/unity_mmorpg/58.jpg"></a>
	<figcaption>MMO Unity</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Pool.mp4" frameborder="0"> </iframe>

# Coroutine

* can Save/Restore status of function
  - stop a work spending so much time
  - stop/restore function when you want

## return

  * yield retun object
    - return type what you want(even class)

  * yield return null
    - pass this tic

  * yield return break;
    - stop this coroutine

### Test

* GameScene.cs
{% highlight C# %}  
public class GameScene : BaseScene
{
    Coroutine co;

    protected override void Init()
    {
        ...
        co = StartCoroutine("ExplodeAfterSeconds", 4.0f);
        StartCoroutine("CoStopExplode", 6.0f);
    }

    IEnumerator CoStopExplode(float seconds)
    {
        Debug.Log("Stop Enter");
        yield return new WaitForSeconds(seconds);
        Debug.Log("Stop Execute!");

        if (co != null)
        {
            StopCoroutine(co);
            co = null;
        }
    }

    IEnumerator ExplodeAfterSeconds(float seconds)
    {
        Debug.Log("Explode Enter");
        yield return new WaitForSeconds(seconds);
        Debug.Log("Explode Execute!");
        co = null;
    }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmorpg/MMORPG-Coroutine.mp4" frameborder="0"> </iframe>

# Data

* JSON
  - Normally, data is managed by json files

* Assets\Resources\Data\StatData.json
{% highlight json %}
{
  "stats": [
    {
      "level": "1",
      "hp": "100",
      "attack": "10"
    },
    {
      "level": "2",
      "hp": "150",
      "attack": "15"
    },
    {
      "level": "3",
      "hp": "200",
      "attack": "20"
    }
  ]
}
{% endhighlight %}

## Data Manager

* Data Manager
  - control the json files of data

* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...
    DataManager _data = new DataManager();
    ...
    public static DataManager Data { get { return Instance._data; } }
    ...
    static void Init()
    {
        if (s_instance == null)
        {
            ...
            s_instance._data.Init();
            ...
        }
    }
}
{% endhighlight %}

* Scripts\Managers\DataManager.cs
{% highlight C# %}
public interface ILoader<Key, Value>
{
    Dictionary<Key, Value> MakeDict();
}

public class DataManager
{
    public Dictionary<int, Stat> StatDict { get; private set; } = new Dictionary<int, Stat>();

    public void Init()
    {
        StatDict = LoadJson<StatData, int, Stat>("StatData").MakeDict();
    }

    Loader LoadJson<Loader, Key, Value>(string path) where Loader: ILoader<Key, Value>
    {
        TextAsset textAsset = Managers.Resource.Load<TextAsset>($"Data/{path}");
        return JsonUtility.FromJson<Loader>(textAsset.text);
    }
}
{% endhighlight %}

### Test

* GameScene.cs
{% highlight C# %}
protected override void Init()
{
    ...
    Dictionary<int,Stat> dict = Managers.Data.StatDict;
}
{% endhighlight %}

[Download](https://github.com/leehuhlee/Unity){: .btn}
