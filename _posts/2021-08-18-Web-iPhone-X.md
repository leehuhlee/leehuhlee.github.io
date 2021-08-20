---
layout: post
title: "[Web] iPhone X"
date: 2021-08-18
excerpt: "Create same page with iPhone X"
tags: [HTML, CSS, JavaScript, Web]
comments: false
---

<center>Reference by <a href="https://www.apple.com/de/iphone-10/">Apple Store iPhone 10</a></center>

# Demo

<iframe width="560" height="315" src="/assets/video/posts/web_iphone_x/Web-iPhone-X.mp4" frameborder="0"> </iframe>

# Code

* HTML
{% highlight html %}
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>iPhone X</title>
    <style>
        ...
    </style>
</head>
<body>
    <div class="video-wrapper">
        <video muted="" playsinline="" autoplay="" loop="" id="iphone-x" src="https://images.apple.com/media/us/iphone-x/2017/01df5b43-28e4-4848-bf20-490c34a926a7/overview/primary/hero/large_2x.mp4"></video>
    </div>
    <canvas id="cover-canvas"></canvas>
    <script>
        ...
    </script>
</body>
</html>
{% endhighlight %}

* CSS
{% highlight css %}
<style>
    /* Clear default style*/
    html{
        height:100%;
        font-family:sans-serif;
        -webkit-text-size-adjust:100%;
        -ms-text-size-adjust:100%;
        -webkit-tap-highlight-color:rgba(0,0,0,0)
    }
    
    body{
        height:100%;
        -webkit-font-smoothing:antialiased;
        font-smoothing:antialiased;
        -webkit-overflow-scrolling:touch;
        overflow-scrolling:touch
    }
    
    html,body,div,span,applet,object,iframe,figure,h1,h2,h3,h4,h5,h6,p,blockquote,pre,a,abbr,acronym,address,big,cite,code,del,dfn,em,font,img,ins,kbd,q,s,samp,small,strike,strong,sub,sup,tt,var,dl,dt,dd,ol,ul,li,fieldset,form,label,legend,table,caption,tbody,tfoot,thead,tr,th,td{
        margin:0;
        padding:0;
        border:0
    }
    
    article,aside,details,figcaption,figure,footer,header,hgroup,main,nav,section,summary{
        display:block
    }
    
    div,article,section,p,ul,li,span,label{
        box-sizing:border-box
    }
    /**********************/

    body{
        background:#000
    }
    
    #cover-canvas{
        position:fixed;
        top:0;
        left:0
    }
    
    .video-wrapper{
        display:flex;
        justify-content:center;
        align-items:center;
        overflow:hidden;
        position:fixed;
        top:0;
        left:0;
        width:100vw;
        height:100vh
    }
    
    #video-studiomeal{
        /* This video scale will be changed */
        transform:scale(1)
    }
</style>
{% endhighlight %}

