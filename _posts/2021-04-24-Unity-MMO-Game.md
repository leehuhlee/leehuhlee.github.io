---
layout: post
title: "[Unity] MMO Game"
date: 2021-04-24
excerpt: "MMO Game 2D"
tags: [C#, Unity, Game, MMO, 2D]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part7/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part7: MMO 컨텐츠 구현 (Unity + C# 서버 연동 기초)</a></center>

# Client

## Set for 2D

* Project Settings
  - [Window]-[Project Settings]-[Editor]
  - Change `Default Behavior Mode` to `2D`

<figure>
  <a href="/assets/img/posts/unity_mmogame/0.jpg"><img src="/assets/img/posts/unity_mmogame/0.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Scene
  - Change `Scene` to `2D`

<figure>
  <a href="/assets/img/posts/unity_mmogame/1.jpg"><img src="/assets/img/posts/unity_mmogame/1.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Package Manager
  - [window]-[Package Manager]
  - Download `2D Tilemap Editor`

<figure>
  <a href="/assets/img/posts/unity_mmogame/2.jpg"><img src="/assets/img/posts/unity_mmogame/2.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Asset Store
  - [window]-[Asset Store]
  - Download and import Assets

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/3.jpg"><img src="/assets/img/posts/unity_mmogame/3.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/4.jpg"><img src="/assets/img/posts/unity_mmogame/4.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Tilemap Map

* Grid
  - [2D Object]-[Tilemap]
 - Create Tilemap

<figure>
  <a href="/assets/img/posts/unity_mmogame/6.jpg"><img src="/assets/img/posts/unity_mmogame/6.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Palette
  - [window]-[2D]-[Tile Palette]
  - Create new Palette and change name to `Palette_Map`
  - Save on `Resources\Tiles\Palettes`
  - Drag `Assets\TinyRPGTown\Artwork\tileset` and drop on `TilePalette` window
  - Save tiles on `Resources\Tiles\Tiles_Map`
  - Make your map

<figure class="third">
  <a href="/assets/img/posts/unity_mmogame/5.jpg"><img src="/assets/img/posts/unity_mmogame/5.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/7.jpg"><img src="/assets/img/posts/unity_mmogame/7.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/8.jpg"><img src="/assets/img/posts/unity_mmogame/8.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Tilemap Environment

* Grid
  - Create Tilemap and change name to `Tilemap_Env`
  - change layer to `1`

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/10.jpg"><img src="/assets/img/posts/unity_mmogame/10.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/12.jpg"><img src="/assets/img/posts/unity_mmogame/12.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Palette
  - Create new Palette and change name to `Palette_Env`
  - Save on `Resources\Tiles\Palettes`
  - Select `Palette_Evn`
  - Drag `Assets\TinyRPGTown\Artwork\Sprites\barrel` and `building` and drop on `TilePalette` window
  - Save tiles on `Resources\Tiles\Tiles_Env`
  - Make your map

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/11.jpg"><img src="/assets/img/posts/unity_mmogame/11.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/13.jpg"><img src="/assets/img/posts/unity_mmogame/13.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Tilemap Collision

* Grid
  - Create Tilemap and change name to `Tilemap_Collision`
  - change layer to `10`
  
<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/15.jpg"><img src="/assets/img/posts/unity_mmogame/15.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/16.jpg"><img src="/assets/img/posts/unity_mmogame/16.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Palette
  - Select `Palette_Evn`
  - Drag `Assets\TinyRPGTown\Artwork\Sprites\rock` and drop on `TilePalette` window
  - Save tiles on `Resources\Tiles\Tiles_Env`
  - Make your map

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/17.jpg"><img src="/assets/img/posts/unity_mmogame/17.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/18.jpg"><img src="/assets/img/posts/unity_mmogame/18.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

### Test

* Assets\Scripts\TestCollision.cs
{% highlight C# %}
public class TestCollision : MonoBehaviour
{
    public Tilemap _tilemap;
    public TileBase _tile;

    void Start()
    {
        // set tile on specified position
        _tilemap.SetTile(new Vector3Int(0, 0, 0), _tile);
    }

    void Update()
    {
        // scan all map and get position if tile is setted
        List<Vector3Int> blocked = new List<Vector3Int>();

        foreach(Vector3Int pos in _tilemap.cellBounds.allPositionsWithin)
        {
            TileBase tile = _tilemap.GetTile(pos);
            if (tile != null)
                blocked.Add(pos);
        }
    }
}
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/unity_mmogame/19.jpg"><img src="/assets/img/posts/unity_mmogame/19.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/14.jpg"><img src="/assets/img/posts/unity_mmogame/14.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/20.jpg"><img src="/assets/img/posts/unity_mmogame/20.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Map Tool

* Assets\Editor\MapEditor.cs
{% highlight C# %}
public class MapEditor
{

// it is not excuted in release mode
#if UNITY_EDITOR

    // %: Ctrl, #: Shift, &: Alt
    [MenuItem("Tools/GenerateMap %#g")]
    private static void GenerateMap()
    {
        // find Prefabs/Map
        GameObject[] gameObjects = Resources.LoadAll<GameObject>("Prefabs/Map");

        foreach(GameObject go in gameObjects)
        {
            // find Tilemap_Collision in Prefabs/Map
            Tilemap tm = Util.FindChild<Tilemap>(go, "Tilemap_Collision", true);

            // write file
            using (var writer = File.CreateText($"Assets/Resources/Map/{go.name}.txt"))
            {
                writer.WriteLine(tm.cellBounds.xMin);
                writer.WriteLine(tm.cellBounds.xMax);
                writer.WriteLine(tm.cellBounds.yMin);
                writer.WriteLine(tm.cellBounds.yMax);

                for (int y = tm.cellBounds.yMax; y >= tm.cellBounds.yMin; y--)
                {
                    for (int x = tm.cellBounds.xMin; x <= tm.cellBounds.xMax; x++)
                    {
                        TileBase tile = tm.GetTile(new Vector3Int(x, y, 0));
                        if (tile != null)
                            writer.Write("1");
                        else
                            writer.Write("0");
                    }

                    writer.WriteLine();
                }
            }
        }
    }

#endif

}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/21.jpg"><img src="/assets/img/posts/unity_mmogame/21.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/22.jpg"><img src="/assets/img/posts/unity_mmogame/22.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Player Move
  - remove `TestCollision.cs`

* Player Sprite Setting
  - Set `Pixel Per Unit` to `20`
  - Create Player and set position to (0.5, 0.5, 0)

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/23.jpg"><img src="/assets/img/posts/unity_mmogame/23.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/24.jpg"><img src="/assets/img/posts/unity_mmogame/24.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Utils\Define.cs
{% highlight C# %}
public class Define
{
    public enum MoveDir
    {
        None,
        Up,
        Down,
        Left,
        Right,
    }
    ...
}
{% endhighlight %}

* Controllers\PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    public Grid _grid;
    public float _speed = 5.0f;

    Vector3Int _cellPos = Vector3Int.zero;
    MoveDir _dir = MoveDir.None;
    bool _isMoving = false;


    void Start()
    {
        Vector3 pos = _grid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f) ;
        transform.position = pos;
    }

    void Update()
    {
        GetDirInput();
        UpdatePosition();
        UpdateIsMoving();
    }

    // get Keyboard input and change moving direction
    void GetDirInput()
    {
        if (Input.GetKey(KeyCode.W))
        {
            _dir = MoveDir.Up;
        }
        else if (Input.GetKey(KeyCode.S))
        {
            _dir = MoveDir.Down;
        }
        else if (Input.GetKey(KeyCode.A))
        {
            _dir = MoveDir.Left;
        }
        else if (Input.GetKey(KeyCode.D))
        {
            _dir = MoveDir.Right;
        }
        else
        {
            _dir = MoveDir.None;
        }
    }

    // for slerp moving
    void UpdatePosition()
    {
        if (_isMoving == false)
            return;

        Vector3 destPos = _grid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f);
        Vector3 moveDir = destPos - transform.position;

        // check arrived
        float dist = moveDir.magnitude;
        if(dist<_speed * Time.deltaTime)
        {
            transform.position = destPos;
            _isMoving = false;
        }
        else
        {
            transform.position += moveDir.normalized * _speed * Time.deltaTime;
            _isMoving = true;
        }
    }

    // move
    void UpdateIsMoving()
    {
        if (_isMoving == false)
        {
            switch (_dir)
            {
                case MoveDir.Up:
                    _cellPos += Vector3Int.up;
                    _isMoving = true;
                    break;
                case MoveDir.Down:
                    _cellPos += Vector3Int.down;
                    _isMoving = true;
                    break;
                case MoveDir.Left:
                    _cellPos += Vector3Int.left;
                    _isMoving = true;
                    break;
                case MoveDir.Right:
                    _cellPos += Vector3Int.right;
                    _isMoving = true;
                    break;
                default:
                    break;
            }
        }
    }
}
{% endhighlight %}
  - set `Grid`

<figure>
  <a href="/assets/img/posts/unity_mmogame/25.jpg"><img src="/assets/img/posts/unity_mmogame/25.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Player-Move.mp4" frameborder="0"> </iframe>

## Player Animation

* Create Clips
  - Create new Animation Controller in `Resources\Animations` and change name to `PlayerAnimController`
  - Create Clips in `Resources\Animations\Player`

<figure>
  <a href="/assets/img/posts/unity_mmogame/26.jpg"><img src="/assets/img/posts/unity_mmogame/26.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    ...
    Animator _animator;

    MoveDir _dir = MoveDir.Down;

    public MoveDir Dir
    {
        get { return _dir; }
        set 
        {
            if (_dir == value)
                return;

            switch (value)
            {
                case MoveDir.Up:
                    _animator.Play("WALK_BACK");
                    break;
                case MoveDir.Down:
                    _animator.Play("WALK_FRONT");
                    break;
                case MoveDir.Left:
                    _animator.Play("WALK_RIGHT");
                    transform.localScale = new Vector3(-1.0f, 1.0f, 1.0f);
                    break;
                case MoveDir.Right:
                    _animator.Play("WALK_RIGHT");
                    transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                    break;
                case MoveDir.None:
                    if(_dir == MoveDir.Up)
                    {
                        _animator.Play("IDLE_BACK");
                        transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                    }
                    else if (_dir == MoveDir.Down)
                    {
                        _animator.Play("IDLE_FRONT");
                        transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                    }
                    else if (_dir == MoveDir.Left)
                    {
                        _animator.Play("IDLE_RIGHT");
                        transform.localScale = new Vector3(-1.0f, 1.0f, 1.0f);
                    }
                    else
                    {
                        _animator.Play("IDLE_RIGHT");
                        transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                    }
                    break;
            }

            _dir = value;
        }
    }
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Player-Animation.mp4" frameborder="0"> </iframe>

## Map Manager

* Scripts\Managers\Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...
    #region Contents
    MapManager _map = new MapManager();

    public static MapManager Map { get { return Instance._map; } }

  	#endregion
    ...
}
{% endhighlight %}

