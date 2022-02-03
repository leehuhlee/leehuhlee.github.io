---
layout: post
title: "[JS] Christmas Island"
date: 2022-02-03
excerpt: "JS Games"
tags: [HTML, CSS, JavaScript, Web]
comments: false
---

# Demo

<iframe width="560" height="315" src="/assets/video/posts/js_christmasisland/JS-Christmas-Island.mp4" frameborder="0"> </iframe>

# Code

## Forest

* Razor
{% highlight razor %}
@page "/forest"

@inject NavigationManager NavigationManager
@inject IJSRuntime JSRuntime

<div id="wrapper">
    <div id="content">
        <div class="card text-center">
            <h1>This is our <span style="color:green;"><b>Forest</b></span>.</h1>

            <div class="header">
                Score: <strong class="score">0</strong>
            </div>

            <div class="forest">
                <div Id="crab"></div>
                <div id="ant"></div>
            </div>
        </div>
    </div>
</div>

@code {
    protected override void OnAfterRender(bool firstRender) {
        if (firstRender)
            JSRuntime.InvokeVoidAsync("forest");
    }
}
{% endhighlight %}

* Javascript
{% highlight js %}
function forest() {
    const scoreDisplay = document.querySelector(".score");

    let score = 0;
    let startGame = false;

    function reset() {
        score = 0;
        startGame = true;
        var ant = document.getElementById("ant");
        if (ant.classList != "block") {
            ant.classList.add("block");
        }
        scoreDisplay.innerHTML = score;
    }

    function jump() {
        var crab = document.getElementById("crab");
        var ant = document.getElementById("ant");

        if (crab.classList != "jump") {
            crab.classList.add("jump");
            setTimeout(function () {
                crab.classList.remove("jump");
            }, 300);
        }
        
    }

    function loop() {
        let isAlive = setInterval(function () {
            let crabTop = parseInt(window.getComputedStyle(crab).getPropertyValue("top"));
            let antLeft = parseInt(window.getComputedStyle(ant).getPropertyValue("left"));

            if (antLeft < 50 && antLeft > 0 && crabTop >= 140) {
                location = "/ending-ant";
                alert("Game Over");
            }

        }, 10);
    }

    document.addEventListener("keydown", function (event) {
        if (!startGame) {
            reset();
            loop();
        }
        else {
            scoreDisplay.innerHTML = score + 1;
            jump();
            score++;
            if (score >= 10) {
                location = "/road/intro";
                alert("Clear");
            }
        }
    })
}
{% endhighlight %}

* CSS
{% highlight CSS%}
.forest {
    width: 600px;
    height: 200px;
    border: 1px solid black;
    margin: auto;
}

    .forest > #crab {
        width: 50px;
        height: 50px;
        background-image: url(../image/pixel_crab.png);
        background-size: 50px 50px;
        position: relative;
        top: 150px;
    }

    .forest > #ant {
        width: 20px;
        height: 40px;
        position: relative;
        top: 110px;
        left: 580px;
        background-image: url(../image/pixel_ant.png);
        background-size: 20px 40px;
    }

.jump {
    animation: jump 0.3s linear;
}

.block {
    animation: block 1s infinite linear;
}

@keyframes jump {
    0% {
        top: 150px;
    }

    30% {
        top: 130px;
    }

    50% {
        top: 80px;
    }

    80% {
        top: 130px;
    }

    100% {
        top: 150px;
    }
}

@keyframes block {
    0% {
        left: 580px;
    }

    100% {
        left: -20px;
    }
}
{% endhighlight %}

## Road

* Razor

{% highlight razor%}
@page "/road"

@inject NavigationManager NavigationManager
@inject IJSRuntime JSRuntime

<div id="wrapper">
    <div id="content">
        <div class="card text-center">
            <h1>This is the road <span style="color:red;"><b>humans made</b></span>.</h1>

            <div class="road">
                <div class="header">
                    Score: <strong class="score">0</strong>
                </div>
                <div class="grid">
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                    <div class="cell"></div>
                </div>
            </div>
        </div>
    </div>
