---
layout: post
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
{% highlight YML %}
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
{% highlight HTML %}
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
{% highlight CSS %}
.logo {
  width: 283px;
  margin-bottom:12.5px;
  font-weight:$font-weight-bold;
}
{% endhighlight %}

* _sass/_base.scss
{% highlight CSS %}
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
{% highlight CSS %}
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
{% highlight YML %}
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
{% highlight HTML %}
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
{% highlight CSS %}
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

# Code

[Download](https://github.com/Maggienhanna/Maggienhanna.github.io){: .btn}