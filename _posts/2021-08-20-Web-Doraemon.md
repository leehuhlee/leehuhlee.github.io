---
layout: post
title: "[Web] Doraemon"
date: 2021-08-20
excerpt: "Eyes Rolling"
tags: [HTML, CSS, JavaScript, Web]
comments: false
---

# Demo

<iframe width="560" height="315" src="/assets/video/posts/web_doraemon/Web-Doraemon.mp4" frameborder="0"> </iframe>

# Code

* HTML
{% highlight html %}
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Doraemon</title>
        <style>
            ...
        </style>
    </head>
    <body>
        <div class="doraemon">
            <div class="eyes">
                <div class="eye eye-1">
                    <div class="pupil" style="transform: rotate(-56.3835deg);"></div>
                </div>
                <div class="eye eye-2">
                    <div class="pupil" style="transform: rotate(-52.2706deg);"></div>
                </div>
            </div>
        </div>
        <script>
            ...
        </script>
    </body>
</html>
{% endhighlight %}

* CSS
{% highlight css %}
<style>
    body{
        margin:0
    }

    .doraemon{
        position:absolute;
        top:50%;
        left:50%;
        width:600px;
        height:800px;
        background:url(doraemon.gif) no-repeat 50% 50%;
        background-size:contain;
        transform:translate(-50%,-50%)
    }

    .eyes{
        display:flex;
        position:absolute;
        top:27px;
        left:162px
    }

    .eye{
        position:relative;
        width:120px;
        height:120px;
        border:4px solid #000;
        border-radius:50%;
        background-color:#fff;
        transform:scaleX(.9)
    }

    .eye-1{
        left:7px
    }

    .eye-2{
        left:-7px
    }

    .pupil{
        position:absolute;
        left:45px;
        bottom:0;
        width:30px;
        height:inherit
    }

    .pupil:before{
        content:'';
        display:block;
        position:absolute;
        left:0;
        bottom:0;
        width:inherit;
        height:30px;
        border-radius:50%;
        background-color:#000
    }

    .pupil:after{
        content:'';
        display:block;
        position:absolute;
        left:12px;
        bottom:12px;
        width:6px;
        height:6px;
        border-radius:50%;
        background-color:#fff
    }
</style>
{% endhighlight %}

* JavaScript
{% highlight js %}
<script>
    var pupil=function(selector){
        var eye=document.querySelector(selector),
        pupil=eye.querySelector('.pupil'),
        eyearea=eye.getBoundingClientRect();
        var pupilrolling=function(mouseX,mouseY){
            // Math.atan2 returns radian value
            var radians=Math.atan2(mouseY-(eyearea.y+eyearea.height*0.5),mouseX-(eyearea.x+eyearea.width*0.5));
            // angle = 180*radians/Math.PI
            // angle needs -90
            // because init eye direction is bottom
            pupil.style.transform='rotate('+(180*radians/Math.PI-90)+'deg)';
        };

        // return object
        // method: function
        return{
            pupilrolling:pupilrolling
        };
    };

    var lefteye=pupil('.eye-1');
    var righteye=pupil('.eye-2');
    window.addEventListener('mousemove',function(e){
        lefteye.pupilrolling(e.pageX,e.pageY);
        righteye.pupilrolling(e.pageX,e.pageY);
    });
</script>
{% endhighlight %}

[Download](https://github.com/leehuhlee/InteractiveWeb){: .btn}