</div>

@code {
    protected override void OnAfterRender(bool firstRender) {
        if(firstRender)
            JSRuntime.InvokeVoidAsync("road");
    }
}
{% endhighlight %}

* Javascript
{% highlgiht js %}
function road() {
    const cells = Array.from(document.querySelectorAll(".cell"));
    const carCells = cells.slice(0, 30);
    const crabCells = cells.slice(30);
    const scoreDisplay = document.querySelector(".score");

    let dropCount, speed, score;

    reset();

    document.addEventListener("keydown", e => {
        if (!dropCount) {
            startGame();
        }

        const crab = document.querySelector(".crab");

        if (e.key == "ArrowRight" && crabCells.includes(crab.parentElement.nextElementSibling)) {
            crab.parentElement.nextElementSibling.appendChild(crab);
        }

        if (e.key == "ArrowLeft" && crabCells.includes(crab.parentElement.previousElementSibling)) {
            crab.parentElement.previousElementSibling.appendChild(crab);
        }
    })

    function reset() {
        dropCount = 0;
        speed = 1000;
        score = 0;
        scoreDisplay.innerHTML = "0";

        cells.forEach(cell => cell.innerHTML = "");
        crabCells[1].innerHTML = '<div class="crab"></div>';
    }

    function startGame() {
        reset();
        loop();
    }

    function loop() {
        let stopGame = false;

        for (let i = carCells.length - 1; i >= 0; i--) {
            const cell = carCells[i];
            const nextCell = cells[i + 3];
            const car = cell.children[0];

            if (!car) {
                continue;
            }

            nextCell.appendChild(car);

            if (crabCells.includes(nextCell)) {
                if (nextCell.querySelector(".crab")) {
                    stopGame = true;
                }
                else {
                    score++;
                    speed = Math.max(100, speed - 25);
                    scoreDisplay.innerHTML = score;
                    car.remove();
                    if (score >= 10) {
                        location = "/sun/intro";
                        alert("Clear");
                    }
                }
            }
        }

        if (dropCount % 2 == 0) {
            const position = Math.floor(Math.random() * 3);

            carCells[position].innerHTML = '<div class="car"></div>';
        }

        if (stopGame) {
            location = "/ending-roadkill";
            alert("Game Over");
        }
        else {
            dropCount++;
            setTimeout(loop, speed);
        }
    }
}
{% endhighlight %}

* CSS
{% highlight css %}
.road > .header {
    padding-bottom: 5px;
}

.road > .grid {
    display: inline-grid;
    grid-template-columns: repeat(3, 30px);
    padding: 20px;
    grid-gap: 5px;
    border: 1px solid #ccc;
    background: #222;
}

    .road > .grid > .cell {
        height: 30px;
    }

        .road > .grid > .cell > .car,
        .road > .grid > .cell > .crab {
            width: 100%;
            height: 100%;
        }

        .road > .grid > .cell > .car {
            background-image: url(../image/pixel_car.png);
            background-size: 30px 30px;
        }

        .road > .grid > .cell > .crab {
            background-image: url(../image/pixel_crab.png);
            background-size: 30px 30px;
        }
{% endhighlight %}

## Sun

* Razor

{% highlight razor %}
@page "/sun"

@inject NavigationManager NavigationManager
@inject IJSRuntime JSRuntime

<div class="sun">
    <p class="text">Loading...</p>
</div>

<style>
    * {
        margin: 0 !important;
        padding: 0 !important;
    }
</style>

@code {
    protected override void OnAfterRender(bool firstRender) {
        if (firstRender)
            JSRuntime.InvokeVoidAsync("sun");
    }
}
{% endhighlight %}

