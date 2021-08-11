---
layout: post
title: "[CSS] Flexbox Froggy"
date: 2021-08-11
excerpt: "CSS Flexbox"
tags: [CSS, Frontend, Web]
comments: false
---

<center>Reference by <a href="https://flexboxfroggy.com/"></a></center>

# justify-content 

## Elements
 - flex-start: Items align to the left side of the container.
 - flex-end: Items align to the right side of the container.
 - center: Items align at the center of the container.
 - space-between: Items display with equal spacing between them.
 - space-around: Items display with equal spacing around them.

### Solution
{% highlight CSS %}
#pond {
  display: flex;
  justify-content: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/0.jpg"><img src="/assets/img/posts/css_flexbox_froggy/0.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/1.jpg"><img src="/assets/img/posts/css_flexbox_froggy/1.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  justify-content: center;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/2.jpg"><img src="/assets/img/posts/css_flexbox_froggy/2.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/3.jpg"><img src="/assets/img/posts/css_flexbox_froggy/3.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  justify-content: space-around;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/4.jpg"><img src="/assets/img/posts/css_flexbox_froggy/4.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/5.jpg"><img src="/assets/img/posts/css_flexbox_froggy/5.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  justify-content: space-between;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/6.jpg"><img src="/assets/img/posts/css_flexbox_froggy/6.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/7.jpg"><img src="/assets/img/posts/css_flexbox_froggy/7.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

# align-items

## Elements
 - flex-start: Items align to the top of the container.
 - flex-end: Items align to the bottom of the container.
 - center: Items align at the vertical center of the container.
 - baseline: Items display at the baseline of the container.
 - stretch: Items are stretched to fit the container.

### Solutions

{% highlight CSS %}
#pond {
  display: flex;
  align-items: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/8.jpg"><img src="/assets/img/posts/css_flexbox_froggy/8.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/9.jpg"><img src="/assets/img/posts/css_flexbox_froggy/9.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  justify-content: center;
  align-items: center;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/10.jpg"><img src="/assets/img/posts/css_flexbox_froggy/10.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/11.jpg"><img src="/assets/img/posts/css_flexbox_froggy/11.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/12.jpg"><img src="/assets/img/posts/css_flexbox_froggy/12.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/13.jpg"><img src="/assets/img/posts/css_flexbox_froggy/13.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

# flex-direction

## Elements
 - row: Items are placed the same as the text direction.
 - row-reverse: Items are placed opposite to the text direction.
 - column: Items are placed top to bottom.
 - column-reverse: Items are placed bottom to top.

### Solutions

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: row-reverse;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/14.jpg"><img src="/assets/img/posts/css_flexbox_froggy/14.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/15.jpg"><img src="/assets/img/posts/css_flexbox_froggy/15.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: column;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/16.jpg"><img src="/assets/img/posts/css_flexbox_froggy/16.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/17.jpg"><img src="/assets/img/posts/css_flexbox_froggy/17.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: row-reverse;
  justify-content: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/18.jpg"><img src="/assets/img/posts/css_flexbox_froggy/18.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/19.jpg"><img src="/assets/img/posts/css_flexbox_froggy/19.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/20.jpg"><img src="/assets/img/posts/css_flexbox_froggy/20.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/21.jpg"><img src="/assets/img/posts/css_flexbox_froggy/21.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: column-reverse;
  justify-content: space-between;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/22.jpg"><img src="/assets/img/posts/css_flexbox_froggy/22.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/23.jpg"><img src="/assets/img/posts/css_flexbox_froggy/23.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: row-reverse;
  justify-content: center;
  align-items: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/24.jpg"><img src="/assets/img/posts/css_flexbox_froggy/24.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/25.jpg"><img src="/assets/img/posts/css_flexbox_froggy/25.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

# order

## Elements
 - By default, items have a value of 0, but we can use this property to also set it to a positive or negative integer value (-2, -1, 0, 1, 2).

