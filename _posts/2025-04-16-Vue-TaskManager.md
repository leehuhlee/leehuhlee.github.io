---
layout: post
title: "[vue.js, pinia] Task Manager"
date: 2025-01-18
excerpt: "vue.js"
tags: [vue.js, pinia]
comments: false
---

# Install
- download <a href="https://github.com/Daweb-Schools/vue-composition-pinia-youtube">file</a>
- open terminal and type `npm install` to install required packages
- type `npm run dev` to run

<figure class="half">
  <a href="/assets/img/posts/vue_taskmanager/0.jpg"><img src="/assets/img/posts/vue_taskmanager/0.jpg"></a>
  <a href="/assets/img/posts/vue_taskmanager/1.jpg"><img src="/assets/img/posts/vue_taskmanager/1.jpg"></a>
	<figcaption>Install</figcaption>
</figure>

# Syntax

## Single-File Component
- When using a build step, we typically define each Vue component in a dedicated file using the `.vue` extension - known as a Single-File Component (SFC for short)

* SFC Example
{% highlight vue %}
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
{% endhighlight %}

- When not using a build step, a Vue component can be defined as a plain JavaScript object containing Vue-specific options

* JavaScript Example
{% highlight vue %}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`
  // Can also target an in-DOM template:
  // template: '#my-template-element'
}
{% endhighlight %}

## API Styles

### Options API
- With Options API, we define a component's logic using an object of options such as `data`, `methods`, and `mounted`. Properties defined by options are exposed on `this` inside functions, which points to the component instance

* Options API Example
{% highlight vue %}
<script>
export default {
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event handlers in templates.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
{% endhighlight %}

### Composition API

- With Composition API, we define a component's logic using imported API functions. 
- In SFCs, Composition API is typically used with `<script setup>`. The `setup` attribute is a hint that makes Vue perform compile-time transforms that allow us to use Composition API with less boilerplate.
- For example, imports and top-level variables / functions declared in `<script setup>` are directly usable in the template.

* Composition API Example
{% highlight vue %}
<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
{% endhighlight %}

## ref
- Takes an inner value and returns a reactive and mutable ref object, which has a single property `.value` that points to the inner value.

* App.vue
{% highlight vue %}
<script setup>
import { ref } from 'vue';

const appName = ref("My new task manager");

console.log(appName.value);

...
</script>

<template>

  <main class="container">
    <div class="header">
      <div class="header-side">
        <h1>
          {{ appName }}
        </h1>
      </div>
      <input type="text" v-model="appName">
    </div>
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/vue_taskmanager/2.jpg"><img src="/assets/img/posts/vue_taskmanager/2.jpg"></a>
  <a href="/assets/img/posts/vue_taskmanager/3.jpg"><img src="/assets/img/posts/vue_taskmanager/3.jpg"></a>
	<figcaption>Syntax</figcaption>
</figure>

