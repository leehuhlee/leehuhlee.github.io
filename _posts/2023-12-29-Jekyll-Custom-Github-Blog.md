---
layout: project
title: "[Jekyll] Jekyll Custom Github Blog"
date: 2023-12-29
excerpt: "Jekyll"
project: true
tags: [Jekyll, HTML, CSS, JS]
comments: false
---

### Demo
<a href="https://maggienhanna.github.io/">Art | Baek Ha</a>

# Theme
- Download theme <a href="https://github.com/wild-flame/jekyll-simple">Jekyll-Simple</a>
- In this theme there is no Gemfile. I made Gemfile with `bundle init`.

# Config
- `_config.yml` is a setting file for blog.
- If you need more plugins, use `plug-in` field and add plugins on `Gemfile` too.

* _config.yml
{% highlight yaml %}
title: Baek Ha
email: baek.ha.maggie@gmail.com
description: > # this means to ignore newlines until "baseurl:"
  Baek Ha
baseurl: # the subpath of your site, e.g. /blog
url: "https://maggienhanna.github.io/" # the base hostname & protocol for your site
instagram_username:  baek_ha.aaaaaaaaa

markdown: kramdown
kramdown:
  input: GFM
{% endhighlight %}

# Header

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/0.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/0.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

## Change Layout
- Move logo out from side menu.
- Original side bar position was right. But I wanted to add dropdown menu as nested menu, so I changed side bar position to left.
- Remove container border.

* _layouts\default.html
{% highlight html %}
<!DOCTYPE html>
<html>
  {% include head.html %}
  <body>
    <div class="page-content">
      <div class="container">
          <h2 class="logo"><a href="{{ site.baseurl }}/">{{ site.title }}</a></h2>
        <div class="three columns">
          {% include header.html %}
        </div>
        <div class="nine columns" style="z-index:100;">
          <div class="wrapper">
            {{ content }}
          </div>
        </div>
      </div>
      {% include footer.html %} 
    </div>
  </body>
</html>
{% endhighlight %}

* _sass/_layout.scss
{% highlight css %}
.logo {
  width: 283px;
  margin-bottom:12.5px;
  font-weight:$font-weight-bold;
}
{% endhighlight %}

* _sass/_base.scss
{% highlight css %}
.container {
  position:relative;
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  padding: 10px 10px;
  box-sizing: border-box;
  /* for test only */
  // border: 1px dashed #eeeeee;
}

...

@media (min-width: 781px) {
  .container {
    width: 90%; }
  .column,
  .columns {
    // margin-right: 4%; 
  }
  ...
}
{% endhighlight %}

* _sass/_base.scss
{% highlight css %}
@media (min-width: 781px) {
  .container {
    width: 90%; }
  .column,
  .columns {
    // margin-right: 4%; 
  }
  ...
}
{% endhighlight %}

## Add Dropdown Nested Menu
- I needed dropdown menu because `work` and `exhibition` menus have sub menu as years.
- For url listing, I used `_data/navigation.yml`.
- Enough margin-top for `dropdown-content` is important. If the `dropdown-content` space wraps main menu, dropdown menus are dissappeared in 1 sec because it recognizes that you clicked first sub menu.
- Every posts has `permalink`, which links url with the post.

* _data/navigation.yml
{% highlight yaml %}
main:
  - title: "HOME"
    url: "/"
  - title: "WORK"
    sublinks:
      - title: "2023"
        url: "/work/2023"
      - title: "2022"
        url: "/work/2022"
      - title: "2021"
        url: "/work/2021"
      - title: "2020"
        url: "/work/2020"
      - title: "2018"
        url: "/work/2018"
  - title: "EXHIBITION"
    sublinks:
      - title: "2023"
        url: "/exhibition/2023"
      - title: "2021"
        url: "/exhibition/2021"
      - title: "2019-2020"
        url: "/exhibition/2019-2020"
  - title: "TEXT"
    url: "/text"
  - title: "CV"
    url: "/cv"
  - title: "CONTACT"
    url: "/contact"
{% endhighlight %}

