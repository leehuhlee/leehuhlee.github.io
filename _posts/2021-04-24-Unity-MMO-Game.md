---
layout: post
title: "[Unity] MMO Game"
date: 2021-04-24
excerpt: "MMO Game 2D"
tags: [C#, Unity, Game, MMO, 2D]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part7/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part7: MMO 컨텐츠 구현 (Unity + C# 서버 연동 기초)</a></center>

# Set for 2D

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

# Tilemap

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


* Scripts\Managers\ObjectManager.cs
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

## Attack

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

* CreatureController.cs
{% highlight C# %}
{% endhighlight %}

* CreatureController.cs
{% highlight C# %}
{% endhighlight %}

* CreatureController.cs
{% highlight C# %}
{% endhighlight %}
### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmogame/MMO-Game-Attack.mp4" frameborder="0"> </iframe>


[Download](https://github.com/leehuhlee/Unity){: .btn}