* Javascript
{% highlight js %}
function sun() {
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);

    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    renderer.setClearColor(0xb7c3f3, 1);

    const light = new THREE.AmbientLight(0xffffff);
    scene.add(light);

    const end_position = 3;
    const start_position = -end_position;
    const crab_start_position = start_position / 2;
    const crab_end_position = -crab_start_position;

    const text = document.querySelector(".text");

    const TIME_LIMIT = 10;
    let gameStat = false;
    let isNight = true;

    function createCube(size, positionX, rotY = 0, color = 0xfbc851) {
        const geometry = new THREE.BoxGeometry(size.w, size.h, size.d);
        const material = new THREE.MeshBasicMaterial({ color: color });
        const cube = new THREE.Mesh(geometry, material);
        cube.position.x = positionX;
        cube.rotation.y = rotY;
        scene.add(cube);

        return cube;
    }

    camera.position.z = 5;

    const loader = new THREE.GLTFLoader();

    function delay(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    class Sphere {
        constructor() {
            loader.load("../models/sphere/scene.gltf", (gltf) => {
                scene.add(gltf.scene);
                gltf.scene.scale.set(.003, .003, .003);
                gltf.scene.position.set(0, 0, 0);

                this.sphere = gltf.scene;
            });
        }

        lookDay() {
            gsap.to(this.sphere.rotation, { x: -1.5, duration: .45 });
            setTimeout(() => isNight = false, 450);
        }

        lookNight() {
            gsap.to(this.sphere.rotation, { x: 1.5, duration: .45 });
            setTimeout(() => isNight = true, 150);
        }

        async start() {
            this.lookNight();
            await delay((Math.random() * 1000) + 1000);
            this.lookDay();
            await delay((Math.random() * 750) + 750);
            this.start();
        }
    }

    function createTrack() {
        createCube({ w: end_position * 2 + .2, h: 1.5, d: 1 }, 0, 0, 0xe5a716).position.z = -1;
        createCube({ w: .2, h: 1.5, d: 1 }, end_position, -.35);
        createCube({ w: .2, h: 1.5, d: 1 }, start_position, .15);
    }
    createTrack();

    class Crab {
        constructor() {
            const geometry = new THREE.PlaneGeometry(.3, .3);
            const loader = new THREE.TextureLoader();
            const material = new THREE.MeshBasicMaterial({
                map: loader.load("../image/pixel_crab.png"),
                transparent: true
            });
            const plane = new THREE.Mesh(geometry, material);
            plane.position.z = 3;
            plane.position.x = crab_start_position;
            scene.add(plane);
            this.crab = plane;

            this.crabInfo = {
                positionX: crab_start_position,
                velocity: 0
            };
        }

        run() {
            this.crabInfo.velocity = .01;
        }

        stop() {
            gsap.to(this.crabInfo, { velocity: 0, duration: .1 });
        }

        check() {
            if (this.crabInfo.velocity > 0 && !isNight) {
                location = "/ending-burning";
                alert("Game Over");
                return;
            }
            if (this.crabInfo.positionX > crab_end_position - .2) {
                location = "/rock/intro";
                alert("Clear");
                return;
            }
        }

        update() {
            this.check();
            this.crabInfo.positionX += this.crabInfo.velocity;
            this.crab.position.x = this.crabInfo.positionX;
        }
    }

    const crab = new Crab();
    const sphere = new Sphere();

    async function init() {
        await delay(500);
        text.innerText = "Starting in 3";
        await delay(500);
        text.innerText = "Starting in 2";
        await delay(500);
        text.innerText = "Starting in 1";
        await delay(500);
        text.innerText = "Go!!!";
        startGame();
    }

    function startGame() {
        gameStat = true;
        let progressBar = createCube({ w: 5, h: .1, d: 1 }, 0);
        progressBar.position.y = 3.35;
        gsap.to(progressBar.scale, { x: 0, duration: TIME_LIMIT, ease: "none" });
        sphere.start();
        setTimeout(() => {
            if (gameStat) {
                alert("Game Over");
                location = "/ending-burning";
            }
        }, TIME_LIMIT * 1000);
    }

    init();

    function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
        crab.update();
    }
    animate();

    window.addEventListener('resize', onWindowResize, false);

    function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();

        renderer.setSize(window.innerWidth, window.innerHeight);
    }

    document.addEventListener('keydown', function (event) {
        if (!gameStat) return;
        crab.run();
    })

    document.addEventListener('keyup', function (event) {
        crab.stop();
    })
}
{% endhighlight %}

