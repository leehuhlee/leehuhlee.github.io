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

### v-model
- can be used on a component to implement a two-way binding.
- Its `.value` is synced with the value bound by the parent `v-model`;
- When it is mutated by the child, it causes the parent bound value to be updated as well.

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

## reactive
- returns a reactive proxy of the object.

### v-for
- renders the element or template block multiple times based on the source data.

### @click
- calls binded event or function.

* App.vue
{% highlight vue %}
<script setup>
import { ref, reactive } from 'vue';

const appName = ref("My new task manager");

let tasks = reactive([
    {
      name: "Website design",
      description: "Define the style guide, branding and create the webdesign on Figma.",
      completed: true
    },
    {
      name: "Website development",
      description: "Develop the portfolio website using Vue JS.",
      completed: false
    },
    {
      name: "Hosting and infrastructure",
      description: "Define hosting, domain and infrastructure for the portfolio website.",
      completed: false
    },
    {
      name: "Composition API",
      description: "Learn how to use the composition API and how it compares to the options API.",
      completed: true
    },
    {
      name: "Pinia",
      description: "Learn how to setup a store using Pinia.",
      completed: true
    },
    {
      name: "Groceries",
      description: "Buy rice, apples and potatos.",
      completed: false
    },
    {
      name: "Bank account",
      description: "Open a bank account for my freelance business.",
      completed: false
    }
]);

let newTask = {completed: false};

function addTask(){
  if(newTask.name && newTask.description) {
    tasks.push(newTask);
    newTask = {completed: false};
  } else {
    alret("Please enter the title and description for the task.");
  }
}

</script>
...
    <div class="tasks">
      
      <div v-for="(task, index) in tasks" :key="index" class="task">
        <h3>
          {{ task.name }}
        </h3>
        <p>
          {{ task.description }}
        </p>
        <div class="task-check">
          <input type="checkbox" :checked="task.completed" />
          <label>
            Done
          </label>
        </div>
      </div>

      <!-- <div class="task">
        <h3>
          Website development
        </h3>
        <p>
          Develop the portfolio website using Vue JS.
        </p>
        <div class="task-check">
          <input type="checkbox"/>
          <label>
            To-Do
          </label>
        </div>
      </div> -->

    </div>

    <div class="add-task">
      <h3>Add a new task</h3>
      <input v-model="newTask.name" type="text" name="title" placeholder="Enter a title..."><br />
      <textarea v-model="newTask.description" name="description" rows="4" placeholder="Enter a description..." /><br />
      <button @click="addTask" class="btn gray">Add Task</button>

    </div>
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/vue_taskmanager/4.jpg"><img src="/assets/img/posts/vue_taskmanager/4.jpg"></a>
  <a href="/assets/img/posts/vue_taskmanager/5.jpg"><img src="/assets/img/posts/vue_taskmanager/5.jpg"></a>
	<figcaption>Syntax</figcaption>
</figure>

# components
- create `components` folder in src folder
- create `Task.vue` file in components folder

## props
- Vue components require explicit props declaration so that Vue knows what external props passed to the component should be treated as fallthrough attributes.

## scoped style
- When a `<style>` tag has the `scoped` attribute, its CSS will apply to elements of the current component only. 

* Task.vue
{% highlight vue %}
<script setup>

  const props = defineProps(['task']);

</script>

