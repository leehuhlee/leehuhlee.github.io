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
- <a href="https://github.com/Maggienhanna/Maggienhanna.github.io/blob/main/_layouts/default.html">Code</a>

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



# Code

[Download](https://github.com/Maggienhanna/Maggienhanna.github.io){: .btn}