* CSS
{% highlight css %}
.sun > .text {
    position: fixed;
    font-size: 50px;
    font-family: Arial, Helvetica, sans-serif;
    font-weight: bold;
    top: 10px;
    left: 50%;
    text-shadow: 0 2px 5px rgba(0,0,0,.3);
    transform: translateX(-50%);
}
{% endhighlight %}

## Rock

* Razor
{% highlight razor %}
@page "/rock"

@inject NavigationManager NavigationManager
@inject IJSRuntime JSRuntime

<div id="wrapper">
    <div id="content">
        <div class="card text-center">
            <div class="header">
                <font size="5" id="score">Score</font> / <font size="5" id="heart">Life</font>
            </div>

            <div class="rock">
                <div id="canvas"></div>
                <hr id="line" align="left">
                <img src="image/pixel_crab.png" style="width:100px;" />
                <br />
                <input id="text" placeholder="text" autofocus>
                <button id="auth" class="btn btn-dark">Enter</button>
            </div>
        </div>
    </div>
</div>

@code {
    protected override void OnAfterRender(bool firstRender) {
        if (firstRender)
            JSRuntime.InvokeVoidAsync("rock");
    }
}
{% endhighlight %}

* Javascript
{% highlight js %}
function rock() {
	var canvasWidth = 600;
	var canvasHeigth = 380; // 키워드 생성되는 세로범위
	var goal = 5;
	var keyword = ['hanna', 'maggie', 'peter', 'justin', 'ilikim', 'meike'];

	function heart_counter(heart) {
		var result = "<font color=red>";
		for (var x = 0; x < heart; ++x) {
			result += "♥";
		}
		result += "</font>";
		return result;
	}

	var keyword_cnt = 0;
	var score = 0;
	var heart = 5;

	document.getElementById('score').innerHTML = "Score : " + score
	document.getElementById('heart').innerHTML = "Life : " + heart_counter(heart);

	function gamewin() {
		clearInterval(setInterval1);
		clearInterval(setInterval2);
		location = "/beach/intro";
		alert("Clear");
	}

	function remove_node(pRemoveEle) {
		var vRemove = document.getElementById(pRemoveEle);
		var vParent = vRemove.parentNode;
		vParent.removeChild(vRemove);
		vRemove = null;
	}

	function gameover(code) {
		clearInterval(setInterval1);
		clearInterval(setInterval2);
		location = "/ending-angry";
		alert("Game Over");
	}

	function random_speed(maxSpeed) {
		return parseInt(Math.random() * maxSpeed) + 1;
	}

	function random_width() {
		return parseInt(Math.random() * canvasWidth) + 50;
	}

	function keyword_rain() {
		this.y = 0;
		this.speed = random_speed(2);

		this.node = document.createElement('h3');
		this.node.id = keyword[keyword_cnt];
		this.node.innerHTML = keyword[keyword_cnt++];

		if (keyword_cnt >= keyword.length) {
			clearInterval(setInterval1);
		}

		this.node.style.position = 'absolute';
		this.node.style.left = random_width() + "px";
		console.log(this.node.style.position.x);

		document.getElementById('canvas').appendChild(this.node);

		keyword_rain.prototype.move = function () {
			if (this.y > canvasHeigth) { 
				this.node.remove(); 
				this.y = this.speed = 0;
				keyword.splice(keyword.indexOf(this.node.id), 1);
				keyword_cnt -= 1;
				heart -= 1;
				document.getElementById('heart').innerHTML = "Life : " + heart_counter(heart);
				if (heart < 1) gameover(1);
				if (keyword.length == 0) gameover(2);
				return;
			}
			this.y += this.speed;
			this.node.style.top = this.y + 'px';
		}
	}

	function keydown(keyCode) {
		if (keyCode == "Enter") {
			var text = document.getElementById('text');

			if (keyword.indexOf(text.value) != -1) {
				remove_node(text.value);
				for (var i in game) {
					if (game[i]['node'].id == text.value) {
						game[i]['y'] = 0;
						game[i]['speed'] = 0;
					}
				}
				keyword.splice(keyword.indexOf(text.value), 1);
				keyword_cnt -= 1;
				score++;
				document.getElementById('score').innerHTML = "Score : " + score;
			}
			text.value = "";
			if (score >= goal) { gamewin(); return; }
			if (keyword.length == 0) { gameover(2); return; }
		}
		return;
	}

	var game = [];

	var setInterval1 = setInterval(function () {
		game.push(new keyword_rain());
	}, 1000);

	var setInterval2 = setInterval(function () {
		for (var x in game) { game[x].move(); }
	}, 15);

	document.addEventListener("keydown", e => {
		keydown(e.key);
	})
}
{% endhighlight %}