<template>
  <div class="task">
    <h3>
      {{ task.name }}
    </h3>
    <p>
      {{ task.description }}
    </p>
    <div class="task-check">
      <input type="checkbox" :checked="task.completed" />
      <label>
        Done
      </label>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.task {
  display: flex;
  flex-direction: column;
  background-color: var(--white-color);
  color: var(--black-color);
  padding: 20px;
  border-radius: 12px;
  position: relative;


  h3 {
    font-size: 20px;
    font-weight: 700;
    line-height: 21px;
    letter-spacing: 0em;
    text-align: left;
  }

  p {
    margin-top: 24px;
    margin-bottom: 12px;
    font-size: 16px;
    font-weight: 400;
    line-height: 16px;
    letter-spacing: 0em;
    text-align: left;
  }


  .task-check {
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    bottom: 10px;
    right: 10px;

    label {
      font-size: 13px;
      font-weight: 400;
      line-height: 16px;
      letter-spacing: 0em;
      text-align: left;
      margin-left: 5px;
      cursor: pointer;
    }

    input {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 18px;
      height: 18px;
      border-radius: 100%;
      border: 0.77px solid #AEAEB2;
      appearance: none;
      cursor: pointer;


      &:checked {
        background-color: #0A7AFF;
        border-color: #0A7AFF;

        &::before {
          content: '';
          display: block;
          width: 4.5px;
          height: 9px;
          border: solid white;
          border-width: 0 2px 2px 0;
          transform: rotate(45deg);
        }
      }
    }
  }
}
</style>

{% endhighlight %}

* App.vue
{% highlight vue %}
<script setup>
import { ref, reactive } from 'vue';
import Task from './components/Task.vue';

const appName = ref("My new task manager");

let tasks = reactive([
    {
      name: "Website design",
      description: "Define the style guide, branding and create the webdesign on Figma.",
      completed: true
    },
    {
      name: "Website development",
      description: "Develop the portfolio website using Vue JS.",
      completed: false
    },
    {
      name: "Hosting and infrastructure",
      description: "Define hosting, domain and infrastructure for the portfolio website.",
      completed: false
    },
    {
      name: "Composition API",
      description: "Learn how to use the composition API and how it compares to the options API.",
      completed: true
    },
    {
      name: "Pinia",
      description: "Learn how to setup a store using Pinia.",
      completed: true
    },
    {
      name: "Groceries",
      description: "Buy rice, apples and potatos.",
      completed: false
    },
    {
      name: "Bank account",
      description: "Open a bank account for my freelance business.",
      completed: false
    }
]);

let newTask = {completed: false};

function addTask(){
  if(newTask.name && newTask.description) {
    tasks.push(newTask);
    newTask = {completed: false};
  } else {
    alret("Please enter the title and description for the task.");
  }
}

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
    
    <div class="filters">
      <div>
        <p>Filter by state</p>
        <div class="badges">
          <div class="badge">
            To-Do
          </div>
          <div class="badge">
            Done
          </div>
          <span class="clear">
            x clear
          </span>
        </div>
      </div>
    </div>

    <div class="tasks">
      
      <Task v-for="(task, index) in tasks" :task="task" :key="index"/>

    </div>

    <div class="add-task">
      <h3>Add a new task</h3>
      <input v-model="newTask.name" type="text" name="title" placeholder="Enter a title..."><br />
      <textarea v-model="newTask.description" name="description" rows="4" placeholder="Enter a description..." /><br />
      <button @click="addTask" class="btn gray">Add Task</button>

    </div>

  </main>
  
</template>


<style lang="scss" scoped>

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  .header-side {
    display: flex;
    align-items: center;

    h1 {
      text-transform: capitalize;
      font-size: 42px;
      font-weight: 700;
      line-height: 47px;
      letter-spacing: 0em;
      text-align: left;
    }

    .secondary {
      margin-left: 12px;
    }
  }

}

.filters {
  display: flex;
  flex-direction: column;
  margin: 40px 0;

  p {
    font-size: 16px;
    font-weight: 400;
    line-height: 21px;
    letter-spacing: 0em;
    text-align: left;
  }

  .badges {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    margin: 14px 0;
    align-items: center;
  }

  .clear {
    font-size: 14px;
    font-weight: 400;
    line-height: 16px;
    letter-spacing: 0em;
    text-align: left;
    cursor: pointer;
  }

}

.tasks {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;

  @media (max-width: 768px) {
    grid-template-columns: repeat(1, 1fr);
  }
}

.add-task {
  margin-top: 60px;

  input, textarea {
    width: 360px;
    max-width: 100%;
    margin-top: 12px;
    padding: 5px;
  }

  button {
    width: 360px;
    margin-top: 12px;
  }
}

</style>
{% endhighlight %}