* _includes/header.html
{% highlight html %}
<div class="site-nav">
  <nav>
    <ul class="page-link">
      {%- for link in site.data.navigation.main -%}
        {% assign class = nil %}
          {% if page.url contains link.url %}
              {% assign class = 'active' %}
          {% endif %}
          {% if link.sublinks %}
            <li class="dropdown {{ class }}">
                <a class="dropdown-toggle" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">{{ link.title }}  <i class="fa fa-caret-down fa-sm" aria-hidden="true"></i><span class="caret"></span></a>

                <ul class="dropdown-content" >
                    {% for sublink in link.sublinks %}
                        <li><a class = "dropdown-item" href="{{ site.baseurl }}{{ sublink.url }}">    {{ sublink.title }}</a></li>
                    {% endfor %}
                </ul>
            </li>
          {% else %}
            <li class="masthead__menu-item">
                <a href="{{ site.baseurl }}{{ link.url }}">{{ link.title }}</a>
            </li>
        {% endif %}
      {% endfor %}
    </ul>
  </nav>
</div>
{% endhighlight %}

* _sass/_base.scss
{% highlight css %}
@media (min-width: 781px) {
  .container {
    width: 90%; }
  .column,
  .columns {
    // margin-right: 4%; 
  }
  ...
}
{% endhighlight %}