* CSS
{% highlight css %}
.rock > #canvas {
    background-color: #E3E3E3;
    width: inherit;
    height: 400px;
}

.rock > #line {
    width: inherit;
    border: 3px solid #EEEEEE;
}

.rock > #text {
    font-size: 23px;
    width: 300px;
    height: 30px;
}

.rock > #auth {
    width: 100px;
    height: 30px;
}
{% endhighlight %}

## Beach

{% highlight razor %}
@page "/beach"

@inject NavigationManager NavigationManager
@inject IJSRuntime JSRuntime

<div class="beach">
    <div Id="game-board"></div>
</div>

<style>
    * {
        margin: 0 !important;
        padding: 0 !important;
    }

    body {
        min-height: 100vh;
        width: 100vw;
        display: flex;
        justify-content: center;
        align-items: center;
        margin: 0;
        background-color: black;
    }
</style>

@code {
    protected override void OnAfterRender(bool firstRender) {
        if (firstRender)
            JSRuntime.InvokeVoidAsync("beach");
    }
}
{% endhighlight %}

* Javascript

{% highlight js %}
function beach() {
    let lastRenderTime = 0;
    let gameOver = false;
    const gameBoard = document.getElementById('game-board');
    const GRID_SIZE = 21;

    function update() {
        updateCrab();
        updateBaby();
        checkDeath();
    }

    function draw(gameBoard) {
        gameBoard.innerHTML = '';
        drawCrab(gameBoard);
        drawBaby(gameBoard);
    }

    function checkDeath() {
        gameOver = outsideGrid(getCrabHead()) || crabIntersection();

    }

    const CRAB_SPEED = 2;
    const crabBody = [{ x: 11, y: 11 }];
    let newSegments = 0;

    function updateCrab() {
        addSegments();
        const inputDirection = getInputDirection();
        for (let i = crabBody.length - 2; i >= 0; i--) {
            crabBody[i + 1] = { ...crabBody[i] };
        }

        crabBody[0].x += inputDirection.x;
        crabBody[0].y += inputDirection.y;
    }

    function drawCrab(gameBoard) {
        crabBody.forEach(segment => {
            const crabElement = document.createElement('div');
            crabElement.style.gridRowStart = segment.y;
            crabElement.style.gridColumnStart = segment.x;
            crabElement.classList.add('crab');
            gameBoard.appendChild(crabElement);
        })
    }

    function expandCrab(amount) {
        newSegments += amount;
    }

    function onCrab(position, { ignoreHead = false } = {} ) {
        return crabBody.some((segment, index) => {
            if (ignoreHead && index == 0) return false;
            return equalPositions(segment, position);
        })
    }

    function equalPositions(pos1, pos2) {
        return pos1.x == pos2.x && pos1.y == pos2.y;
    }

    function addSegments() {
        for (let i = 0; i < newSegments; i++) {
            crabBody.push({ ...crabBody[crabBody.length - 1] });
        }

        newSegments = 0;
    }

    function getCrabHead() {
        return crabBody[0];
    }

    function crabIntersection() {
        return onCrab(crabBody[0], { ignoreHead: true });
    }

    let baby = getRandomBabyPosition();

    const EXPANSION_RATE = 1;

    function updateBaby() {
        if (onCrab(baby)) {
            expandCrab(EXPANSION_RATE);
            baby = getRandomBabyPosition();
        }
    }

    function drawBaby(gameBoard) {
        const babyElement = document.createElement('div');
        babyElement.style.gridRowStart = baby.y;
        babyElement.style.gridColumnStart = baby.x;
        babyElement.classList.add('baby');
        gameBoard.appendChild(babyElement);
    }

    function getRandomBabyPosition() {
        let newBabyPosition
        while (newBabyPosition == null || onCrab(newBabyPosition)) {
            newBabyPosition = randomGridPosition();
        }

        return newBabyPosition;
    }


    function randomGridPosition() {
        return {
            x: Math.floor(Math.random() * GRID_SIZE) + 1,
            y: Math.floor(Math.random() * GRID_SIZE) + 1
        };
    }

    function outsideGrid(position) {
        return (
            position.x < 1 || position.x > GRID_SIZE || position.y < 1 || position.y > GRID_SIZE
        );
    }

    let inputDirection = { x: 0, y: 0 };
    let lastInputDirection = { x: 0, y: 0 };

    function getInputDirection() {
        lastInputDirection = inputDirection;
        return inputDirection;
    }

    function animate() {
        if (gameOver) {
            location = "/ending-missing";
            return alert("Game Over");
        }

        if (crabBody.length >= 10) {
            location = "/ending-clear";
            return alert("Clear");
        }

        requestAnimationFrame(animate);
        let currentTime = new Date().getTime();
        const secondsSinceLastRender = (currentTime - lastRenderTime) / 1000;
        if (secondsSinceLastRender < 1 / CRAB_SPEED) return

        lastRenderTime = currentTime;

        update();

        draw(gameBoard);
    }
    animate();

    document.addEventListener('keydown', e => {
        switch (e.key) {
            case 'ArrowUp':
                if (lastInputDirection.y != 0) break;
                inputDirection = { x: 0, y: -1 };
                break;
            case 'ArrowDown':
                if (lastInputDirection.y != 0) break;
                inputDirection = { x: 0, y: 1 };
                break;
            case 'ArrowLeft':
                if (lastInputDirection.x != 0) break;
                inputDirection = { x: -1, y: 0 };
                break;
            case 'ArrowRight':
                if (lastInputDirection.x != 0) break;
                inputDirection = { x: 1, y: 0 };
                break;
        }
    })
}
{% endhighlight %}

* CSS
{% highlight css %}
.beach > #game-board {
    background-color: #CCC;
    width: 100vmin;
    height: 100vmin;
    display: grid;
    grid-template-rows: repeat(21, 1fr);
    grid-template-columns: repeat(21, 1fr);
}

    .beach > #game-board > .crab {
        background-image: url(../image/pixel_crab.png);
        background-size: 35px 35px;
        background-repeat: no-repeat;
        background-position: center center;
    }

    .beach > #game-board > .baby {
        background-image: url(../image/pixel_crab.png);
        background-size: 20px 20px;
        background-repeat: no-repeat;
        background-position: center center;
    }
{% endhighlight %}

[Download](https://github.com/leehuhlee/InteractiveWeb){: .btn}
