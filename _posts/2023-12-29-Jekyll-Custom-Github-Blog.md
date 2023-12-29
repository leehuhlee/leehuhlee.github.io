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
{% endhighlight %}

# Code

[Download](https://github.com/Maggienhanna/Maggienhanna.github.io){: .btn}