### Solutions
{% highlight CSS %}
#pond {
  display: flex;
}

.yellow {
  order: 3;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/26.jpg"><img src="/assets/img/posts/css_flexbox_froggy/26.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/27.jpg"><img src="/assets/img/posts/css_flexbox_froggy/27.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
}

.red {
  order: -1;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/28.jpg"><img src="/assets/img/posts/css_flexbox_froggy/28.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/29.jpg"><img src="/assets/img/posts/css_flexbox_froggy/29.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

#  align-self

## Elements
 - This property accepts the same values as align-items and its value for the specific item.

 ### Solutions
{% highlight CSS %}
#pond {
  display: flex;
  align-items: flex-start;
}

.yellow {
  align-self: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/30.jpg"><img src="/assets/img/posts/css_flexbox_froggy/30.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/31.jpg"><img src="/assets/img/posts/css_flexbox_froggy/31.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  align-items: flex-start;
}

.yellow {
  order: 1;
  align-self: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/32.jpg"><img src="/assets/img/posts/css_flexbox_froggy/32.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/33.jpg"><img src="/assets/img/posts/css_flexbox_froggy/33.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

#  flex-wrap

## Elements
 - nowrap: Every item is fit to a single line.
 - wrap: Items wrap around to additional lines.
 - wrap-reverse: Items wrap around to additional lines in reverse.

### Solutions

{% highlight CSS %}
#pond {
  display: flex;
  flex-wrap: wrap;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/34.jpg"><img src="/assets/img/posts/css_flexbox_froggy/34.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/35.jpg"><img src="/assets/img/posts/css_flexbox_froggy/35.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/36.jpg"><img src="/assets/img/posts/css_flexbox_froggy/36.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/37.jpg"><img src="/assets/img/posts/css_flexbox_froggy/37.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

# flex-flow 

## Elements
 - The two properties flex-direction and flex-wrap are used so often together that the shorthand property flex-flow was created to combine them. This shorthand property accepts the value of the two properties separated by a space.
 - For example, you can use flex-flow: row wrap to set rows and wrap them.

### Solutions
{% highlight CSS %}
#pond {
  display: flex;
  flex-flow: column wrap;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/38.jpg"><img src="/assets/img/posts/css_flexbox_froggy/38.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/39.jpg"><img src="/assets/img/posts/css_flexbox_froggy/39.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

# align-content

## Elements
 - flex-start: Lines are packed at the top of the container.
 - flex-end: Lines are packed at the bottom of the container.
 - center: Lines are packed at the vertical center of the container.
 - space-between: Lines display with equal spacing between them.
 - space-around: Lines display with equal spacing around them.
 - stretch: Lines are stretched to fit the container.

### Solutions

{% highlight CSS %}
#pond {
  display: flex;
  flex-wrap: wrap;
  align-content: flex-start;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/40.jpg"><img src="/assets/img/posts/css_flexbox_froggy/40.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/41.jpg"><img src="/assets/img/posts/css_flexbox_froggy/41.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-wrap: wrap;
align-content: flex-end;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/42.jpg"><img src="/assets/img/posts/css_flexbox_froggy/42.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/43.jpg"><img src="/assets/img/posts/css_flexbox_froggy/43.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-wrap: wrap;
  flex-direction: column-reverse;
  align-content: center;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/44.jpg"><img src="/assets/img/posts/css_flexbox_froggy/44.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/45.jpg"><img src="/assets/img/posts/css_flexbox_froggy/45.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

{% highlight CSS %}
#pond {
  display: flex;
  flex-flow: column-reverse wrap-reverse;
  justify-content: center;
  align-content: space-between;
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/css_flexbox_froggy/46.jpg"><img src="/assets/img/posts/css_flexbox_froggy/46.jpg"></a>
  <a href="/assets/img/posts/css_flexbox_froggy/47.jpg"><img src="/assets/img/posts/css_flexbox_froggy/47.jpg"></a>
	<figcaption>Flexbox Froggy</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
