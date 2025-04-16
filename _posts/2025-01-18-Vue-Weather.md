---
layout: post
title: "[vue.js, tailwind] Weather Application"
date: 2025-01-18
excerpt: "vue.js"
tags: [vue.js, tailwind]
comments: false
---

# Vue Install
- open terminal and type `npm init vue@latest` to create vue project

<figure>
  <a href="/assets/img/posts/vue_weather/3.jpg"><img src="/assets/img/posts/vue_weather/3.jpg"></a>
	<figcaption>Vue Install</figcaption>
</figure>

### Extensions
* Vue Language Features (Volar)
* Vue VSCode Snippets
* Tailwind CSS IntelliSense

<figure class="third">
  <a href="/assets/img/posts/vue_weather/0.jpg"><img src="/assets/img/posts/vue_weather/0.jpg"></a>
  <a href="/assets/img/posts/vue_weather/1.jpg"><img src="/assets/img/posts/vue_weather/1.jpg"></a>
  <a href="/assets/img/posts/vue_weather/2.jpg"><img src="/assets/img/posts/vue_weather/2.jpg"></a>
	<figcaption>Vue Install</figcaption>
</figure>

### Prepare
- remove all files and folders in components folder

<figure class="half">
  <a href="/assets/img/posts/vue_weather/4.jpg"><img src="/assets/img/posts/vue_weather/4.jpg"></a>
  <a href="/assets/img/posts/vue_weather/5.jpg"><img src="/assets/img/posts/vue_weather/5.jpg"></a>
	<figcaption>Vue Install</figcaption>
</figure>

- remove all files in assets folder

<figure class="half">
  <a href="/assets/img/posts/vue_weather/11.jpg"><img src="/assets/img/posts/vue_weather/11.jpg"></a>
  <a href="/assets/img/posts/vue_weather/12.jpg"><img src="/assets/img/posts/vue_weather/12.jpg"></a>
	<figcaption>Vue Install</figcaption>
</figure>

- open index.js in router folder
- remove about url

* index.js
{% highlight js %}
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView,
    }
  ],
})

export default router
{% endhighlight %}

- delete `AboutView.vue` file in views folder

<figure class="half">
  <a href="/assets/img/posts/vue_weather/6.jpg"><img src="/assets/img/posts/vue_weather/6.jpg"></a>
  <a href="/assets/img/posts/vue_weather/7.jpg"><img src="/assets/img/posts/vue_weather/7.jpg"></a>
	<figcaption>Vue Install</figcaption>
</figure>

* HomeView.js
{% highlight vue %}
<script setup></script>

<template>
  <main></main>
</template>
{% endhighlight %}

* App.vue
{% highlight vue %}
<template>
  <div>
    <RouterView />
  </div>
</template>

<script setup>
import { RouterView } from "vue-router";
</script>

<style lang="scss" scoped></style>
{% endhighlight %}

# Tailwind Install
- open terminal and type `npm install -D tailwindcss postcss autoprefixer` to install tailwind

<figure>
  <a href="/assets/img/posts/vue_weather/8.jpg"><img src="/assets/img/posts/vue_weather/8.jpg"></a>
	<figcaption>Tailwind Install</figcaption>
</figure>

- type `npx tailwindcss init -p` to create `tailwind.config.js`file which is a config for tailwindcss

<figure class="half">
  <a href="/assets/img/posts/vue_weather/9.jpg"><img src="/assets/img/posts/vue_weather/9.jpg"></a>
  <a href="/assets/img/posts/vue_weather/10.jpg"><img src="/assets/img/posts/vue_weather/10.jpg"></a>
	<figcaption>Tailwind Install</figcaption>
</figure>

* tailwind.config.js
{% highlight js %}
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{vue,js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        "weather-primary": "#00668A",
        "weather-secondary": "#004E71"
      }
    },
    fontFamily: {
      Roboto: ["Roboto, sans-serif"]
    },
    container: {
      padding: "2rem",
      center: true
    },
    screens: {
      sm: "640px",
      md: "768px"
    }
  },
  plugins: []
};
{% endhighlight %}

- create `tailwind.css` in assets folder

* tailwind.css
{% highlight css %}
@tailwind base;
@tailwind components;
@tailwind utilities;
{% endhighlight %}

* main.js
{% highlight js %}
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import './assets/tailwind.css'

const app = createApp(App)

app.use(router)

app.mount('#app')
{% endhighlight %}

- get Roboto link in <a href="https://fonts.google.com/selection/embed">google font</a>
- get font-awesome link in <a href="https://cdnjs.com/libraries/font-awesome">cdnjs</a>

* index.html
{% highlight html %}
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="UTF-8">
    <link rel="icon" href="/favicon.ico">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css" integrity="sha512-Evv84Mr4kqVGRNSgIGL/F/aIDqQb7xQ2vcrdIwxfjThSH8CSR7PBEakCr51Ck+w+/U6swU2Im1vVX0SVk9ABhg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <title>Vue Weather</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>
{% endhighlight %}

# Navigation

## :to
- links with the url which is in the router

### Site Navigation
- create `SiteNavigation.vue` file in components folder

* SiteNavigation.vue
{% highlight vue %}
<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"></i>
      </div>
    </nav>
  </header>
</template>

<script setup>
import { RouterLink } from 'vue-router';
</script>
{% endhighlight %}

* App.vue
{% highlight vue %}
<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation/>
    <RouterView />
  </div>
</template>

<script setup>
import { RouterView } from "vue-router";
import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<style lang="scss" scoped></style>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/vue_weather/13.jpg"><img src="/assets/img/posts/vue_weather/13.jpg"></a>
	<figcaption>Navigation</figcaption>
</figure>