* Scripts\Managers\Contents\MapManager.cs
{% highlight C# %}
public class MapManager
{
    public Grid CurrentGrid { get; private set; }

    public int MinX { get; set; }
    public int MaxX { get; set; }
    public int MinY { get; set; }
    public int MaxY { get; set; }

    bool[,] _collision;

    public bool CanGo(Vector3Int cellPos)
    {
        if (cellPos.x < MinX || cellPos.x > MaxX)
            return false;
        if (cellPos.y < MinY || cellPos.y > MaxY)
            return false;

        int x = cellPos.x - MinX;
        int y = MaxY - cellPos.y;
        return !_collision[y, x];
    }

    public void LoadMap(int mapId)
    {
        DestroyMap();

        string mapName = "Map_" + mapId.ToString("000");
        GameObject go = Managers.Resource.Instantiate($"Map/{mapName}");
        go.name = "Map";

        GameObject collision = Util.FindChild(go, "Tilemap_Collision", true);
        if (collision != null)
            collision.SetActive(false);

        CurrentGrid = go.GetComponent<Grid>();

        // Collision file load
        TextAsset txt = Managers.Resource.Load<TextAsset>($"Map/{mapName}");
        StringReader reader = new StringReader(txt.text);

        MinX = int.Parse(reader.ReadLine());
        MaxX = int.Parse(reader.ReadLine());
        MinY = int.Parse(reader.ReadLine());
        MaxY = int.Parse(reader.ReadLine());

        int xCount = MaxX - MinX + 1;
        int yCount = MaxY - MinY + 1;
        _collision = new bool[yCount, xCount];
        for(int y=0; y<yCount; y++)
        {
            string line = reader.ReadLine();
            for(int x = 0; x<xCount; x++)
            {
                _collision[y, x] = (line[x] == '1' ? true : false);
            }
        }
    }
    
    public void DestroyMap()
    {
        GameObject map = GameObject.Find("Map");
        if(map != null)
        {
            GameObject.Destroy(map);
            CurrentGrid = null;
        }
    }
}

{% endhighlight %}

* Scripts\Scenes\GameScene.cs
  - Create Empty Game Object and change name go `GameScene`
  - add `GameScene.cs` compoenent

<figure>
  <a href="/assets/img/posts/unity_mmogame/27.jpg"><img src="/assets/img/posts/unity_mmogame/27.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

{% highlight C# %}
public class GameScene : BaseScene
{
    protected override void Init()
    {
        base.Init();

        SceneType = Define.Scene.Game;

        Managers.Map.LoadMap(1);
        ...
    }
    ...
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : MonoBehaviour
{
    // remove Grid
    public float _speed = 5.0f;
    ...

    void Start()
    {
        _animator = GetComponent<Animator>();
        Vector3 pos = Managers.Map.CurrentGrid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f) ;
        transform.position = pos;
    }

    ...

    // camera should follow player
    void LateUpdate()
    {
        Camera.main.transform.position = new Vector3(transform.position.x, transform.position.y, -10);
    }

    ...

    void UpdatePosition()
    {
        if (_isMoving == false)
            return;

        Vector3 destPos = Managers.Map.CurrentGrid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f);
        Vector3 moveDir = destPos - transform.position;
        ...
    }

    // Now you have to check it is collision
    void UpdateIsMoving()
    {
        if (_isMoving == false && _dir != MoveDir.None)
        {
            Vector3Int destPos = _cellPos;

            switch (_dir)
            {
                case MoveDir.Up:
                    destPos += Vector3Int.up;
                    break;
                case MoveDir.Down:
                    destPos += Vector3Int.down;
                    break;
                case MoveDir.Left:
                    destPos += Vector3Int.left;
                    break;
                case MoveDir.Right:
                    destPos += Vector3Int.right;
                    break;
                default:
                    break;
            }

            if (Managers.Map.CanGo(destPos))
            {
                _cellPos = destPos;
                _isMoving = true;
            }

        }

    }
}
{% endhighlight %}

* EditorMapEditor.cs
{% highlight C# %}
public class MapEditor
{
#if UNITY_EDITOR

    [MenuItem("Tools/GenerateMap %#g")]
    private static void GenerateMap()
    {
        GameObject[] gameObjects = Resources.LoadAll<GameObject>("Prefabs/Map");

        foreach(GameObject go in gameObjects)
        {
            // Tilemap_Base is bigger then Tilemap_Collision
            // you have to load Tilemap_Base to check whole map
            Tilemap tmBase = Util.FindChild<Tilemap>(go, "Tilemap_Base", true);
            Tilemap tm = Util.FindChild<Tilemap>(go, "Tilemap_Collision", true);

            using (var writer = File.CreateText($"Assets/Resources/Map/{go.name}.txt"))
            {
                writer.WriteLine(tmBase.cellBounds.xMin);
                writer.WriteLine(tmBase.cellBounds.xMax);
                writer.WriteLine(tmBase.cellBounds.yMin);
                writer.WriteLine(tmBase.cellBounds.yMax);

                for (int y = tmBase.cellBounds.yMax; y >= tmBase.cellBounds.yMin; y--)
                {
                    for (int x = tmBase.cellBounds.xMin; x <= tmBase.cellBounds.xMax; x++)
                    {
                       ...
                    }

                    writer.WriteLine();
                }
            }
        }
    }

#endif
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Map-Manager.mp4" frameborder="0"> </iframe>

## Separate Controller
  - create CreatureController to use base controller

* Define.cs
{% highlight C# %}
public class Define
{
    public enum CreatureState
    {
        Idle,
        Moving,
        Skill,
        Dead,
    }
    ...
}
{% endhighlight %}

* Scripts\Controllers\CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    public float _speed = 5.0f;

    protected Vector3Int _cellPos = Vector3Int.zero;
    protected Animator _animator;
    protected SpriteRenderer _sprite;

    CreatureState _state = CreatureState.Idle;
    public CreatureState State
    {
        get { return _state; }
        set
        {
            if (_state == value)
                return;

            _state = value;
            UpdateAnimation();
        }
    }

    // for Idle Animation Update
    MoveDir _lastDir = MoveDir.Down;
    MoveDir _dir = MoveDir.Down;

    public MoveDir Dir
    {
        get { return _dir; }
        set
        {
            if (_dir == value)
                return;

            _dir = value;
            // save last direction
            if (value != MoveDir.None)
                _lastDir = value;

            UpdateAnimation();
        }
    }

    protected virtual void UpdateAnimation()
    {
        if(_state == CreatureState.Idle)
        {
            switch (_lastDir)
            {
                case MoveDir.Up:
                    _animator.Play("IDLE_BACK");
                    _sprite.flipX = false;
                    break;
                case MoveDir.Down:
                    _animator.Play("IDLE_FRONT");
                    _sprite.flipX = false;
                    break;
                case MoveDir.Left:
                    _animator.Play("IDLE_RIGHT");
                    // reverse x direction
                    _sprite.flipX = true;
                    break;
                case MoveDir.Right:
                    _animator.Play("IDLE_RIGHT");
                    _sprite.flipX = false;
                    break;
            }
        }
        else if(_state == CreatureState.Moving)
        {
            switch (_dir)
            {
                case MoveDir.Up:
                    _animator.Play("WALK_BACK");
                    _sprite.flipX = false;

                    break;
                case MoveDir.Down:
                    _animator.Play("WALK_FRONT");
                    _sprite.flipX = false;

                    break;
                case MoveDir.Left:
                    _animator.Play("WALK_RIGHT");
                    _sprite.flipX = true;
                    break;
                case MoveDir.Right:
                    _animator.Play("WALK_RIGHT");
                    _sprite.flipX = false;
                    break;
            }
        }
        else if(_state == CreatureState.Skill)
        {
            // TODO
        }
        else
        {
            return;
        }
    }

    void Start()
    {
        Init();
    }

    void Update()
    {
        UpdateController();
    }

    protected virtual void Init()
    {
        _animator = GetComponent<Animator>();
        _sprite = GetComponent<SpriteRenderer>();
        Vector3 pos = Managers.Map.CurrentGrid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f);
        transform.position = pos;
    }

    protected virtual void UpdateController()
    {
        UpdatePosition();
        UpdateIsMoving();
    }

    void UpdatePosition()
    {
        if (State  != CreatureState.Moving)
            return;

        Vector3 destPos = Managers.Map.CurrentGrid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f);
        Vector3 moveDir = destPos - transform.position;

        float dist = moveDir.magnitude;
        if (dist < _speed * Time.deltaTime)
        {
            transform.position = destPos;
            // exceptionally controll animation directly
            _state = CreatureState.Idle;
            // after moving, change animation to Idle
            if (_dir == MoveDir.None)
                UpdateAnimation();
        }
        else
        {
            transform.position += moveDir.normalized * _speed * Time.deltaTime;
            State = CreatureState.Moving;
        }
    }

    void UpdateIsMoving()
    {
        if ( State == CreatureState.Idle && _dir != MoveDir.None)
        {
            Vector3Int destPos = _cellPos;

            switch (_dir)
            {
                case MoveDir.Up:
                    destPos += Vector3Int.up;
                    break;
                case MoveDir.Down:
                    destPos += Vector3Int.down;
                    break;
                case MoveDir.Left:
                    destPos += Vector3Int.left;
                    break;
                case MoveDir.Right:
                    destPos += Vector3Int.right;
                    break;
                default:
                    break;
            }

            if (Managers.Map.CanGo(destPos))
            {
                _cellPos = destPos;
                State = CreatureState.Moving;
            }
        }
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
    protected override void Init()
    {
        base.Init();
    }

    protected override void UpdateController()
    {
        GetDirInput();
        base.UpdateController();
    }

    void LateUpdate()
    {
        Camera.main.transform.position = new Vector3(transform.position.x, transform.position.y, -10);
    }

    void GetDirInput()
    {
        if (Input.GetKey(KeyCode.W))
        {
            Dir = MoveDir.Up;
        }
        else if (Input.GetKey(KeyCode.S))
        {
            Dir = MoveDir.Down;
        }
        else if (Input.GetKey(KeyCode.A))
        {
            Dir = MoveDir.Left;
        }
        else if (Input.GetKey(KeyCode.D))
        {
            Dir = MoveDir.Right;
        }
        else
        {
            Dir = MoveDir.None;
        }
    }
}
{% endhighlight %}

* Scripts\Controllers\MonsterController.cs
  - Copy `Player` and change name to `Monster`
  - remove `PlayerController.cs` component and add `MonsterController.cs` component
  - change color to red

<figure>
  <a href="/assets/img/posts/unity_mmogame/28.jpg"><img src="/assets/img/posts/unity_mmogame/28.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

{% highlight C# %}
public class MonsterController : CreatureController
{
    protected override void Init()
    {
        base.Init();
    }

    protected override void UpdateController()
    {
        base.UpdateController();
    }

    void GetDirInput()
    {
        if (Input.GetKey(KeyCode.W))
        {
            Dir = MoveDir.Up;
        }
        else if (Input.GetKey(KeyCode.S))
        {
            Dir = MoveDir.Down;
        }
        else if (Input.GetKey(KeyCode.A))
        {
            Dir = MoveDir.Left;
        }
        else if (Input.GetKey(KeyCode.D))
        {
            Dir = MoveDir.Right;
        }
        else
        {
            Dir = MoveDir.None;
        }
    }
}
{% endhighlight %}

## Object Manager

* MonsterController.cs
{% highlight C# %}
public class MonsterController : CreatureController
{
    protected override void Init()
    {
        base.Init();
        // initial monster
        State = CreatureState.Idle;
        Dir = MoveDir.None;
    }
    ...
}
{% endhighlight %}

* Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...
    #region Contents
    MapManager _map = new MapManager();
    ObjectManager _obj = new ObjectManager();

    public static MapManager Map { get { return Instance._map; } }
    public static ObjectManager Object { get { return Instance._obj; } }
	  #endregion
  ...
}
{% endhighlight %}


* Scripts\Managers\Contents\ObjectManager.cs
  - manage collision with objects

{% highlight C# %}
public class ObjectManager
{
    List<GameObject> _objects = new List<GameObject>();

    public void Add(GameObject go)
    {
        _objects.Add(go);
    }

    public void Remove(GameObject go)
    {
        _objects.Remove(go);
    }

    // find collision object by position
    public GameObject Find(Vector3Int cellPos)
    {
        foreach(GameObject obj in _objects)
        {
            CreatureController cc = obj.GetComponent<CreatureController>();
            if (cc == null)
                continue;

            if (cc.CellPos == cellPos)
                return obj;
        }

        return null;
    }

    public void Clear()
    {
        _objects.Clear();
    }
}
{% endhighlight %}


* CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    public float _speed = 5.0f;

    // open CellPos for Find
    public Vector3Int CellPos { get; set; } = Vector3Int.zero;
    protected Animator _animator;
    protected SpriteRenderer _sprite;
    ...

    protected virtual void Init()
    {
        _animator = GetComponent<Animator>();
        _sprite = GetComponent<SpriteRenderer>();
        Vector3 pos = Managers.Map.CurrentGrid.CellToWorld(CellPos) + new Vector3(0.5f, 0.5f);
        transform.position = pos;
    }

    void UpdatePosition()
    {
        ...
        Vector3 destPos = Managers.Map.CurrentGrid.CellToWorld(CellPos) + new Vector3(0.5f, 0.5f);
        Vector3 moveDir = destPos - transform.position;
        ...
    }

    void UpdateIsMoving()
    {
        if ( State == CreatureState.Idle && _dir != MoveDir.None)
        {
            Vector3Int destPos = CellPos;
            ...

            State = CreatureState.Moving;

            if (Managers.Map.CanGo(destPos))
            {
                // move if there is not objects
                if(Managers.Object.Find(destPos) == null)
                {
                    CellPos = destPos;
                }
                
            }

        }
    }
}
{% endhighlight %}

* Prefabs
  - Create `Player` and `Monster` Prefab in `Prefabs\Creature`
  - Remove `Player` and `Monster` in Hierarchy

* GameScene.cs
{% highlight C# %}
public class GameScene : BaseScene
{
    protected override void Init()
    {
        ...

        Managers.Map.LoadMap(1);

        // Create Player by Prefab
        GameObject player = Managers.Resource.Instantiate("Creature/Player");
        player.name = "Player";
        Managers.Object.Add(player);

        // Create Monsters by Prefab
        for(int i=0; i < 5; i++)
        {
            GameObject monster = Managers.Resource.Instantiate("Creature/Monster");
            monster.name = $"Monster_{i + 1}";

            Vector3Int pos = new Vector3Int()
            {
                x = Random.Range(Managers.Map.MinX, Managers.Map.MaxX),
                y = Random.Range(Managers.Map.MinY, Managers.Map.MaxY)
            };

            MonsterController mc = monster.GetComponent<MonsterController>();
            mc.CellPos = pos;

            Managers.Object.Add(monster);
        }
        ...
    }
    ...
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/30.jpg"><img src="/assets/img/posts/unity_mmogame/30.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/31.jpg"><img src="/assets/img/posts/unity_mmogame/31.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

### Test

<figure>
  <a href="/assets/img/posts/unity_mmogame/29.jpg"><img src="/assets/img/posts/unity_mmogame/29.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Skill : Attack

* CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    ...
    // separate update by state
    public Vector3Int GetFrontCellPos()
    {
        Vector3Int cellPos = CellPos;

        switch (_lastDir)
        {
            case MoveDir.Up:
                cellPos += Vector3Int.up;
                break;
            case MoveDir.Down:
                cellPos += Vector3Int.down;
                break;
            case MoveDir.Left:
                cellPos += Vector3Int.left;
                break;
            case MoveDir.Right:
                cellPos += Vector3Int.right;
                break;
        }

        return cellPos;
    }
    ...

    protected virtual void UpdateAnimation()
    {
        if(_state == CreatureState.Idle)
        {
            // use skill on last direction
            switch (_lastDir)
            {
                case MoveDir.Up:
                    _animator.Play("IDLE_BACK");
                    _sprite.flipX = false;
                    break;
                case MoveDir.Down:
                    _animator.Play("IDLE_FRONT");
                    _sprite.flipX = false;
                    break;
                case MoveDir.Left:
                    _animator.Play("IDLE_RIGHT");
                    _sprite.flipX = true;
                    break;
                case MoveDir.Right:
                    _animator.Play("IDLE_RIGHT");
                    _sprite.flipX = false;
                    break;
            }
        }
        else if(_state == CreatureState.Moving)
        {
            switch (_dir)
            {
                case MoveDir.Up:
                    _animator.Play("WALK_BACK");
                    _sprite.flipX = false;

                    break;
                case MoveDir.Down:
                    _animator.Play("WALK_FRONT");
                    _sprite.flipX = false;

                    break;
                case MoveDir.Left:
                    _animator.Play("WALK_RIGHT");
                    _sprite.flipX = true;
                    break;
                case MoveDir.Right:
                    _animator.Play("WALK_RIGHT");
                    _sprite.flipX = false;
                    break;
            }
        }
        else if(_state == CreatureState.Skill)
        {
            switch (_lastDir)
            {
                case MoveDir.Up:
                    _animator.Play("ATTACK_BACK");
                    _sprite.flipX = false;

                    break;
                case MoveDir.Down:
                    _animator.Play("ATTACK_FRONT");
                    _sprite.flipX = false;

                    break;
                case MoveDir.Left:
                    _animator.Play("ATTACK_RIGHT");
                    _sprite.flipX = true;
                    break;
                case MoveDir.Right:
                    _animator.Play("ATTACK_RIGHT");
                    _sprite.flipX = false;
                    break;
            }
        }
        else
        {
            return;
        }
    }
    ...

    protected virtual void UpdateController()
    {
        // change method name
        switch (State)
        {
            case CreatureState.Idle:
                UpdateIdle();
                break;
            case CreatureState.Moving:
                UpdateMoving();
                break;
            case CreatureState.Skill:
                break;
            case CreatureState.Dead:
                break;
        }
    }

    protected virtual void UpdateIdle()
    {
        if (_dir != MoveDir.None)
        {
            Vector3Int destPos = CellPos;

            switch (_dir)
            {
                case MoveDir.Up:
                    destPos += Vector3Int.up;
                    break;
                case MoveDir.Down:
                    destPos += Vector3Int.down;
                    break;
                case MoveDir.Left:
                    destPos += Vector3Int.left;
                    break;
                case MoveDir.Right:
                    destPos += Vector3Int.right;
                    break;
                default:
                    break;
            }

            State = CreatureState.Moving;

            if (Managers.Map.CanGo(destPos))
            {
                if (Managers.Object.Find(destPos) == null)
                {
                    CellPos = destPos;
                }

            }
        }
    }

    protected virtual void UpdateMoving()
    {
        Vector3 destPos = Managers.Map.CurrentGrid.CellToWorld(CellPos) + new Vector3(0.5f, 0.5f);
        Vector3 moveDir = destPos - transform.position;

        float dist = moveDir.magnitude;
        if (dist < _speed * Time.deltaTime)
        {
            transform.position = destPos;
            _state = CreatureState.Idle;
            if (_dir == MoveDir.None)
                UpdateAnimation();
        }
        else
        {
            transform.position += moveDir.normalized * _speed * Time.deltaTime;
            State = CreatureState.Moving;
        }
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
    // for skill tick time 
    Coroutine _coSkill;
    ...

    protected override void UpdateController()
    {
        switch (State)
        {
            // player can use skill when his state is Idle
            case CreatureState.Idle:
                GetDirInput();
                GetIdleInput();
                break;
            case CreatureState.Moving:
                GetDirInput();
                break;
        }

        base.UpdateController();
    }
    ...

    void GetIdleInput()
    {
        if (Input.GetKey(KeyCode.Space))
        {
            State = CreatureState.Skill;
            _coSkill = StartCoroutine("CoStartPunch");
        }
    }

    IEnumerator CoStartPunch()
    {
        // detection
        GameObject go = Managers.Object.Find(GetFrontCellPos());
        if (go != null)
        {
            Debug.Log(go.name);
        }

        // wait time
        yield return new WaitForSeconds(0.5f);
        State = CreatureState.Idle;
        _coSkill = null;
    }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Attack.mp4" frameborder="0"> </iframe>

## Skill : Arrow

* Arrow
  - set `Pixel Per Unit` of `Tiny RPG Forest\Artwork\sprites\misc\Arrow` to 20
  - Create `Arrow` Prefab and Change `Order in Layer` to 11
  - add `ArrowController.cs` Compoenent in `Arrow` Prefab

<figure class="third">
  <a href="/assets/img/posts/unity_mmogame/32.jpg"><img src="/assets/img/posts/unity_mmogame/32.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/33.jpg"><img src="/assets/img/posts/unity_mmogame/33.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/34.jpg"><img src="/assets/img/posts/unity_mmogame/34.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Scripts\Controllers\Creature\ArrowController.cs
{% highlight C# %}
public class ArrowController : CreatureController
{
    protected override void Init()
    {
        // TODO
        // change arrow direction
        switch (_lastDir)
        {
            case MoveDir.Up:
                transform.rotation = Quaternion.Euler(0, 0, 0);
                break;
            case MoveDir.Down:
                transform.rotation = Quaternion.Euler(0, 0, -180);
                break;
            case MoveDir.Left:
                transform.rotation = Quaternion.Euler(0, 0, 90);
                break;
            case MoveDir.Right:
                transform.rotation = Quaternion.Euler(0, 0, -90);
                break;
        }
        base.Init();
    }

    protected override void UpdateAnimation()
    {
        
    }

    protected override void UpdateIdle()
    {
        if (_dir != MoveDir.None)
        {
            Vector3Int destPos = CellPos;

            switch (_dir)
            {
                case MoveDir.Up:
                    destPos += Vector3Int.up;
                    break;
                case MoveDir.Down:
                    destPos += Vector3Int.down;
                    break;
                case MoveDir.Left:
                    destPos += Vector3Int.left;
                    break;
                case MoveDir.Right:
                    destPos += Vector3Int.right;
                    break;
                default:
                    break;
            }

            State = CreatureState.Moving;

            if (Managers.Map.CanGo(destPos))
            {
                GameObject go = Managers.Object.Find(destPos);
                if (Managers.Object.Find(destPos) == null)
                {
                    CellPos = destPos;
                }
                else
                {
                    Debug.Log(go.name);
                    Managers.Resource.Destroy(gameObject);
                }
            }
            else
            {
                Managers.Resource.Destroy(gameObject);
            }
        }
    }
}
{% endhighlight %}

* CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    public float _speed = 5.0f;

    public Vector3Int CellPos { get; set; } = Vector3Int.zero;
    protected Animator _animator;
    protected SpriteRenderer _sprite;

    protected CreatureState _state = CreatureState.Idle;
    ...

    protected MoveDir _lastDir = MoveDir.Down;
    protected MoveDir _dir = MoveDir.Down;
    ...
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
    Coroutine _coSkill;
    bool _rangeSkill = false;
    ...

    protected override void UpdateAnimation()
    {
        ...
        else if (_state == CreatureState.Skill)
        {
            switch (_lastDir)
            {
                case MoveDir.Up:
                    _animator.Play(_rangeSkill ? "ATTACK_WEAPON_BACK" : "ATTACK_BACK");
                    _sprite.flipX = false;
                    break;
                case MoveDir.Down:
                    _animator.Play(_rangeSkill ? "ATTACK_WEAPON_FRONT" : "ATTACK_FRONT");
                    _sprite.flipX = false;
                    break;
                case MoveDir.Left:
                    _animator.Play(_rangeSkill ? "ATTACK_WEAPON_RIGHT" : "ATTACK_RIGHT");
                    _sprite.flipX = true;
                    break;
                case MoveDir.Right:
                    _animator.Play(_rangeSkill ? "ATTACK_WEAPON_RIGHT" : "ATTACK_RIGHT");
                    _sprite.flipX = false;
                    break;
            }
        }
        else
        {
            return;
        }
    }
    ...

    void GetIdleInput()
    {
        if (Input.GetKey(KeyCode.Space))
        {
            State = CreatureState.Skill;
            //_coSkill = StartCoroutine("CoStartPunch");
            _coSkill = StartCoroutine("CoStartShootArrow");
        }
    }

    IEnumerator CoStartPunch()
    {
        ...

        _rangeSkill = false;
        yield return new WaitForSeconds(0.5f);
        State = CreatureState.Idle;
        _coSkill = null;
    }

    IEnumerator CoStartShootArrow()
    {
        GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
        ArrowController ac = go.GetComponent<ArrowController>();
        ac.Dir = _lastDir;
        ac.CellPos = CellPos;

        // wait time
        _rangeSkill = true;
        yield return new WaitForSeconds(0.3f);
        State = CreatureState.Idle;
        _coSkill = null;
    }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Arrow.mp4" frameborder="0"> </iframe>

## Effect

* Animation
  - Create `Effect\DieEffect` Prefab
  - Create animation controller and change name to `Resources\Animations\Effect\EffectAnimController`
  - Add `Animator` Component and link `EffectAnimController`

<figure class="third">
  <a href="/assets/img/posts/unity_mmogame/35.jpg"><img src="/assets/img/posts/unity_mmogame/35.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/36.jpg"><img src="/assets/img/posts/unity_mmogame/36.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/38.jpg"><img src="/assets/img/posts/unity_mmogame/38.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

  - Create `Assets\Tiny RPG Forest\Artwork\sprites\misc\enemy-death` sprites to animation and change name to `Start`
  - change `Pixel Per Unit` of `enemy-death` sprites to 20 

<figure>
  <a href="/assets/img/posts/unity_mmogame/37.jpg"><img src="/assets/img/posts/unity_mmogame/37.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    ...

    protected virtual void UpdateDead()
    {

    }
    
    public virtual void OnDamaged()
    {

    }
}
{% endhighlight %}

* MonsterController.cs
{% highlight C# %}
public class MonsterController : CreatureController
{
    ...

    // you can use OnDemaged in others
    public override void OnDamaged()
    {
        GameObject effect = Managers.Resource.Instantiate("Effect/DieEffect");
        effect.transform.position = transform.position;
        effect.GetComponent<Animator>().Play("START");
        GameObject.Destroy(effect, 0.5f);

        // for destroy monster
        // you should remove object for collision
        Managers.Object.Remove(gameObject);
        Managers.Resource.Destroy(gameObject);
    }
}
{% endhighlight %}

* ArrowController.cs
{% highlight C# %}
public class ArrowController : CreatureController
{
    ...

    protected override void UpdateIdle()
    {
        if (_dir != MoveDir.None)
        {
            ...

            if (Managers.Map.CanGo(destPos))
            {
                GameObject go = Managers.Object.Find(destPos);
                if (Managers.Object.Find(destPos) == null)
                {
                    CellPos = destPos;
                }
                else
                {
                    CreatureController cc = go.GetComponent<CreatureController>();
                    if (cc != null)
                        cc.OnDamaged();

                    Managers.Resource.Destroy(gameObject);
                }
            }
            else
            {
                Managers.Resource.Destroy(gameObject);
            }
        }
    }
}
{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
    ...

    IEnumerator CoStartPunch()
    {
        GameObject go = Managers.Object.Find(GetFrontCellPos());
        if (go != null)
        {
            CreatureController cc = go.GetComponent<CreatureController>();
            if (cc != null)
                cc.OnDamaged();
        }

        _rangeSkill = false;
        yield return new WaitForSeconds(0.5f);
        State = CreatureState.Idle;
        _coSkill = null;
    }

    IEnumerator CoStartShootArrow()
    {
        GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
        ArrowController ac = go.GetComponent<ArrowController>();
        ac.Dir = _lastDir;
        ac.CellPos = CellPos;

        // wait time
        _rangeSkill = true;
        yield return new WaitForSeconds(0.3f);
        State = CreatureState.Idle;
        _coSkill = null;
    }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Effect.mp4" frameborder="0"> </iframe>

## AI: Patrol
* CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    ...
    public virtual CreatureState State
    {
        get { return _state; }
        set
        {
            if (_state == value)
                return;

            _state = value;
            UpdateAnimation();
        }
    }
    ...

    protected virtual void UpdateIdle()
    {
    }

    protected virtual void UpdateMoving()
    {
        ...
        if (dist < _speed * Time.deltaTime)
        {
            transform.position = destPos;
            MoveToNextPos();
        }
        ...
    }

    protected virtual void MoveToNextPos()
    {
        if(_dir == MoveDir.None)
        {
            State = CreatureState.Idle;
            return;
        }

        Vector3Int destPos = CellPos;

        switch (_dir)
        {
            case MoveDir.Up:
                destPos += Vector3Int.up;
                break;
            case MoveDir.Down:
                destPos += Vector3Int.down;
                break;
            case MoveDir.Left:
                destPos += Vector3Int.left;
                break;
            case MoveDir.Right:
                destPos += Vector3Int.right;
                break;
            default:
                break;
        }

        if (Managers.Map.CanGo(destPos))
        {
            if (Managers.Object.Find(destPos) == null)
            {
                CellPos = destPos;
            }
        }
    }
    ...
}

{% endhighlight %}

* PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
    ...
    protected override void UpdateController()
    {
        switch (State)
        {
            case CreatureState.Idle:
                GetDirInput();
                break;
            case CreatureState.Moving:
                GetDirInput();
                break;
        }

        base.UpdateController();
    }
    ...

    protected override void UpdateIdle()
    {
        // check where to go
        if(Dir != MoveDir.None)
        {
            State = CreatureState.Moving;
            return;
        }

        // check weather state change to skill 
        if (Input.GetKey(KeyCode.Space))
        {
            State = CreatureState.Skill;
            //_coSkill = StartCoroutine("CoStartPunch");
            _coSkill = StartCoroutine("CoStartShootArrow");
        }
    }
    ...
}
{% endhighlight %}

* ArrowController.cs
{% highlight C# %}
public class ArrowController : CreatureController
{
    protected override void Init()
    {
        // TODO
        ...
        State = CreatureState.Moving;
        _speed = 15.0f;

        base.Init();
    }

    protected override void UpdateAnimation()
    {
    }

    protected override void MoveToNextPos()
    {
        Vector3Int destPos = CellPos;

        switch (_dir)
        {
            case MoveDir.Up:
                destPos += Vector3Int.up;
                break;
            case MoveDir.Down:
                destPos += Vector3Int.down;
                break;
            case MoveDir.Left:
                destPos += Vector3Int.left;
                break;
            case MoveDir.Right:
                destPos += Vector3Int.right;
                break;
            default:
                break;
        }

        if (Managers.Map.CanGo(destPos))
        {
            GameObject go = Managers.Object.Find(destPos);
            if (Managers.Object.Find(destPos) == null)
            {
                CellPos = destPos;
            }
            else
            {
                CreatureController cc = go.GetComponent<CreatureController>();
                if (cc != null)
                    cc.OnDamaged();

                Managers.Resource.Destroy(gameObject);
            }
        }
        else
        {
            Managers.Resource.Destroy(gameObject);
        }
    }
}
{% endhighlight %}

* MonsterController.cs
{% highlight C# %}
public class MonsterController : CreatureController
{
    // Patrol: move randomly
    Coroutine _coPatrol;
    Vector3Int _destCellPos;

    public override CreatureState State
    {
        get { return _state; }
        set
        {
            if (_state == value)
                return;

            base.State = value;
            
            if (_coPatrol != null)
            {
                StopCoroutine(_coPatrol);
                _coPatrol = null;
            }
        }
    }

    protected override void Init()
    {
        base.Init();
        State = CreatureState.Idle;
        Dir = MoveDir.None;
    }

    protected override void UpdateIdle()
    {
        base.UpdateIdle();

        if(_coPatrol == null)
        {
            _coPatrol = StartCoroutine("CoPatrol");
        }
    }

    protected override void MoveToNextPos()
    {
        Vector3Int moveCellDir = _destCellPos - CellPos;
        // TODO:Astar
        if (moveCellDir.x > 0)
            Dir = MoveDir.Right;
        else if (moveCellDir.x < 0)
            Dir = MoveDir.Left;
        else if (moveCellDir.y > 0)
            Dir = MoveDir.Up;
        else if (moveCellDir.y < 0)
            Dir = MoveDir.Down;
        else
            Dir = MoveDir.None;

        Vector3Int destPos = CellPos;

        switch (_dir)
        {
            case MoveDir.Up:
                destPos += Vector3Int.up;
                break;
            case MoveDir.Down:
                destPos += Vector3Int.down;
                break;
            case MoveDir.Left:
                destPos += Vector3Int.left;
                break;
            case MoveDir.Right:
                destPos += Vector3Int.right;
                break;
            default:
                break;
        }

        if (Managers.Map.CanGo(destPos) && Managers.Object.Find(destPos) == null)
        {
            CellPos = destPos;
        }
        else
        {
            State = CreatureState.Idle;
        }
    }

    public override void OnDamaged()
    {
        GameObject effect = Managers.Resource.Instantiate("Effect/DieEffect");
        effect.transform.position = transform.position;
        effect.GetComponent<Animator>().Play("START");
        GameObject.Destroy(effect, 0.5f);

        Managers.Object.Remove(gameObject);
        Managers.Resource.Destroy(gameObject);
    }

    IEnumerator CoPatrol()
    {
        int waitSeconds = Random.Range(1, 4);
        yield return new WaitForSeconds(waitSeconds);

        for(int i=0; i<10; i++)
        {
            int xRange = Random.Range(-5, 6);
            int yRange = Random.Range(-5, 6);
            Vector3Int randPos = CellPos + new Vector3Int(xRange, yRange, 0);

            if(Managers.Map.CanGo(randPos) && Managers.Object.Find(randPos) == null)
            {
                _destCellPos = randPos;
                State = CreatureState.Moving;
                // get out from corutine
                yield break;
            }
        }
        State = CreatureState.Idle;
    }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-PatrolAI.mp4" frameborder="0"> </iframe>

## AI: Search
  - add A* Algorithm
  - PriorityQueue is on <a href="https://leehuhlee.github.io/C-AStar-Maze/">A* Maze</a> Post

* Scripts\Utils\PriorityQueue.cs
{% highlight C# %}
public class PriorityQueue<T> where T : IComparable<T>
{
	List<T> _heap = new List<T>();

	public void Push(T data)
	{
		_heap.Add(data);

		int now = _heap.Count - 1;
		while (now > 0)
		{
			int next = (now - 1) / 2;
			if (_heap[now].CompareTo(_heap[next]) < 0)
				break;

			T temp = _heap[now];
			_heap[now] = _heap[next];
			_heap[next] = temp;

			now = next;
		}
	}

	public T Pop()
	{
		T ret = _heap[0];

		int lastIndex = _heap.Count - 1;
		_heap[0] = _heap[lastIndex];
		_heap.RemoveAt(lastIndex);
		lastIndex--;

		int now = 0;
		while (true)
		{
			int left = 2 * now + 1;
			int right = 2 * now + 2;

			int next = now;
			if (left <= lastIndex && _heap[next].CompareTo(_heap[left]) < 0)
				next = left;
			if (right <= lastIndex && _heap[next].CompareTo(_heap[right]) < 0)
				next = right;

			if (next == now)
				break;

			T temp = _heap[now];
			_heap[now] = _heap[next];
			_heap[next] = temp;
			now = next;
		}

		return ret;
	}

	public int Count { get { return _heap.Count; } }
}
{% endhighlight %}

* MapController.cs
{% highlight C# %}
public struct Pos
{
	public Pos(int y, int x) { Y = y; X = x; }
	public int Y;
	public int X;
}

public struct PQNode : IComparable<PQNode>
{
	public int F;
	public int G;
	public int Y;
	public int X;

	public int CompareTo(PQNode other)
	{
		if (F == other.F)
			return 0;
		return F < other.F ? 1 : -1;
	}
}

public class MapManager
{
	public Grid CurrentGrid { get; private set; }

	public int MinX { get; set; }
	public int MaxX { get; set; }
	public int MinY { get; set; }
	public int MaxY { get; set; }

	public int SizeX { get { return MaxX - MinX + 1; } }
	public int SizeY { get { return MaxY - MinY + 1; } }

	bool[,] _collision;

	public bool CanGo(Vector3Int cellPos)
	{
		if (cellPos.x < MinX || cellPos.x > MaxX)
			return false;
		if (cellPos.y < MinY || cellPos.y > MaxY)
			return false;

		int x = cellPos.x - MinX;
		int y = MaxY - cellPos.y;
		return !_collision[y, x];
	}

	public void LoadMap(int mapId)
	{
		DestroyMap();

		string mapName = "Map_" + mapId.ToString("000");
		GameObject go = Managers.Resource.Instantiate($"Map/{mapName}");
		go.name = "Map";

		GameObject collision = Util.FindChild(go, "Tilemap_Collision", true);
		if (collision != null)
			collision.SetActive(false);

		CurrentGrid = go.GetComponent<Grid>();

		// Collision 관련 파일
		TextAsset txt = Managers.Resource.Load<TextAsset>($"Map/{mapName}");
		StringReader reader = new StringReader(txt.text);

		MinX = int.Parse(reader.ReadLine());
		MaxX = int.Parse(reader.ReadLine());
		MinY = int.Parse(reader.ReadLine());
		MaxY = int.Parse(reader.ReadLine());

		int xCount = MaxX - MinX + 1;
		int yCount = MaxY - MinY + 1;
		_collision = new bool[yCount, xCount];

		for (int y = 0; y < yCount; y++)
		{
			string line = reader.ReadLine();
			for (int x = 0; x < xCount; x++)
			{
				_collision[y, x] = (line[x] == '1' ? true : false);
			}
		}
	}

	public void DestroyMap()
	{
		GameObject map = GameObject.Find("Map");
		if (map != null)
		{
			GameObject.Destroy(map);
			CurrentGrid = null;
		}
	}

	#region A* PathFinding

	// U D L R
	int[] _deltaY = new int[] { 1, -1, 0, 0 };
	int[] _deltaX = new int[] { 0, 0, -1, 1 };
	int[] _cost = new int[] { 10, 10, 10, 10 };

    // startCellPos is monster position
    // destCellPos is player position
    // ignoreDestCollision is for ignoring collision of last position(player)
	public List<Vector3Int> FindPath(Vector3Int startCellPos, Vector3Int destCellPos, bool ignoreDestCollision = false)
	{
		List<Pos> path = new List<Pos>();

		bool[,] closed = new bool[SizeY, SizeX]; // CloseList

		int[,] open = new int[SizeY, SizeX]; // OpenList
		for (int y = 0; y < SizeY; y++)
			for (int x = 0; x < SizeX; x++)
				open[y, x] = Int32.MaxValue;

		Pos[,] parent = new Pos[SizeY, SizeX];

		PriorityQueue<PQNode> pq = new PriorityQueue<PQNode>();

		// CellPos -> ArrayPos
		Pos pos = Cell2Pos(startCellPos);
		Pos dest = Cell2Pos(destCellPos);

		open[pos.Y, pos.X] = 10 * (Math.Abs(dest.Y - pos.Y) + Math.Abs(dest.X - pos.X));
		pq.Push(new PQNode() { F = 10 * (Math.Abs(dest.Y - pos.Y) + Math.Abs(dest.X - pos.X)), G = 0, Y = pos.Y, X = pos.X });
		parent[pos.Y, pos.X] = new Pos(pos.Y, pos.X);

		while (pq.Count > 0)
		{
			PQNode node = pq.Pop();
			if (closed[node.Y, node.X])
				continue;

			closed[node.Y, node.X] = true;
			if (node.Y == dest.Y && node.X == dest.X)
				break;

			for (int i = 0; i < _deltaY.Length; i++)
			{
				Pos next = new Pos(node.Y + _deltaY[i], node.X + _deltaX[i]);

				if (!ignoreDestCollision || next.Y != dest.Y || next.X != dest.X)
				{
					if (CanGo(Pos2Cell(next)) == false) // CellPos
						continue;
				}
				
				if (closed[next.Y, next.X])
					continue;

				int g = 0;// node.G + _cost[i];
				int h = 10 * ((dest.Y - next.Y) * (dest.Y - next.Y) + (dest.X - next.X) * (dest.X - next.X));
				if (open[next.Y, next.X] < g + h)
					continue;

				open[dest.Y, dest.X] = g + h;
				pq.Push(new PQNode() { F = g + h, G = g, Y = next.Y, X = next.X });
				parent[next.Y, next.X] = new Pos(node.Y, node.X);
			}
		}

		return CalcCellPathFromParent(parent, dest);
	}

	List<Vector3Int> CalcCellPathFromParent(Pos[,] parent, Pos dest)
	{
		List<Vector3Int> cells = new List<Vector3Int>();

		int y = dest.Y;
		int x = dest.X;
		while (parent[y, x].Y != y || parent[y, x].X != x)
		{
			cells.Add(Pos2Cell(new Pos(y, x)));
			Pos pos = parent[y, x];
			y = pos.Y;
			x = pos.X;
		}
		cells.Add(Pos2Cell(new Pos(y, x)));
		cells.Reverse();

		return cells;
	}

	Pos Cell2Pos(Vector3Int cell)
	{
		// CellPos -> ArrayPos
		return new Pos(MaxY - cell.y, cell.x - MinX);
	}

	Vector3Int Pos2Cell(Pos pos)
	{
		// ArrayPos -> CellPos
		return new Vector3Int(pos.X + MinX, MaxY - pos.Y, 0);
	}

	#endregion
}
{% endhighlight %}

* ObjectManager.cs
{% highlight C# %}
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ObjectManager
{
	...
	public GameObject Find(Vector3Int cellPos)
	{
		foreach (GameObject obj in _objects)
		{
			CreatureController cc = obj.GetComponent<CreatureController>();
			if (cc == null)
				continue;

			if (cc.CellPos == cellPos)
				return obj;
		}

		return null;
	}

    // delegate
    // act with GameObject and return bool
	public GameObject Find(Func<GameObject, bool> condition)
	{
		foreach (GameObject obj in _objects)
		{
			if (condition.Invoke(obj))
				return obj;
		}

		return null;
	}
    ...
}
{% endhighlight %}

* MonsterController.cs
{% highlight C# %}
public class MonsterController : CreatureController
{
	Coroutine _coPatrol;
	Coroutine _coSearch;

	[SerializeField]
	Vector3Int _destCellPos;

	[SerializeField]
	GameObject _target;

	[SerializeField]
	float _searchRange = 5.0f;

	public override CreatureState State
	{
		get { return _state; }
		set
		{
			if (_state == value)
				return;

			base.State = value;

			if (_coPatrol != null)
			{
				StopCoroutine(_coPatrol);
				_coPatrol = null;
			}

			if (_coSearch != null)
			{
				StopCoroutine(_coSearch);
				_coSearch = null;
			}
		}
	}

	protected override void Init()
	{
		base.Init();

		State = CreatureState.Idle;
		Dir = MoveDir.None;

		_speed = 3.0f;
	}

	protected override void UpdateIdle()
	{
		base.UpdateIdle();

		if (_coPatrol == null)
		{
			_coPatrol = StartCoroutine("CoPatrol");
		}

		if (_coSearch == null)
		{
			_coSearch = StartCoroutine("CoSearch");
		}
	}

	protected override void MoveToNextPos()
	{
		Vector3Int destPos = _destCellPos;
		if (_target != null)
		{
			destPos = _target.GetComponent<CreatureController>().CellPos;
		}

		List<Vector3Int> path = Managers.Map.FindPath(CellPos, destPos, ignoreDestCollision: true);
        // 0 means Idle
        // 1 means it coudln't find path
        // 11 means player is too far
		if (path.Count < 2 || (_target != null && path.Count > 10))
		{
			_target = null;
			State = CreatureState.Idle;
			return;
		}

		Vector3Int nextPos = path[1];
		Vector3Int moveCellDir = nextPos - CellPos;

		if (moveCellDir.x > 0)
			Dir = MoveDir.Right;
		else if (moveCellDir.x < 0)
			Dir = MoveDir.Left;
		else if (moveCellDir.y > 0)
			Dir = MoveDir.Up;
		else if (moveCellDir.y < 0)
			Dir = MoveDir.Down;
		else
			Dir = MoveDir.None;

		if (Managers.Map.CanGo(nextPos) && Managers.Object.Find(nextPos) == null)
		{
			CellPos = nextPos;
		}
		else
		{
			State = CreatureState.Idle;
		}
	}
	...

	IEnumerator CoSearch()
	{
		while (true)
		{
			yield return new WaitForSeconds(1);

			if (_target != null)
				continue;

			_target = Managers.Object.Find((go) =>
			{
				PlayerController pc = go.GetComponent<PlayerController>();
				if (pc == null)
					return false;

				Vector3Int dir = (pc.CellPos - CellPos);
				if (dir.magnitude > _searchRange)
					return false;

				return true;
			});
		}
	}
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-SearchAI.mp4" frameborder="0"> </iframe>

## AI: Skill

* CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
    ...
	public MoveDir GetDirFromVec(Vector3Int dir) 
	{
		if (dir.x > 0)
			return MoveDir.Right;
		else if (dir.x < 0)
			return MoveDir.Left;
		else if (dir.y > 0)
			return MoveDir.Up;
		else if (dir.y < 0)
			return MoveDir.Down;
		else
			return MoveDir.None;
	}
	...
}
{% endhighlight %}

* MonsterController.cs
{% highlight C# %}
public class MonsterController : CreatureController
{
	Coroutine _coSkill;
	Coroutine _coPatrol;
	Coroutine _coSearch;
	...

	[SerializeField]
	float _skillRange = 1.0f;

	[SerializeField]
	bool _rangedSkill = false;

	...
	protected override void Init()
	{
		base.Init();

		State = CreatureState.Idle;
		Dir = MoveDir.None;

		_speed = 3.0f;
        _rangedSkill = (Random.Range(0, 2) == 0 ? true : false);

        if (_rangedSkill)
			_skillRange = 10.0f;
		else
			_skillRange = 1.0f;
	}
	...

	protected override void MoveToNextPos()
	{
		Vector3Int destPos = _destCellPos;
		if (_target != null)
		{
			destPos = _target.GetComponent<CreatureController>().CellPos;

			Vector3Int dir = destPos - CellPos;
            if (dir.magnitude <= _skillRange && (dir.x ==0 || dir.y==0))
            {
				Dir = GetDirFromVec(dir);
				State = CreatureState.Skill;

				if (_rangedSkill)
					_coSkill = StartCoroutine("CoStartShootArrow");
				else
					_coSkill = StartCoroutine("CoStartPunch");
				return;
            }
		}

		List<Vector3Int> path = Managers.Map.FindPath(CellPos, destPos, ignoreDestCollision: true);
		if (path.Count < 2 || (_target != null && path.Count > 20))
		{
			_target = null;
			State = CreatureState.Idle;
			return;
		}

		Vector3Int nextPos = path[1];
		Vector3Int moveCellDir = nextPos - CellPos;

		Dir = GetDirFromVec(moveCellDir);

		if (Managers.Map.CanGo(nextPos) && Managers.Object.Find(nextPos) == null)
		{
			CellPos = nextPos;
		}
		else
		{
			State = CreatureState.Idle;
		}
	}
    ...

	IEnumerator CoPatrol()
	{
		...
		for (int i = 0; i < 10; i++)
		{
			...

			if (Managers.Map.CanGo(randPos) && Managers.Object.Find(randPos) == null)
			{
				_destCellPos = randPos;
				State = CreatureState.Moving;
				yield break;
			}
		}

		State = CreatureState.Idle;
	}

	...

	IEnumerator CoStartPunch()
	{
		GameObject go = Managers.Object.Find(GetFrontCellPos());
		if (go != null)
		{
			CreatureController cc = go.GetComponent<CreatureController>();
			if (cc != null)
				cc.OnDamaged();
		}

		yield return new WaitForSeconds(0.5f);
		State = CreatureState.Moving;
		_coSkill = null;
	}

	IEnumerator CoStartShootArrow()
	{
		GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
		ArrowController ac = go.GetComponent<ArrowController>();
		ac.Dir = _lastDir;
		ac.CellPos = CellPos;

		yield return new WaitForSeconds(0.3f);
		State = CreatureState.Moving;
		_coSkill = null;
	}
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-SkillAI.mp4" frameborder="0"> </iframe>

# Server

## Protobuf in Server
* Create `Common` folder
* Download <a href="https://github.com/protocolbuffers/protobuf/releases/tag/v3.12.3">Google Protobuf protoc-3.12.3-winXX.zip</a> on Common folder
* Download `Google.Protobuf` in Nuget Package Manager

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/41.jpg"><img src="/assets/img/posts/unity_mmogame/41.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/42.jpg"><img src="/assets/img/posts/unity_mmogame/42.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/43.jpg"><img src="/assets/img/posts/unity_mmogame/43.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

* Common\protoc-3.12.3-win64\bin\Protocol.proto
{% highlight proto %}
syntax = "proto3";

package Protocol;
import "google/protobuf/timestamp.proto";
option csharp_namespace = "Google.Protobuf.Protocol";

enum MsgId {
  PERSON = 0;
  C_CHAT = 1;
  S_CHAT = 2;
  S_ENTER_GAME = 3;
}

message C_Chat {
  string context = 1;
}

message S_Chat {
  string context = 1;
}

message S_EnterGame {
  string context = 1;
}

message Person {
  int32 packetHeader = 1;
  string name = 2;
  int32 id = 3;  // Unique ID number for this person.
  string email = 4;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
  }

  repeated PhoneNumber phones = 5;
  google.protobuf.Timestamp last_updated = 6;
}

// Our address book file is just one of these.
message AddressBook {
  repeated Person people = 1;
}
{% endhighlight %}

* Common\protoc-3.12.3-win64\bin\GenProto.bat
{% highlight bat %}
protoc.exe -I=./ --csharp_out=./ ./Protocol.proto 
IF ERRORLEVEL 1 PAUSE

START ../../../Server/PacketGenerator/bin/PacketGenerator.exe ./Protocol.proto
XCOPY /Y Protocol.cs "../../../Client/Assets/Scripts/Packet"
XCOPY /Y Protocol.cs "../../../Server/Server/Packet"
XCOPY /Y ClientPacketManager.cs "../../../Client/Assets/Scripts/Packet"
XCOPY /Y ServerPacketManager.cs "../../../Server/Server/Packet"
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
namespace Server
{
	class ClientSession : PacketSession
	{
		public int SessionId { get; set; }

		public override void OnConnected(EndPoint endPoint)
		{
			Console.WriteLine($"OnConnected : {endPoint}");

			// PROTO Test
			S_Chat chat = new S_Chat()
			{
				Context = "Hello!"
			};

			ushort size = (ushort)chat.CalculateSize();
			byte[] sendBuffer = new byte[size + 4];
			Array.Copy(BitConverter.GetBytes(size + 4), 0, sendBuffer, 0, sizeof(ushort));
			ushort protocolId = (ushort)MsgId.SChat;
			Array.Copy(BitConverter.GetBytes(protocolId), 0, sendBuffer, 2, sizeof(ushort));
			Array.Copy(chat.ToByteArray(), 0, sendBuffer, 4, size);

			Send(new ArraySegment<byte>(sendBuffer));			
		}

		public override void OnRecvPacket(ArraySegment<byte> buffer)
		{
			PacketManager.Instance.OnRecvPacket(this, buffer);
		}

		public override void OnDisconnected(EndPoint endPoint)
		{
			SessionManager.Instance.Remove(this);

			Console.WriteLine($"OnDisconnected : {endPoint}");
		}

		public override void OnSend(int numOfBytes){}
	}
}
{% endhighlight %}

* PacketGenerator\PacketFormat.cs
{% highlight C# %}
namespace PacketGenerator
{
	class PacketFormat
	{
		// {0} Packet Assign
		public static string managerFormat =
@"using Google.Protobuf;
using Google.Protobuf.Protocol;
using ServerCore;
using System;
using System.Collections.Generic;

class PacketManager
{
	#region Singleton
	static PacketManager _instance = new PacketManager();
	public static PacketManager Instance { get { return _instance; } }
	#endregion

	PacketManager()
	{
		Register();
	}

	Dictionary<ushort, Action<PacketSession, ArraySegment<byte>, ushort>> _onRecv = new Dictionary<ushort, Action<PacketSession, ArraySegment<byte>, ushort>>();
	Dictionary<ushort, Action<PacketSession, IMessage>> _handler = new Dictionary<ushort, Action<PacketSession, IMessage>>();
		
	public void Register()
    {
	    {0}
	}

	public void OnRecvPacket(PacketSession session, ArraySegment<byte> buffer)
	{
		ushort count = 0;

		ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
		count += 2;
		ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
		count += 2;

		Action<PacketSession, ArraySegment<byte>, ushort> action = null;
		if (_onRecv.TryGetValue(id, out action))
			action.Invoke(session, buffer, id);
	}

	void MakePacket<T>(PacketSession session, ArraySegment<byte> buffer, ushort id) where T : IMessage, new()
	{
		T pkt = new T();
		pkt.MergeFrom(buffer.Array, buffer.Offset + 4, buffer.Count - 4);
		Action<PacketSession, IMessage> action = null;
		if (_handler.TryGetValue(id, out action))
			action.Invoke(session, pkt);
	}

	public Action<PacketSession, IMessage> GetPacketHandler(ushort id)
	{
		Action<PacketSession, IMessage> action = null;
		if (_handler.TryGetValue(id, out action))
			return action;
		return null;
	}
}";

		// {0} MsgId
		// {1} Packet Name 
		public static string managerRegisterFormat =
@"		
		_onRecv.Add((ushort)MsgId.{0}, MakePacket<{1}>);
		_handler.Add((ushort)MsgId.{0}, PacketHandler.{1}Handler);";

	}
}
{% endhighlight %}

* PacketGenerator\Program.cs
{% highlight C# %}
namespace PacketGenerator
{
	class Program
	{
		static string clientRegister;
		static string serverRegister;

		static void Main(string[] args)
		{
			string file = "../../../Common/protoc-3.12.3-win64/bin/Protocol.proto";
			if (args.Length >= 1)
				file = args[0];

			bool startParsing = false;
			foreach (string line in File.ReadAllLines(file))
			{
				if (!startParsing && line.Contains("enum MsgId"))
				{
					startParsing = true;
					continue;
				}

				if (!startParsing)
					continue;

				if (line.Contains("}"))
					break;

				string[] names = line.Trim().Split(" =");
				if (names.Length == 0)
					continue;

                // Client deals with Server message
				string name = names[0];
				if (name.StartsWith("S_"))
				{
					string[] words = name.Split("_");

					string msgName = "";
					foreach (string word in words)
						msgName += FirstCharToUpper(word);

					string packetName = $"S_{msgName.Substring(1)}";
					clientRegister += string.Format(PacketFormat.managerRegisterFormat, msgName, packetName);
				}
                // Server deals with Client message
				else if (name.StartsWith("C_"))
				{
					string[] words = name.Split("_");

					string msgName = "";
					foreach (string word in words)
						msgName += FirstCharToUpper(word);

					string packetName = $"C_{msgName.Substring(1)}";
					serverRegister += string.Format(PacketFormat.managerRegisterFormat, msgName, packetName);
				}
			}

			string clientManagerText = string.Format(PacketFormat.managerFormat, clientRegister);
			File.WriteAllText("ClientPacketManager.cs", clientManagerText);
			string serverManagerText = string.Format(PacketFormat.managerFormat, serverRegister);
			File.WriteAllText("ServerPacketManager.cs", serverManagerText);
		}

		public static string FirstCharToUpper(string input)
		{
			if (string.IsNullOrEmpty(input))
				return "";
			return input[0].ToString().ToUpper() + input.Substring(1).ToLower();
		}
	}
}
{% endhighlight %}

* Server\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void C_ChatHandler(PacketSession session, IMessage packet)
	{
		S_Chat chatPacket = packet as S_Chat;
		ClientSession serverSession = session as ClientSession;

		Console.WriteLine(chatPacket.Context);
	}
}
{% endhighlight %}

## Protobuf in Client

* Scripts\Managers\Contents\NetworkManager.cs
- NetworkManager is on <a href="https://leehuhlee.github.io/C-Chatting-Test//">Chatting Test</a> Post

{% highlight C# %}
public class NetworkManager
{
	ServerSession _session = new ServerSession();

	public void Send(ArraySegment<byte> sendBuff)
	{
		_session.Send(sendBuff);
	}

	public void Init()
	{
		string host = Dns.GetHostName();
		IPHostEntry ipHost = Dns.GetHostEntry(host);
		IPAddress ipAddr = ipHost.AddressList[0];
		IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

		Connector connector = new Connector();

		connector.Connect(endPoint,
			() => { return _session; },
			1);
	}

	public void Update()
	{
		List<PacketMessage> list = PacketQueue.Instance.PopAll();
		foreach (PacketMessage packet in list)
		{
			Action<PacketSession, IMessage> handler = PacketManager.Instance.GetPacketHandler(packet.Id);
			if (handler != null)
				handler.Invoke(_session, packet.Message);
		}	
	}
}
{% endhighlight %}

* Scripts\Managers\Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    static Managers s_instance;
    static Managers Instance { get { Init(); return s_instance; } }

    #region Contents
    MapManager _map = new MapManager();
    ObjectManager _obj = new ObjectManager();
    NetworkManager _network = new NetworkManager();

    public static MapManager Map { get { return Instance._map; } }
    public static ObjectManager Object { get { return Instance._obj; } }
    public static NetworkManager Network { get { return Instance._network; } }
	#endregion

	#region Core
	DataManager _data = new DataManager();
    PoolManager _pool = new PoolManager();
    ResourceManager _resource = new ResourceManager();
    SceneManagerEx _scene = new SceneManagerEx();
    SoundManager _sound = new SoundManager();
    UIManager _ui = new UIManager();

    public static DataManager Data { get { return Instance._data; } }
    public static PoolManager Pool { get { return Instance._pool; } }
    public static ResourceManager Resource { get { return Instance._resource; } }
    public static SceneManagerEx Scene { get { return Instance._scene; } }
    public static SoundManager Sound { get { return Instance._sound; } }
    public static UIManager UI { get { return Instance._ui; } }
	#endregion

	void Start()
    {
        Init();
	}

    void Update()
    {
        _network.Update();
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

            s_instance._network.Init();
            s_instance._data.Init();
            s_instance._pool.Init();
            s_instance._sound.Init();
        }		
	}

    public static void Clear()
    {
        Sound.Clear();
        Scene.Clear();
        UI.Clear();
        Pool.Clear();
    }
}
{% endhighlight %}

* Scripts\Packet\PacketQueue.cs
{% highlight C# %}
public class PacketMessage
{
	public ushort Id { get; set; }
	public IMessage Message { get; set; }
}

public class PacketQueue
{
	public static PacketQueue Instance { get; } = new PacketQueue();

	Queue<PacketMessage> _packetQueue = new Queue<PacketMessage>();
	object _lock = new object();

	public void Push(ushort id, IMessage packet)
	{
		lock (_lock)
		{
			_packetQueue.Enqueue(new PacketMessage() { Id = id, Message = packet });
		}
	}

	public PacketMessage Pop()
	{
		lock (_lock)
		{
			if (_packetQueue.Count == 0)
				return null;

			return _packetQueue.Dequeue();
		}
	}

	public List<PacketMessage> PopAll()
	{
		List<PacketMessage> list = new List<PacketMessage>();

		lock (_lock)
		{
			while (_packetQueue.Count > 0)
				list.Add(_packetQueue.Dequeue());
		}

		return list;
	}
}
{% endhighlight %}

* Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_ChatHandler(PacketSession session, IMessage packet)
	{
		S_Chat chatPacket = packet as S_Chat;
		ServerSession serverSession = session as ServerSession;

		Debug.Log(chatPacket.Context);
	}

	public static void S_EnterGameHandler(PacketSession session, IMessage packet)
	{
		S_EnterGame enterGamePacket = packet as S_EnterGame;
		ServerSession serverSession = session as ServerSession;
	}
}
{% endhighlight %}

* Libs
  - Create `Assets\Libs` folder
  - Copy `Server\Server\bin\Release\netcoreapp3.1\Google.Protobuf.dll` and `ServerCore.dll`, Paste on `Libs`

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/44.jpg"><img src="/assets/img/posts/unity_mmogame/44.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/45.jpg"><img src="/assets/img/posts/unity_mmogame/45.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

  - Download `System.Buffers` and `System.Runtime.CompilerServices.Unsafe` in Nuget Package Manager

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/46.jpg"><img src="/assets/img/posts/unity_mmogame/46.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/47.jpg"><img src="/assets/img/posts/unity_mmogame/47.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

  - Find each `.dll` files from `C:\Users\leehu\.nuget\packages` and copy on `Libs`

<figure class="third">
  <a href="/assets/img/posts/unity_mmogame/48.jpg"><img src="/assets/img/posts/unity_mmogame/48.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/49.jpg"><img src="/assets/img/posts/unity_mmogame/49.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/50.jpg"><img src="/assets/img/posts/unity_mmogame/50.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

### Test

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/39.jpg"><img src="/assets/img/posts/unity_mmogame/39.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/40.jpg"><img src="/assets/img/posts/unity_mmogame/40.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## MultiPlay Environment

## Packet Send

* Server\Session\ClientSession.cs
{% highlight C# %}
class ClientSession : PacketSession
{
    public int SessionId { get; set; }

    public void Send(IMessage packet)
    {
        // Find Packet Id By Packet Name
        string msgName = packet.Descriptor.Name.Replace("_", string.Empty);
        MsgId msgId = (MsgId)Enum.Parse(typeof(MsgId), msgName);

        ushort size = (ushort)packet.CalculateSize();
        byte[] sendBuffer = new byte[size + 4];
        Array.Copy(BitConverter.GetBytes(size + 4), 0, sendBuffer, 0, sizeof(ushort));
        Array.Copy(BitConverter.GetBytes((ushort)msgId), 0, sendBuffer, 2, sizeof(ushort));
        Array.Copy(packet.ToByteArray(), 0, sendBuffer, 4, size);

        Send(new ArraySegment<byte>(sendBuffer));
    }

    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected : {endPoint}");

        // PROTO Test
        S_Chat chat = new S_Chat()
        {
            Context = "Hello!"
        };
        Send(chat);
    }
    ...
}
{% endhighlight %}

## Security
* Delete `ServerCore.dll` from `Libs`
* Copy `Session.cs`, `RecvBuffer.cs` and `Connector.cs`, Paste them to `Assets\Scripts\ServerCore`
* Change `Console.WriteLine` to `Debug.Log`

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/55.jpg"><img src="/assets/img/posts/unity_mmogame/55.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/56.jpg"><img src="/assets/img/posts/unity_mmogame/56.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Build
* [Fild]-[Build Settings]
* Remove Scenes without `Scenes/Game` and Click Build on `Assets\Builds\WinXX\TestBuild`

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/51.jpg"><img src="/assets/img/posts/unity_mmogame/51.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/52.jpg"><img src="/assets/img/posts/unity_mmogame/52.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Multiplay

* Scripts\Scenes\GameScene.cs
{% highlight C# %}
protected override void Init()
{
    base.Init();

    SceneType = Define.Scene.Game;

    Managers.Map.LoadMap(1);

    // Window Size
    Screen.SetResolution(640, 480, false);
}
{% endhighlight %}

* Assets\Editor\MultiplayersBuildandRun.cs
{% highlight C# %}
public class MultiplayersBuildAndRun
{
    [MenuItem("Tools/Run Multiplayer/2 Players")]
    static void PerformWin64Build2()
    {
        PerformWin64Build(2);
    }

    [MenuItem("Tools/Run Multiplayer/3 Players")]
    static void PerformWin64Build3()
    {
        PerformWin64Build(3);
    }

    [MenuItem("Tools/Run Multiplayer/4 Players")]
    static void PerformWin64Build4()
    {
        PerformWin64Build(4);
    }

    static void PerformWin64Build(int playerCount)
    {
        EditorUserBuildSettings.SwitchActiveBuildTarget(BuildTargetGroup.Standalone, BuildTarget.StandaloneWindows);

        for(int i= 1; i<= playerCount; i++)
        {
            BuildPipeline.BuildPlayer(GetScenePaths(), "Builds/Win64/" + GetProjectName() + i.ToString() + "/" + GetProjectName() + i.ToString() + ".exe", BuildTarget.StandaloneWindows64, BuildOptions.AutoRunPlayer);
        }
    }

    static string GetProjectName()
    {
        string[] s = Application.dataPath.Split('/');
        return s[s.Length - 2];
    }

    static string[] GetScenePaths()
    {
        string[] scenes = new string[EditorBuildSettings.scenes.Length];

        for (int i = 0; i < scenes.Length; i++)
        {
            scenes[i] = EditorBuildSettings.scenes[i].path;
        }

        return scenes;
    }
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/53.jpg"><img src="/assets/img/posts/unity_mmogame/53.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/54.jpg"><img src="/assets/img/posts/unity_mmogame/54.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Enter Game

* Server\Session\ClientSession.cs
{% highlight C# %}
public class ClientSession : PacketSession
{
    public Player MyPlayer { get; set; }
    public int SessionId { get; set; }

    public void Send(IMessage packet)
    {
        string msgName = packet.Descriptor.Name.Replace("_", string.Empty);
        MsgId msgId = (MsgId)Enum.Parse(typeof(MsgId), msgName);

        ushort size = (ushort)packet.CalculateSize();
        byte[] sendBuffer = new byte[size + 4];
        Array.Copy(BitConverter.GetBytes((ushort)(size + 4)), 0, sendBuffer, 0, sizeof(ushort));
        Array.Copy(BitConverter.GetBytes((ushort)msgId), 0, sendBuffer, 2, sizeof(ushort));
        Array.Copy(packet.ToByteArray(), 0, sendBuffer, 4, size);

        Send(new ArraySegment<byte>(sendBuffer));
    }

    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected : {endPoint}");

        // PROTO Test
        MyPlayer = PlayerManager.Instance.Add();
        {
            MyPlayer.Info.Name = $"Player_{MyPlayer.Info.PlayerId}";
            MyPlayer.Info.PosX = 0;
            MyPlayer.Info.PosY = 0;
            MyPlayer.Session = this;
        }

        RoomManager.Instance.Find(1).EnterGame(MyPlayer);
    }
    ...

    public override void OnDisconnected(EndPoint endPoint)
    {
        RoomManager.Instance.Find(1).LeaveGame(MyPlayer.Info.PlayerId);
        
        SessionManager.Instance.Remove(this);

        Console.WriteLine($"OnDisconnected : {endPoint}");
    }
    ...
}
{% endhighlight %}

* Protocol.proto
{% highlight C# %}
...
enum MsgId {
  S_ENTER_GAME = 0;
  S_LEAVE_GAME = 1;
  S_SPAWN = 2;
  S_DESPAWN = 3;
  C_MOVE = 4;
  S_MOVE = 5;
}

message S_EnterGame {
  PlayerInfo player  = 1;
}

message S_LeaveGame{
}

message S_Spawn{
  repeated PlayerInfo players = 1;
}

message S_Despawn{
  repeated int32 playerIds = 1;
}

message C_Move{
  int32 posX = 1;
  int32 posY = 2;
}

message S_Move{
  int32 playerId = 1;
  int32 posX = 2;
  int32 posY = 3;
}

message PlayerInfo{
  int32 playerId = 1;
  string name = 2;
  int32 posX = 3;
  int32 posY = 4;
}
{% endhighlight %}

  - execute `GenProto.bat`

* Server\Game\GameRoom.cs
{% highlight C# %}
public class GameRoom
{
    object _lock = new object();
    public int RoomId { get; set; }

    List<Player> _players = new List<Player>();

    public void EnterGame(Player newPlayer)
    {
        if (newPlayer == null)
            return;

        lock (_lock)
        {
            _players.Add(newPlayer);
            newPlayer.Room = this;

            // send information to me
            {
                S_EnterGame enterPacket = new S_EnterGame();
                enterPacket.Player = newPlayer.Info;
                newPlayer.Session.Send(enterPacket);

                S_Spawn spawnPacket = new S_Spawn();
                foreach(Player p in _players)
                {
                    if (newPlayer != p)
                        spawnPacket.Players.Add(p.Info);
                }
                newPlayer.Session.Send(spawnPacket);
            }

            // send information to others
            {
                S_Spawn spawnPacket = new S_Spawn();
                spawnPacket.Players.Add(newPlayer.Info);
                foreach(Player p in _players)
                {
                    if (newPlayer != p)
                        p.Session.Send(spawnPacket);
                }
            }
        }
    }

    public void LeaveGame(int playerId)
    {
        lock (_lock)
        {
            Player player = _players.Find(p => p.Info.PlayerId == playerId);
            if (player == null)
                return;

            _players.Remove(player);
            player.Room = null;

            // send information to me
            {
                S_LeaveGame leavePacket = new S_LeaveGame();
                player.Session.Send(leavePacket);
            }

            // send information to others
            {
                S_Despawn despawnPacket = new S_Despawn();
                despawnPacket.PlayerIds.Add(player.Info.PlayerId);
                foreach(Player p in _players)
                {
                    if(player != p)
                        p.Session.Send(despawnPacket);
                }
            }
        }
    }
}
{% endhighlight %}

* Server\Game\RoomManager.cs
{% highlight C# %}
public class RoomManager
{
    public static RoomManager Instance { get; } = new RoomManager();

    object _lock = new object();
    Dictionary<int, GameRoom> _rooms = new Dictionary<int, GameRoom>();
    int _roomId = 1;

    public GameRoom Add()
    {
        GameRoom gameRoom = new GameRoom();

        lock (_lock)
        {
            gameRoom.RoomId = _roomId;
            _rooms.Add(_roomId, gameRoom);
            _roomId++;
        }

        return gameRoom;
    }

    public bool Remove(int roomId)
    {
        lock (_lock)
        {
            return _rooms.Remove(roomId);
        }
    }

    public GameRoom Find(int roomId)
    {
        lock (_lock)
        {
            GameRoom room = null;
            if (_rooms.TryGetValue(roomId, out room))
                return room;

            return null;
        }
    }
}
{% endhighlight %}

* Server\Game\Player.cs
{% highlight C# %}
public class Player
{
    public PlayerInfo Info { get; set; } = new PlayerInfo();
    public GameRoom Room { get; set; }
    public ClientSession Session { get; set; }
}
{% endhighlight %}

* Server\Game\PlayerManager.cs
{% highlight C# %}
public class PlayerManager
{
    public static PlayerManager Instance { get; } = new PlayerManager();

    object _lock = new object();
    Dictionary<int, Player> _players = new Dictionary<int, Player>();
    int _playerId = 1; // TODO

    public Player Add()
    {
        Player player = new Player();

        lock (_lock)
        {
            player.Info.PlayerId = _playerId;
            _players.Add(_playerId, player);
            _playerId++;
        }

        return player;
    }

    public bool Remove(int playerId)
    {
        lock (_lock)
        {
            return _players.Remove(playerId);
        }
    }

    public Player Find(int playerId)
    {
        lock (_lock)
        {
            Player player = null;
            if (_players.TryGetValue(playerId, out player))
                return player;

            return null;
        }
    }
}
{% endhighlight %}

* Server\Program.cs
{% highlight C# %}
class Program
{
    ...

    static void Main(string[] args)
    {
        RoomManager.Instance.Add();
       ...
    }
}
{% endhighlight %}

* Client\Assets\Scripts\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_EnterGameHandler(PacketSession session, IMessage packet)
	{
		S_EnterGame enterGamePacket = packet as S_EnterGame;
		ServerSession serverSession = session as ServerSession;

		Debug.Log("S_EnterGameHandler");
		Debug.Log(enterGamePacket.Player);
	}

	public static void S_LeaveGameHandler(PacketSession session, IMessage packet)
	{
		S_LeaveGame leaveGamePacket = packet as S_LeaveGame;
		ServerSession serverSession = session as ServerSession;

		Debug.Log("S_LeaveGameHandler");
;	}

	public static void S_SpawnHandler(PacketSession session, IMessage packet)
	{
		S_Spawn spawnPacket = packet as S_Spawn;
		ServerSession serverSession = session as ServerSession;

		Debug.Log("S_SpawnHandler");
		Debug.Log(spawnPacket.Players);
	}

	public static void S_DespawnHandler(PacketSession session, IMessage packet)
	{
		S_Despawn despawnePacket = packet as S_Despawn;
		ServerSession serverSession = session as ServerSession;

		Debug.Log("S_DespawnHandler");
	}
	public static void S_MoveHandler(PacketSession session, IMessage packet)
	{
		S_Move movePacket = packet as S_Move;
		ServerSession serverSession = session as ServerSession;

		Debug.Log("S_MoveHandler");
	}
}
{% endhighlight %}

### Test

<figure class="third">
  <a href="/assets/img/posts/unity_mmogame/57.jpg"><img src="/assets/img/posts/unity_mmogame/57.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/58.jpg"><img src="/assets/img/posts/unity_mmogame/58.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/59.jpg"><img src="/assets/img/posts/unity_mmogame/59.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

## Separate MyPlayer

* Scripts\Controllers\MyPlayerController.cs
  - to get keyboard input from only MyPlayer
  - copy and paste `Player` Prefab and change name to  `MyPlayer`
  - Add `MyPlayerController` Componenet on `MyPlayer` Prefab

<figure class="half">
  <a href="/assets/img/posts/unity_mmogame/61.jpg"><img src="/assets/img/posts/unity_mmogame/61.jpg"></a>
  <a href="/assets/img/posts/unity_mmogame/60.jpg"><img src="/assets/img/posts/unity_mmogame/60.jpg"></a>
	<figcaption>Unity MMO Game</figcaption>
</figure>

{% highlight C# %}
public class MyPlayerController : PlayerController
{
	protected override void Init()
	{
		base.Init();
	}

	protected override void UpdateController()
	{
		switch (State)
		{
			case CreatureState.Idle:
				GetDirInput();
				break;
			case CreatureState.Moving:
				GetDirInput();
				break;
		}

		base.UpdateController();
	}

	void LateUpdate()
	{
		Camera.main.transform.position = new Vector3(transform.position.x, transform.position.y, -10);
	}

	void GetDirInput()
	{
		if (Input.GetKey(KeyCode.W))
		{
			Dir = MoveDir.Up;
		}
		else if (Input.GetKey(KeyCode.S))
		{
			Dir = MoveDir.Down;
		}
		else if (Input.GetKey(KeyCode.A))
		{
			Dir = MoveDir.Left;
		}
		else if (Input.GetKey(KeyCode.D))
		{
			Dir = MoveDir.Right;
		}
		else
		{
			Dir = MoveDir.None;
		}
	}

	protected override void UpdateIdle()
	{
		if (Dir != MoveDir.None)
		{
			State = CreatureState.Moving;
			return;
		}

		if (Input.GetKey(KeyCode.Space))
		{
			State = CreatureState.Skill;
			//_coSkill = StartCoroutine("CoStartPunch");
			_coSkill = StartCoroutine("CoStartShootArrow");
		}
	}
}
{% endhighlight %}

* Scripts\Controllers\PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
	protected Coroutine _coSkill;
	bool _rangedSkill = false;

	protected override void Init()
	{
		base.Init();
	}

	protected override void UpdateAnimation()
	{
		if (_state == CreatureState.Idle)
		{
			switch (_lastDir)
			{
				case MoveDir.Up:
					_animator.Play("IDLE_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("IDLE_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("IDLE_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("IDLE_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else if (_state == CreatureState.Moving)
		{
			switch (_dir)
			{
				case MoveDir.Up:
					_animator.Play("WALK_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("WALK_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("WALK_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("WALK_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else if (_state == CreatureState.Skill)
		{
			switch (_lastDir)
			{
				case MoveDir.Up:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_BACK" : "ATTACK_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_FRONT" : "ATTACK_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_RIGHT" : "ATTACK_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_RIGHT" : "ATTACK_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else
		{

		}
	}

	protected override void UpdateController()
	{
		base.UpdateController();
	}

	protected override void UpdateIdle()
	{
		if (Dir != MoveDir.None)
		{
			State = CreatureState.Moving;
			return;
		}
	}

	IEnumerator CoStartPunch()
	{
		GameObject go = Managers.Object.Find(GetFrontCellPos());
		if (go != null)
		{
			CreatureController cc = go.GetComponent<CreatureController>();
			if (cc != null)
				cc.OnDamaged();
		}

		_rangedSkill = false;
		yield return new WaitForSeconds(0.5f);
		State = CreatureState.Idle;
		_coSkill = null;
	}

	IEnumerator CoStartShootArrow()
	{
		GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
		ArrowController ac = go.GetComponent<ArrowController>();
		ac.Dir = _lastDir;
		ac.CellPos = CellPos;

		_rangedSkill = true;
		yield return new WaitForSeconds(0.3f);
		State = CreatureState.Idle;
		_coSkill = null;
	}

	public override void OnDamaged()
	{
		Debug.Log("Player HIT !");
	}
}
{% endhighlight %}

* Scripts\Managers\Contents\ObjectManager.cs
{% highlight C# %}
public class ObjectManager
{
	public MyPlayerController MyPlayer { get; set; }
	Dictionary<int, GameObject> _objects = new Dictionary<int, GameObject>();
	//List<GameObject> _objects = new List<GameObject>();

	public void Add(PlayerInfo info, bool myPlayer = false)
    {
        if (myPlayer)
        {
			GameObject go = Managers.Resource.Instantiate("Creature/MyPlayer");
			go.name = info.Name;
			_objects.Add(info.PlayerId, go);

			MyPlayer = go.GetComponent<MyPlayerController>();
			MyPlayer.Id = info.PlayerId;
			MyPlayer.CellPos = new Vector3Int(info.PosX, info.PosY, 0);
        }
        else
        {
			GameObject go = Managers.Resource.Instantiate("Creature/Player");
			go.name = info.Name;
			_objects.Add(info.PlayerId, go);

			PlayerController pc = go.GetComponent<PlayerController>();
			pc.Id = info.PlayerId;
			pc.CellPos = new Vector3Int(info.PosX, info.PosY, 0);
		}
	}

	public void Add(int id, GameObject go)
	{
		_objects.Add(id,go);
	}

	public void Remove(int id)
	{
		_objects.Remove(id);
	}

	public void RemoveMyPlayer()
    {
		if (MyPlayer == null)
			return;

		Remove(MyPlayer.Id);
		MyPlayer = null;
    }

	public GameObject Find(Vector3Int cellPos)
	{
		foreach (GameObject obj in _objects.Values)
		{
			CreatureController cc = obj.GetComponent<CreatureController>();
			if (cc == null)
				continue;

			if (cc.CellPos == cellPos)
				return obj;
		}

		return null;
	}

	public GameObject Find(Func<GameObject, bool> condition)
	{
		foreach (GameObject obj in _objects.Values)
		{
			if (condition.Invoke(obj))
				return obj;
		}

		return null;
	}
    ...
}
{% endhighlight %}

* Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_EnterGameHandler(PacketSession session, IMessage packet)
	{
		S_EnterGame enterGamePacket = packet as S_EnterGame;
		Managers.Object.Add(enterGamePacket.Player, myPlayer: true);
	}

	public static void S_LeaveGameHandler(PacketSession session, IMessage packet)
	{
		S_LeaveGame leaveGamePacket = packet as S_LeaveGame;
		Managers.Object.RemoveMyPlayer();
;	}

	public static void S_SpawnHandler(PacketSession session, IMessage packet)
	{
		S_Spawn spawnPacket = packet as S_Spawn;

		foreach(PlayerInfo player in spawnPacket.Players)
        {
			Managers.Object.Add(player, myPlayer: false);
        }
	}

	public static void S_DespawnHandler(PacketSession session, IMessage packet)
	{
		S_Despawn despawnePacket = packet as S_Despawn;

		foreach (int id in despawnePacket.PlayerIds)
		{
			Managers.Object.Remove(id);
		}
		Debug.Log("S_DespawnHandler");
	}
	public static void S_MoveHandler(PacketSession session, IMessage packet)
	{
		S_Move movePacket = packet as S_Move;
		ServerSession serverSession = session as ServerSession;

		Debug.Log("S_MoveHandler");
	}
}
{% endhighlight %}

* Server\PacketGenerator\PacketFormat.cs
{% highlight C# %}
class PacketFormat
{
    // {0} Packet Assign
    public static string managerFormat =
@"using Google.Protobuf;
...

class PacketManager
{
	...
	Dictionary<ushort, Action<PacketSession, ArraySegment<byte>, ushort>> _onRecv = new Dictionary<ushort, Action<PacketSession, ArraySegment<byte>, ushort>>();
	Dictionary<ushort, Action<PacketSession, IMessage>> _handler = new Dictionary<ushort, Action<PacketSession, IMessage>>();
		
	public Action<PacketSession, IMessage, ushort> CustomHandler { get; set; }
	...

	void MakePacket<T>(PacketSession session, ArraySegment<byte> buffer, ushort id) where T : IMessage, new()
	{
		T pkt = new T();
		pkt.MergeFrom(buffer.Array, buffer.Offset + 4, buffer.Count - 4);

		if(CustomHandler != null)
		{
			CustomHandler.Invoke(session, pkt, id);
		}
		else
		{
			Action<PacketSession, IMessage> action = null;
			if (_handler.TryGetValue(id, out action))
				action.Invoke(session, pkt);
		}
	}
    ...
}";
...
}
{% endhighlight %}

* Scripts\Packet\ServerSession.cs
{% highlight C# %}
public class ServerSession : PacketSession
{
	public override void OnConnected(EndPoint endPoint)
	{
		Debug.Log($"OnConnected : {endPoint}");

		PacketManager.Instance.CustomHandler = (s, m, i) => { PacketQueue.Instance.Push(i, m); };
	}
    ...
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_minirpg/MMO-Game-MyPlayer.mp4" frameborder="0"> </iframe>

## Move: Server
  - Policy: First Move, then Send

* Protocol.proto
{% highlight proto %}
...
enum CreatureState{
  IDLE = 0;
  MOVING = 1;
  SKILL = 2;
  DEAD = 3;
}

enum MoveDir{
  NONE = 0;
  UP = 1;
  DOWN = 2;
  LEFT = 3;
  RIGHT = 4;
}
...

message S_Spawn{
  repeated PlayerInfo players = 1;
}

message S_Despawn{
  repeated int32 playerIds = 1;
}

message C_Move{
  PositionInfo posInfo = 1;
}

message S_Move{
  int32 playerId = 1;
  PositionInfo posInfo = 2;
}

message PlayerInfo{
  int32 playerId = 1;
  string name = 2;
  PositionInfo posInfo = 3;
}

message PositionInfo{
  CreatureState  state= 1;
  MoveDir moveDir = 2;
  int32 posX = 3;
  int32 posY = 4;
}
{% endhighlight %}

* Session\ClientSession.cs
{% highlight C# %}
...
public class ClientSession : PacketSession
{
    ...
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected : {endPoint}");

        // PROTO Test
        MyPlayer = PlayerManager.Instance.Add();
        {
            MyPlayer.Info.Name = $"Player_{MyPlayer.Info.PlayerId}";
            MyPlayer.Info.PosInfo.State = CreatureState.Idle;
            MyPlayer.Info.PosInfo.MoveDir = MoveDir.None;
            MyPlayer.Info.PosInfo.PosX = 0;
            MyPlayer.Info.PosInfo.PosY = 0;
            MyPlayer.Session = this;
        }

        RoomManager.Instance.Find(1).EnterGame(MyPlayer);
    }
    ...
}
{% endhighlight %}

* Game\Player.cs
{% highlight C# %}
public class Player
{
    public PlayerInfo Info { get; set; } = new PlayerInfo() { PosInfo = new PositionInfo() };
    public GameRoom Room { get; set; }
    public ClientSession Session { get; set; }
}
{% endhighlight %}


* Packet\PacketHandler.cs
{% highlight C# %}
public static void C_MoveHandler(PacketSession session, IMessage packet)
{
    C_Move movePacket = packet as C_Move;
    ClientSession clientSession = session as ClientSession;

    Console.WriteLine($"C_Move({movePacket.PosInfo.PosX}, {movePacket.PosInfo.PosY})");

    if (clientSession.MyPlayer == null)
        return;
    if (clientSession.MyPlayer.Room == null)
        return;

    // TODO: Authorization
    
    // First Move Position from Server
    PlayerInfo info = clientSession.MyPlayer.Info;
    info.PosInfo = movePacket.PosInfo;

    // Send Position Information to Others
    S_Move resMovePacket = new S_Move();
    resMovePacket.PlayerId = clientSession.MyPlayer.Info.PlayerId;
    resMovePacket.PosInfo = movePacket.PosInfo;

    clientSession.MyPlayer.Room.Broadcast(resMovePacket);
}
{% endhighlight %}

* Game\GameRoom.cs
{% highlight C# %}
public class GameRoom
{
    ...
    public void Broadcast(IMessage packet)
    {
        lock (_lock)
        {
            foreach(Player p in _players)
            {
                p.Session.Send(packet);
            }
        }
    }
}
{% endhighlight %}

## Move: Client

* Controllers\CreatureController.cs
{% highlight C# %}
public class CreatureController : MonoBehaviour
{
	public int Id { get; set; }

	[SerializeField]
	public float _speed = 5.0f;

	PositionInfo _positionInfo = new PositionInfo();
	public PositionInfo PosInfo
    {
        get { return _positionInfo; }
        set 
		{ 
			if (_positionInfo.Equals(value))
				return;

			_positionInfo = value;
			UpdateAnimation();
		}
    }

	public Vector3Int CellPos 
	{
		get 
		{
			return new Vector3Int(PosInfo.PosX, PosInfo.PosY, 0);
		}

        set
        {
			PosInfo.PosX = value.x;
			PosInfo.PosY = value.y;
        }
	}

	protected Animator _animator;
	protected SpriteRenderer _sprite;

	public virtual CreatureState State
	{
		get { return PosInfo.State; }
		set
		{
			if (PosInfo.State == value)
				return;

			PosInfo.State = value;
			UpdateAnimation();
		}
	}

	protected MoveDir _lastDir = MoveDir.Down;

	public MoveDir Dir
	{
		get { return PosInfo.MoveDir; }
		set
		{
			if (PosInfo.MoveDir == value)
				return;

			PosInfo.MoveDir = value;
			if (value != MoveDir.None)
				_lastDir = value;

			UpdateAnimation();
		}
	}
    ...

	protected virtual void UpdateAnimation()
	{
		if (State == CreatureState.Idle)
		{
			switch (_lastDir)
			{
				case MoveDir.Up:
					_animator.Play("IDLE_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("IDLE_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("IDLE_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("IDLE_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else if (State == CreatureState.Moving)
		{
			switch (Dir)
			{
				case MoveDir.Up:
					_animator.Play("WALK_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("WALK_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("WALK_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("WALK_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else if (State == CreatureState.Skill)
		{
			switch (_lastDir)
			{
				case MoveDir.Up:
					_animator.Play("ATTACK_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("ATTACK_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("ATTACK_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("ATTACK_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else
		{

		}
	}
	...

	protected virtual void UpdateController()
	{
		switch (State)
		{
			case CreatureState.Idle:
				UpdateIdle();
				break;
			case CreatureState.Moving:
				UpdateMoving();
				break;
			case CreatureState.Skill:
				UpdateSkill();
				break;
			case CreatureState.Dead:
				UpdateDead();
				break;
		}
	}

	protected virtual void UpdateIdle()	{ }

	protected virtual void UpdateMoving()
	{
		Vector3 destPos = Managers.Map.CurrentGrid.CellToWorld(CellPos) + new Vector3(0.5f, 0.5f);
		Vector3 moveDir = destPos - transform.position;

		float dist = moveDir.magnitude;
		if (dist < _speed * Time.deltaTime)
		{
			transform.position = destPos;
			MoveToNextPos();
		}
		else
		{
			transform.position += moveDir.normalized * _speed * Time.deltaTime;
			State = CreatureState.Moving;
		}
	}

	protected virtual void MoveToNextPos()
	{
		if (Dir == MoveDir.None)
		{
			State = CreatureState.Idle;
			return;
		}

		Vector3Int destPos = CellPos;

		switch (Dir)
		{
			case MoveDir.Up:
				destPos += Vector3Int.up;
				break;
			case MoveDir.Down:
				destPos += Vector3Int.down;
				break;
			case MoveDir.Left:
				destPos += Vector3Int.left;
				break;
			case MoveDir.Right:
				destPos += Vector3Int.right;
				break;
		}
		...
	}
    ...
}
{% endhighlight %}


* Utils\Define.cs
{% highlight C# %}
public class Define
{
    // Remove CreatureState and MoveDir
    // Because it is in Protobuf
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


* Managers\Contents\ObjectManager.cs
{% highlight C# %}
public class ObjectManager
{
	...
	public void Add(PlayerInfo info, bool myPlayer = false)
    {
        if (myPlayer)
        {
			GameObject go = Managers.Resource.Instantiate("Creature/MyPlayer");
			go.name = info.Name;
			_objects.Add(info.PlayerId, go);

			MyPlayer = go.GetComponent<MyPlayerController>();
			MyPlayer.Id = info.PlayerId;
			MyPlayer.PosInfo = info.PosInfo;
        }
        else
        {
			GameObject go = Managers.Resource.Instantiate("Creature/Player");
			go.name = info.Name;
			_objects.Add(info.PlayerId, go);

			PlayerController pc = go.GetComponent<PlayerController>();
			pc.Id = info.PlayerId;
			pc.PosInfo = info.PosInfo;
		}
	}
    ...

    public GameObject FindById(int id)
    {
		GameObject go = null;
		_objects.TryGetValue(id, out go);
		return go;
    }
    ...
}
{% endhighlight %}


* Controllers\MonsterController.cs
{% highlight C# %}
public class MonsterController : CreatureController
{
	...
	public override CreatureState State
	{
		get { return PosInfo.State; }
		set
		{
			if (PosInfo.State == value)
				return;

			base.State = value;

			if (_coPatrol != null)
			{
				StopCoroutine(_coPatrol);
				_coPatrol = null;
			}

			if (_coSearch != null)
			{
				StopCoroutine(_coSearch);
				_coSearch = null;
			}
		}
	}

	protected override void Init()
	{
		base.Init();

		State = CreatureState.Idle;
		Dir = MoveDir.None;

		_speed = 3.0f;
		_rangedSkill = (Random.Range(0, 2) == 0 ? true : false);

		if (_rangedSkill)
			_skillRange = 10.0f;
		else
			_skillRange = 1.0f;
	}
	...

	protected override void MoveToNextPos()
	{
		Vector3Int destPos = _destCellPos;
		if (_target != null)
		{
			destPos = _target.GetComponent<CreatureController>().CellPos;

			Vector3Int dir = destPos - CellPos;
			if (dir.magnitude <= _skillRange && (dir.x == 0 || dir.y == 0))
			{
				Dir = GetDirFromVec(dir);
				State = CreatureState.Skill;

				if (_rangedSkill)
					_coSkill = StartCoroutine("CoStartShootArrow");
				else
					_coSkill = StartCoroutine("CoStartPunch");

				return;
			}
		}

		List<Vector3Int> path = Managers.Map.FindPath(CellPos, destPos, ignoreDestCollision: true);
		if (path.Count < 2 || (_target != null && path.Count > 20))
		{
			_target = null;
			State = CreatureState.Idle;
			return;
		}

		Vector3Int nextPos = path[1];
		Vector3Int moveCellDir = nextPos - CellPos;

		Dir = GetDirFromVec(moveCellDir);

		if (Managers.Map.CanGo(nextPos) && Managers.Object.Find(nextPos) == null)
		{
			CellPos = nextPos;
		}
		else
		{
			State = CreatureState.Idle;
		}
	}
    ...

	IEnumerator CoPatrol()
	{
		int waitSeconds = Random.Range(1, 4);
		yield return new WaitForSeconds(waitSeconds);

		for (int i = 0; i < 10; i++)
		{
			int xRange = Random.Range(-5, 6);
			int yRange = Random.Range(-5, 6);
			Vector3Int randPos = CellPos + new Vector3Int(xRange, yRange, 0);

			if (Managers.Map.CanGo(randPos) && Managers.Object.Find(randPos) == null)
			{
				_destCellPos = randPos;
				State = CreatureState.Moving;
				yield break;
			}
		}

		State = CreatureState.Idle;
	}
	...

	IEnumerator CoStartPunch()
	{
		GameObject go = Managers.Object.Find(GetFrontCellPos());
		if (go != null)
		{
			CreatureController cc = go.GetComponent<CreatureController>();
			if (cc != null)
				cc.OnDamaged();
		}

		yield return new WaitForSeconds(0.5f);
		State = CreatureState.Moving;
		_coSkill = null;
	}

	IEnumerator CoStartShootArrow()
	{
		GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
		ArrowController ac = go.GetComponent<ArrowController>();
		ac.Dir = _lastDir;
		ac.CellPos = CellPos;

		yield return new WaitForSeconds(0.3f);
		State = CreatureState.Moving;
		_coSkill = null;
	}
}
{% endhighlight %}


* Controllers\PlayerController.cs
{% highlight C# %}
public class PlayerController : CreatureController
{
	...
	protected override void UpdateAnimation()
	{
		if (State == CreatureState.Idle)
		{
			switch (_lastDir)
			{
				case MoveDir.Up:
					_animator.Play("IDLE_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("IDLE_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("IDLE_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("IDLE_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else if (State == CreatureState.Moving)
		{
			switch (Dir)
			{
				case MoveDir.Up:
					_animator.Play("WALK_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play("WALK_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play("WALK_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play("WALK_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else if (State == CreatureState.Skill)
		{
			switch (_lastDir)
			{
				case MoveDir.Up:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_BACK" : "ATTACK_BACK");
					_sprite.flipX = false;
					break;
				case MoveDir.Down:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_FRONT" : "ATTACK_FRONT");
					_sprite.flipX = false;
					break;
				case MoveDir.Left:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_RIGHT" : "ATTACK_RIGHT");
					_sprite.flipX = true;
					break;
				case MoveDir.Right:
					_animator.Play(_rangedSkill ? "ATTACK_WEAPON_RIGHT" : "ATTACK_RIGHT");
					_sprite.flipX = false;
					break;
			}
		}
		else
		{

		}
	}
    ...

	protected override void UpdateIdle()
	{
		if (Dir != MoveDir.None)
		{
			State = CreatureState.Moving;
			return;
		}
	}

	IEnumerator CoStartPunch()
	{
		GameObject go = Managers.Object.Find(GetFrontCellPos());
		if (go != null)
		{
			CreatureController cc = go.GetComponent<CreatureController>();
			if (cc != null)
				cc.OnDamaged();
		}

		_rangedSkill = false;
		yield return new WaitForSeconds(0.5f);
		State = CreatureState.Idle;
		_coSkill = null;
	}

	IEnumerator CoStartShootArrow()
	{
		GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
		ArrowController ac = go.GetComponent<ArrowController>();
		ac.Dir = _lastDir;
		ac.CellPos = CellPos;

		_rangedSkill = true;
		yield return new WaitForSeconds(0.3f);
		State = CreatureState.Idle;
		_coSkill = null;
	}
    ...
}
{% endhighlight %}

* Controllers\ArrowController.cs
{% highlight C# %}
public class ArrowController : CreatureController
{
    ...
	protected override void MoveToNextPos()
	{
		Vector3Int destPos = CellPos;

		switch (Dir)
		{
			case MoveDir.Up:
				destPos += Vector3Int.up;
				break;
			case MoveDir.Down:
				destPos += Vector3Int.down;
				break;
			case MoveDir.Left:
				destPos += Vector3Int.left;
				break;
			case MoveDir.Right:
				destPos += Vector3Int.right;
				break;
		}
		...
	}
}
{% endhighlight %}

* Controllers\MyPlayerController.cs
{% highlight C# %}
public class MyPlayerController : PlayerController
{
	...
    // When Position is changed, Send Position Information to Server
	protected override void MoveToNextPos()
	{
		CreatureState prevState = State;
		Vector3Int prevCellPos = CellPos;

		base.MoveToNextPos();

		if(prevState != State || CellPos != prevCellPos)
        {
			C_Move movePacket = new C_Move();
			movePacket.PosInfo = PosInfo;
			Managers.Network.Send(movePacket);
        }
	}
}
{% endhighlight %}

* Packet\ServerSession.cs
{% highlight C# %}
public class ServerSession : PacketSession
{
    // Send to Server
	public void Send(IMessage packet)
	{
		string msgName = packet.Descriptor.Name.Replace("_", string.Empty);
		MsgId msgId = (MsgId)Enum.Parse(typeof(MsgId), msgName);

		ushort size = (ushort)packet.CalculateSize();
		byte[] sendBuffer = new byte[size + 4];
		Array.Copy(BitConverter.GetBytes((ushort)(size + 4)), 0, sendBuffer, 0, sizeof(ushort));
		Array.Copy(BitConverter.GetBytes((ushort)msgId), 0, sendBuffer, 2, sizeof(ushort));
		Array.Copy(packet.ToByteArray(), 0, sendBuffer, 4, size);

		Send(new ArraySegment<byte>(sendBuffer));
	}
	...
}
{% endhighlight %}

* Managers\Contents\NetworkManager.cs
{% highlight C# %}
public class NetworkManager
{
	ServerSession _session = new ServerSession();

	public void Send(IMessage packet)
	{
		_session.Send(packet);
	}
    ...
}
{% endhighlight %}

* Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    ...
	public static void S_MoveHandler(PacketSession session, IMessage packet)
	{
		S_Move movePacket = packet as S_Move;
		ServerSession serverSession = session as ServerSession;
		
		GameObject go = Managers.Object.FindById(movePacket.PlayerId);
		if (go == null)
			return;

		CreatureController cc = go.GetComponent<CreatureController>();
		if (cc == null)
			return;

		cc.PosInfo = movePacket.PosInfo;
	}
}

{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_minirpg/MMO-Game-Move.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/Unity){: .btn}