* _sass/_layout.scss
{% highlight CSS %}
.nav {
  line-height: $base-line-height;

  .dropdown {
    // float: left;
    width: auto;
    box-sizing: border-box;
  }

  .dropdown-content {
    display: none;
    // position: absolute;

    top: 0;
    right: auto;
    left: 100%;
    margin-top: 1em;
    // margin-left: $dropdown-spacer;

    background-color: white;
    // min-width: 160px;
    // box-shadow: $box-shadow;
    z-index: 1;
    box-sizing: border-box;
  }

  .dropdown:hover .dropdown-content {
    display: block;
    // font-size: 17px;
  }

  .dropdown-item {
    padding: 10px;
    font-size: 15px;
  }

  .page-link {
    color: $text-color;
    line-height: 1.0;
    display: block;
    padding: 5px 0px 0px 0px;

    li {
      font-size:1.5rem;
      list-style:none;
      font-weight: 100;
      margin-bottom:1rem;

      -webkit-box-flex: 0;
      -ms-flex: none;
      flex: none;
      display: list-item;
      text-align: -webkit-match-parent;
    }

    a {
      position: relative;

      &:before {
        content: "";
        position: absolute;
        left: 0;
        bottom: 0;
        height: 4px;
        background: white;
        width: 100%;
        // -webkit-transition: $global-transition;
        // transition: $global-transition;
        -webkit-transform: scaleX(0) translate3d(0, 0, 0);
        transform: scaleX(0) translate3d(0, 0, 0); // hide
      }

      &:hover:before {
        -webkit-transform: scaleX(1);
        -ms-transform: scaleX(1);
        transform: scaleX(1); // reveal
      }
    }

    a:active {
      font-weight: 500;
    }    
  }

  .hidden-links {
    position: absolute;
    top: 100%;
    right: 0;
    margin-top: 15px;
    padding: 5px;
    border: 1px solid none;
    border-radius: none;
    background: $background-color;
    -webkit-box-shadow: 0 2px 4px 0 rgba(#000, 0.16),
      0 2px 10px 0 rgba(#000, 0.12);
    box-shadow: 0 2px 4px 0 rgba(#000, 0.16), 0 2px 10px 0 rgba(#000, 0.12);

    &.hidden {
      display: none;
    }

    a {
      margin: 0;
      padding: 10px 20px;
      font-size: 15px;

      &:hover {
        color: #222;
        background: white;
      }
    }

    &:before {
      content: "";
      position: absolute;
      top: -11px;
      right: 10px;
      width: 0;
      border-style: solid;
      border-width: 0 10px 10px;
      border-color: none transparent;
      display: block;
      z-index: 0;
    }

    &:after {
      content: "";
      position: absolute;
      top: -10px;
      right: 10px;
      width: 0;
      border-style: solid;
      border-width: 0 10px 10px;
      border-color: $background-color transparent;
      display: block;
      z-index: 1;
    }

    li {
      display: block;
      border-bottom: 1px solid none;

      &:last-child {
        border-bottom: none;
      }
    }
  }
}
{% endhighlight %}

### References
- <a href="https://thinkshout.com/blog/2014/12/creating-dynamic-menus-in-jekyll">https://thinkshout.com/blog/2014/12/creating-dynamic-menus-in-jekyll/</a>
- <a href="http://planetjekyll.github.io/snippets/nav-with-data.html">http://planetjekyll.github.io/snippets/nav-with-data.html</a>
- <a href="https://github.com/mmistakes/minimal-mistakes/issues/1801">https://github.com/mmistakes/minimal-mistakes/issues/1801</a>

# Footer

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/1.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/1.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

## Change Layout
- Change size of footer continer.
- Add instagram, hyperlink and mailto.
- Download Instaram svg <a href="https://icons8.com/icons/set/instagram">here</a> and add svg file to `_includes` folder with name `icon-instagram.svg`.

* _includes/footer.html
{% highlight html %}
<footer class="site-footer">
  <div class="container footer-container">
    <div class="footer left column one-half-left">
      <section class="small-font">
        © 2024 by <a href="https://www.instagram.com/baek_ha.aaaaaaaaa/">Baek Ha</a>
      </section>
    </div>

    <div class="footer right column one-half-right">
      <section class="small-font">
        <a href="https://www.instagram.com/baek_ha.aaaaaaaaa">https://www.instagram.com/baek_ha.aaaaaaaaa</a>
        {% if site.instagram_username %}
        {% include icon-instagram.html username=site.instagram_username %}
        {% endif %}
        <a href="mailto:baek.ha.maggie@gmail.com">baek.ha.maggie@gmail.com</a>
      </section>
    </div>
  </div>
</footer>
{% endhighlight %}

* _includes/icon-instagram.html
{% highlight html %}
<a href="https://www.instagram.com/{{ include.username }}"><span class="icon icon--github">{% include icon-instagram.svg %}</span></a>
{% endhighlight %}

* _sass/_base.scss
{% highlight css %}
@media (min-width: 401px) {
  .container {
    width: 100%;
    padding: 64px 15px; }
}
{% endhighlight %}

* _sass/_layout.scss
{% highlight css %}
.site-footer {
  position: absolute;
  bottom: 0;
  padding-bottom: 0rem;
  width:100%;

  /* footer style */    
  color: #99999a;
  background-color: #FAFAFB;

  .left {
    a {
      color: #666;
      border-bottom:1px dotted;
      text-decoration:none;
    }
    a:hover {
      color:red;
      border-bottom:1px dotted;
    }
  }

  .right{
    a {
      color: #666;
      border-bottom:1px none;
      text-decoration:none;
      vertical-align : top;
    }
  }
}
{% endhighlight %}

# Home
- Content start line should be aligned with side menu.
- Image should be showed with modal as Gallery when it is clicked.
- For Gallery I downloaded button images from <a href="https://github.com/lokesh/lightbox2/tree/dev/src/images">here</a>.

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/2.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/2.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

* index.html
{% highlight html %}
<div class="home">
  <figure>
    <a href="assets/img/home/home.jpg" data-lightbox="home" >
      <img src="assets/img/home/home.jpg" alt="home" title="Home">
    </a>
  </figure>
</div>
{% endhighlight %}

* _includes/head.html
{% highlight html %}
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <title>Art | Baek Ha</title>
  <meta name="description" content="{% if page.excerpt %}{{ page.excerpt | strip_html | strip_newlines | truncate: 160 }}{% else %}{{ site.description }}{% endif %}">

  <link rel="canonical" href="{{ page.url | replace:'index.html','' | prepend: site.baseurl | prepend: site.url }}">

  <script src="/assets/js/lightbox-plus-jquery.js"></script>
  <link rel="stylesheet" href="/assets/css/lightbox.css">
</head>
{% endhighlight %}

* assets/css/lightbox.min.css
- Add this <a href="https://raw.githubusercontent.com/lokesh/lightbox2/dev/dist/css/lightbox.min.css">css script</a>.

* assets/js/lightbox-plus-jquery.js
- Add this <a href="https://raw.githubusercontent.com/lokesh/lightbox2/dev/dist/js/lightbox-plus-jquery.min.js">js script</a>.

### References
- <a href="https://www.friedrichkurz.me/blog/2021/12/31/jekyll-lightbox/">https://www.friedrichkurz.me/blog/2021/12/31/jekyll-lightbox/</a>

# Work
- Show just title.
- Set large margin because those pages of work have just images.
- Add image caption.

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/3.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/3.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

* _layouts/post.html
{% highlight html %}
<article class="post" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="artilce_header">
    <h1 class="artilce_title" itemprop="name headline">{{ page.title }}</h1>
    <!-- <p class="artilce_meta"><time datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">{{ page.date | date: "%b %-d, %Y" }}</time>{% if page.author %} • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span itemprop="name">{{ page.author }}</span></span>{% endif %}</p> -->
  </header>

  <div class="article-content" itemprop="articleBody">
    {{ page.content }}
  </div>

</article>
{% endhighlight %}

* _posts/work/2023-12-29-work-2023.md
{% highlight html %}
<figure class="work">
  <a href="/assets/img/work/2023/1.jpg" data-lightbox="work-2023" data-title="조력자 MOMO">
    <img src="/assets/img/work/2023/1.jpg" alt="조력자 MOMO" title="조력자 MOMO">
  </a>
  <figcaption>조력자 MOMO</figcaption>
</figure>
{% endhighlight %}

* _sass/_normalize.scss
{% highlight css %}
figure {
  &.work {
    margin: 15em 40px;
  }
}
{% endhighlight %}

* _sass/_base.scss
{% highlight css %}
figcaption{
  color: rgb(153, 153, 153) ;
  text-align: center;
  font-size: 13px;
  margin: 2em 0px;
}
{% endhighlight %}

# Exhibition
- Exhibition margin is smaller then work's, because there is some description too.

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/4.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/4.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

* _posts/exhibition/2023-12-29-exhibition-2023.md
{% highlight html %}
<div class="exhibition">
  <div class="title">
    <h1>고양이 현대미술기획전 &lt;작은 파티 고양이&gt;</h1>
    <p>
      장소: 고양이스퀘어 <br>
      날짜 : 2023. 11. 13.(월). ~ 11. 26.(일).<br>
      운영 시간 : 평일 10시 30분~19시, 주말 11시~18시<br>
    </p>
  </div>

  <div class="content">
    <figure class="exhibition">
      <a href="/assets/img/exhibition/2023/2023_01.jpg" data-lightbox="exhibition-2023" data-title="">
        <img src="/assets/img/exhibition/2023/2023_01.jpg" alt="" title="">
      </a>
    </figure>
  </div>
</div>
{% endhighlight %}

* _sass/_normalize.scss
{% highlight css %}
figure {
  ...

  &.exhibition {
    margin: 5em 40px;
  }
}
{% endhighlight %}

* _sass/_layout.scss
{% highlight css %}
.exhibition {
  text-align: center;

  .title{
    margin-top: 8em;
  }

  .content{
    margin-bottom: 8em;
  }
}
{% endhighlight %}

# CV
- I made CV with table.

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/5.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/5.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

* _posts/cv/2023-12-29-cv.md
{% highlight html %}
<div class="cv">
  <table>
    <tbody>
      <tr>
        <td colspan="2">Baek Ha</td>
      </tr>
      <tr>
        <td colspan="2">b.1996</td>
      </tr>
      <tr>
        <td class="title" colspan="2">학력</td>
      </tr>
      <tr>
        <td>2019</td>
        <td>Graduated Cat University Fine Art</td>
      </tr>
    </tbody>
  </table>
</div>
{% endhighlight %}

* _sass/_normalize.scss
{% highlight css %}
table {
  border-collapse: collapse;
  border-spacing: 0;
  margin-top: 5em;
}

td,
th {
  padding: 0;
}

td {
  &.title {
    padding-top: 2em;
  }
}
{% endhighlight %}

* _sass/_layout.scss
{% highlight CSS %}
.cv {
  font-size: 13px;
}
{% endhighlight %}

# Contact

* Result
<figure>
  <a href="/assets/img/posts/jekyll-custom-github-blog/6.jpg"><img src="/assets/img/posts/jekyll-custom-github-blog/6.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

* _posts/cv/2023-12-29-contact.md
{% highlight html %}
<div class="contact">
  <a href="mailto:baek.ha.maggie@gmail.com">baek.ha.maggie@gmail.com</a><br><br>
  <a href="https://www.instagram.com/baek_ha.aaaaaaaaa/">https://www.instagram.com/baek_ha.aaaaaaaaa/</a><br>
  <a href="https://www.instagram.com/maggie.i.can/">https://www.instagram.com/maggie.i.can/</a><br><br>
  <p>(+82)10 0000 0000</p>
</div>
{% endhighlight %}

* _sass/_layout.scss
{% highlight css %}
.contact {
  a {
    border-bottom: none;
  }
  
  a:hover{
    border-bottom: none;
    color: #666;
  }
}
{% endhighlight %}

# Code

[Download](https://github.com/Maggienhanna/Maggienhanna.github.io){: .btn}