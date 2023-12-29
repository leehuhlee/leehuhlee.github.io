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

# Code

[Download](https://github.com/Maggienhanna/Maggienhanna.github.io){: .btn}