* JavaScript
{% highlight js %}
<script>
    'use strict';
    (function(){
        var elemCanvas,
            elemVideo,
            elemPhone,
            context,
            windowWidth=0, // window width
            windowHeight=0, // window height
            canvasWidth=0, // canvas width
            canvasHeight=0, // canvas height
            scrollY=0, // current scroll pos
            relativeScrollY=0, // relative scroll pos in each frame
            prevDurations=0, // duration from prev keyframe
            totalScrollHeight=0, // total height for scroll(body height)
            currentKeyframe=0, // current keyframe (0, 1)
            phoneWidth=4000, // iPhone image width
            phoneHeight=4000, // iPhone image height
            resizeHandler,
            scrollHandler,
            render,
            drawCanvas,
            calcAnimationValue,
            calcFinalValue,
            init,
            pixelDuration=0, // scroll height of each keyframe

            // there is 2 keyframes
            // first keyframe: start
            // second keyframe: change X to iPhone
            keyframes=[
                {
                    animationValues:{
                        videoScale:[1,2], // to be bigger
                        triangleMove:[0,200], // X
                        rectangleMove:[0,500]
                    }
                },
                {
                    animationValues:{
                        videoScale:[2,0.5], // to be smaller
                        triangleMove:[200,1000],
                        rectangleMove:[500,500]
                    }
                }
            ],

            // canvas
            elemBody=document.body,
            elemCanvas=document.getElementById('cover-canvas'),
            context=elemCanvas.getContext('2d');
            elemVideo=document.getElementById('iphone-x');

        init=function(){
            windowWidth=window.innerWidth;
            windowHeight=window.innerHeight;

            resizeHandler();
            render();

            // requestAnimationFrame is for smooth rendering
            // because resize and scroll are frequent event.
            window.addEventListener('resize',function(){
                requestAnimationFrame(resizeHandler);
            });
            window.addEventListener('scroll',function(){
                requestAnimationFrame(scrollHandler);
            });

            elemPhone=document.createElement('img');
            elemPhone.src='phone.png';
            elemPhone.addEventListener('load',function(){
                drawCanvas();
            });
        };

        resizeHandler=function(){
            var i;
            windowWidth=window.innerWidth;
            windowHeight=window.innerHeight;
            totalScrollHeight=0;
            // one keyframe duration is half of window height
            pixelDuration=0.5*windowHeight;

            // totalScrollHeight = windowHeight
            for(i=0;i<keyframes.length;i++){
                totalScrollHeight+=pixelDuration;
            }
            totalScrollHeight+=windowHeight;

            elemBody.style.height=totalScrollHeight+'px';
            elemCanvas.width=canvasWidth=windowWidth*2;
            elemCanvas.height=canvasHeight=windowHeight*2;
            elemCanvas.style.width=windowWidth+'px';
            elemCanvas.style.height=windowHeight+'px';
        };

        scrollHandler=function(){
            scrollY=window.pageYOffset; // current scroll pos

            // scroll range valid check
            if(scrollY<0||scrollY>(totalScrollHeight-windowHeight)){
                return;
            }

            // when scroll down 
            if(scrollY>pixelDuration+prevDurations){
                prevDurations+=pixelDuration;
                currentKeyframe++;
            }
            // when scroll up
            else if(scrollY<prevDurations){
                currentKeyframe--;
                prevDurations-=pixelDuration;
            }

            // current keyframe scroll pos
            relativeScrollY=scrollY-prevDurations;
            render();
        };

        render=function(){
            var videoScale,
                triangleMove,
                rectangleMove;
            if(keyframes[currentKeyframe]){
                videoScale=calcAnimationValue(keyframes[currentKeyframe].animationValues.videoScale);
                triangleMove=calcAnimationValue(keyframes[currentKeyframe].animationValues.triangleMove);
                rectangleMove=calcAnimationValue(keyframes[currentKeyframe].animationValues.rectangleMove);
            }
            else{
                return;
            }
            elemVideo.style.transform='scale('+videoScale+')';

            // clear canvas every time before drawing
            context.clearRect(0,0,canvasWidth,canvasHeight);

            if(elemPhone){
                drawCanvas(videoScale,triangleMove,rectangleMove);
            }
        };

        calcAnimationValue=function(values){
            // current keyframe scroll pos / keyframe scroll = ratio
            // values[1]-values[0] is diff value
            // values[0] is init value
            return(relativeScrollY/pixelDuration)*(values[1]-values[0])+values[0];
        };

        drawCanvas=function(videoScale,triangleMove,rectangleMove){
            var videoScale=videoScale||1,
                triangleMove=triangleMove||0,
                rectangleMove=rectangleMove||0;

            context.save();
            context.translate((canvasWidth-phoneWidth*videoScale)*0.5,(canvasHeight-phoneHeight*videoScale)*0.5);
            // phone image scale is changed by video scale
            context.drawImage(elemPhone,0,0,phoneWidth*videoScale,phoneHeight*videoScale);
            context.restore();

            context.fillStyle='black';

            // top triangle
            context.beginPath();
            context.moveTo(canvasWidth*0.5-1500,-triangleMove-1700);
            context.lineTo(canvasWidth*0.5,canvasHeight*0.5-150-triangleMove);
            context.lineTo(canvasWidth*0.5+1500,-triangleMove-1700);
            context.lineTo(canvasWidth*0.5-1500,-triangleMove-1700);
            context.fill();
            context.closePath();

            // bottom triangle
            context.beginPath();
            context.moveTo(canvasWidth*0.5-1500,canvasHeight+triangleMove+1700);
            context.lineTo(canvasWidth*0.5,canvasHeight*0.5+150+triangleMove);
            context.lineTo(canvasWidth*0.5+1500,canvasHeight+triangleMove+1700);
            context.lineTo(canvasWidth*0.5-1500,canvasHeight+triangleMove+1700);
            context.fill();
            context.closePath();

            // left triangle
            context.beginPath();
            context.moveTo(canvasWidth*0.5-1700-triangleMove,-1700);
            context.lineTo(canvasWidth*0.5-130-triangleMove,canvasHeight*0.5);
            context.lineTo(canvasWidth*0.5-1700-triangleMove,canvasHeight+1700);
            context.lineTo(canvasWidth*0.5-1700-triangleMove,-1700);
            context.fill();
            context.closePath();

            // right triangle
            context.beginPath();
            context.moveTo(canvasWidth*0.5+1700+triangleMove,-1700);
            context.lineTo(canvasWidth*0.5+130+triangleMove,canvasHeight*0.5);
            context.lineTo(canvasWidth*0.5+1700+triangleMove,canvasHeight+1700);
            context.lineTo(canvasWidth*0.5+1700+triangleMove,-1700);
            context.fill();
            context.closePath();

            // Box top, bottom
            context.fillRect(0,canvasHeight*0.5-2600-rectangleMove,canvasWidth,2000);
            context.fillRect(0,canvasHeight*0.5+600+rectangleMove,canvasWidth,2000);
        };

        init();
    })();
</script>
{% endhighlight %}

[Download](https://github.com/leehuhlee/InteractiveWeb){: .btn}
