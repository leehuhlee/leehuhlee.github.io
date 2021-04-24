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

[Download](https://github.com/leehuhlee/Unity){: .btn}
