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

<figure>
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

[Download](https://github.com/leehuhlee/Unity){: .btn}
