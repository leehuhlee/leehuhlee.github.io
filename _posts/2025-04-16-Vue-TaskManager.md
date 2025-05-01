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

## emit
- declares the custom events emitted by the component.

* Task.vue
{% highlight vue %}
...
<div class="task-check">
  <input @click="$emit('toggleCompleted', task.id)" type="checkbox" :checked="task.completed" />
  <label>
    {{ task.completed ? 'Done' : 'To-Do' }}
  </label>
</div>
...
{% endhighlight %}

* App.vue
{% highlight vue %}
...
let tasks = reactive([
    {
      id: 1,
      name: "Website design",
      description: "Define the style guide, branding and create the webdesign on Figma.",
      completed: true
    },
    {
      id: 2,
      name: "Website development",
      description: "Develop the portfolio website using Vue JS.",
      completed: false
    },
    {
      id: 3,
      name: "Hosting and infrastructure",
      description: "Define hosting, domain and infrastructure for the portfolio website.",
      completed: false
    },
    {
      id: 4,
      name: "Composition API",
      description: "Learn how to use the composition API and how it compares to the options API.",
      completed: true
    },
    {
      id: 5,
      name: "Pinia",
      description: "Learn how to setup a store using Pinia.",
      completed: true
    },
    {
      id: 6,
      name: "Groceries",
      description: "Buy rice, apples and potatos.",
      completed: false
    },
    {
      id: 7,
      name: "Bank account",
      description: "Open a bank account for my freelance business.",
      completed: false
    }
]);

let newTask = {completed: false};

function addTask(){
  if(newTask.name && newTask.description) {
    newTask.id = Math.max(...tasks.map(task => task.id)) + 1;
    tasks.push(newTask);
    newTask = {completed: false};
  } else {
    alret("Please enter the title and description for the task.");
  }
}

function toggleCompleted(id) {
  tasks.forEach(task => {
    if(task.id === id) {
      task.completed = !task.completed;
    }
  });
}
...
<div class="tasks">
  <Task @toggleCompleted="toggleCompleted" v-for="(task, index) in tasks" :task="task" :key="index"/>
</div>
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/vue_taskmanager/6.jpg"><img src="/assets/img/posts/vue_taskmanager/6.jpg"></a>
	<figcaption>components</figcaption>
</figure>

# Filter
- create `Filter.vue` in components folder

## v-if
- conditionally renders an element or a template fragment based on the truthy-ness of the expression value.

* Filter.vue
{% highlight vue %}
<script setup>
const props = defineProps(['filterBy']);
</script>

<template>
  <div class="filters">
    <div>
      <p>Filter by state</p>
      <div class="badges">
        <div @click="$emit('setFilter', 'todo')" class="badge" :class="{selected : filterBy === 'todo'}">
          To-Do
        </div>
        <div @click="$emit('setFilter', 'done')" class="badge" :class="{selected : filterBy === 'done'}">
          Done
        </div>
        <span @click="$emit('setFilter', '')" v-if="filterBy" class="clear">
          x clear
        </span>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
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

    .badge {
      padding: 8px 7px;
      background-color: var(--gray-color);
      color: var(--black-color);
      font-size: 14px;
      font-weight: 400;
      line-height: 16px;
      letter-spacing: 0em;
      text-align: left;
      border-radius: 8px;
      cursor: pointer;
      user-select: none;
    
      &.selected {
        background-color: #E42C5F;
        color: var(--white-color);
      }
    }
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
</style>
{% endhighlight %}

* App.vue
{% highlight vue %}
<script setup>
import { ref, reactive } from 'vue';
import Task from './components/Task.vue';
import Filter from './components/Filter.vue';

const appName = ref("My new task manager");

let tasks = reactive([
    {
      id: 1,
      name: "Website design",
      description: "Define the style guide, branding and create the webdesign on Figma.",
      completed: true
    },
    {
      id: 2,
      name: "Website development",
      description: "Develop the portfolio website using Vue JS.",
      completed: false
    },
    {
      id: 3,
      name: "Hosting and infrastructure",
      description: "Define hosting, domain and infrastructure for the portfolio website.",
      completed: false
    },
    {
      id: 4,
      name: "Composition API",
      description: "Learn how to use the composition API and how it compares to the options API.",
      completed: true
    },
    {
      id: 5,
      name: "Pinia",
      description: "Learn how to setup a store using Pinia.",
      completed: true
    },
    {
      id: 6,
      name: "Groceries",
      description: "Buy rice, apples and potatos.",
      completed: false
    },
    {
      id: 7,
      name: "Bank account",
      description: "Open a bank account for my freelance business.",
      completed: false
    }
]);

let newTask = {completed: false};

let filterBy = ref("");

function addTask(){
  if(newTask.name && newTask.description) {
    newTask.id = Math.max(...tasks.map(task => task.id)) + 1;
    tasks.push(newTask);
    newTask = {completed: false};
  } else {
    alret("Please enter the title and description for the task.");
  }
}

function toggleCompleted(id) {
  tasks.forEach(task => {
    if(task.id === id) {
      task.completed = !task.completed;
    }
  });
}

function setFilter(value) {
  filterBy.value = value;
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
    
    <Filter :filterBy="filterBy" @setFilter="setFilter"/>

    <div class="tasks">
      <Task @toggleCompleted="toggleCompleted" v-for="(task, index) in tasks" :task="task" :key="index"/>
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

<figure>
  <a href="/assets/img/posts/vue_taskmanager/7.jpg"><img src="/assets/img/posts/vue_taskmanager/7.jpg"></a>
	<figcaption>components</figcaption>
</figure>

# computed
- computed properties are cached based on their reactive dependencies. 
- computed property will only re-evaluate when some of its reactive dependencies have changed. 


* App.vue
{% highlight vue %}
import { ref, reactive, computed } from 'vue';
...
const filteredTasks = computed(() => {
  switch(filterBy.value) {
    case 'todo':
      return tasks.filter(task => !task.completed);
    case 'done':
      return tasks.filter(task => task.completed)
    default:
      return tasks;
  }
})
...
    <div class="tasks">
      <Task @toggleCompleted="toggleCompleted" v-for="(task, index) in filteredTasks" :task="task" :key="index"/>
    </div>
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/vue_taskmanager/8.jpg"><img src="/assets/img/posts/vue_taskmanager/8.jpg"></a>
  <a href="/assets/img/posts/vue_taskmanager/9.jpg"><img src="/assets/img/posts/vue_taskmanager/9.jpg"></a>
  <a href="/assets/img/posts/vue_taskmanager/10.jpg"><img src="/assets/img/posts/vue_taskmanager/10.jpg"></a>
	<figcaption>computed</figcaption>
</figure>

# Modal
- create `ModalWindow.vue` and `ModalCloseButton.vue` in components folder

* ModalWindow.vue
{% highlight vue %}
<script setup>
import ModalCloseButton from './ModalCloseButton.vue';

</script>

<template>
  <div class="modal-wrapper" aria-modal="true"
      role="dialog" tabindex="-1">
      <div class="inner">
           <ModalCloseButton @click="$emit('closePopup')"/>
           <div class="form">
               Your Popup Content Goes Here
           </div>
      </div>
  </div>
</template>

<style lang="scss">
.modal-wrapper {
       position: fixed;
       left: 0;
       top: 0;
       z-index: 500;
       width: 100vw;
       height: 100vh;
       background: rgba(0, 0, 0, 0.2);
       display: grid;
       place-items: center;
       color: #000;
  
       .inner {
       background-color: white;
       padding: 30px;
       border-radius: 12px;
       display: flex;
       flex-direction: column;
       position: relative;
       max-width: 600px;
       width: 90%;
  
       h3 {
           font-size: 16px;
           font-weight: 700;
           line-height: 21px;
           margin-bottom: 20px;
       }
  
       .close-btn {
           position: absolute;
           top: 15px;
           right: 15px;
           cursor: pointer;
           background-color: #fff;
       }
  
       .form {
           display: flex;
           flex-direction: column;
           max-width: 100%;


           label {
           font-size: 12px;
           font-weight: 500;
           line-height: 16px;
           letter-spacing: 0em;
           text-align: left;
           }


           input,
           select,
           textarea {
           font-size: 12px;
           font-weight: 400;
           line-height: 16px;
           letter-spacing: 0em;
           text-align: left;
           border: 1px solid #C2C2C2;
           border-radius: 4px;
           padding: 8px 12px;
           margin-top: 5px;


           &::placeholder {
               color: #A6A6A6;
           }
           }
  
          
  
           .btn {
           width: fit-content;
           padding-inline: 23px;
           }
       }
       }
   }
</style>
{% endhighlight %}

* ModalCloseButton.vue
{% highlight vue %}
<template>
  <button class="close-btn">
    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 256 256">
    <path fill="currentColor"
      d="M208.49 191.51a12 12 0 0 1-17 17L128 145l-63.51 63.49a12 12 0 0 1-17-17L111 128L47.51 64.49a12 12 0 0 1 17-17L128 111l63.51-63.52a12 12 0 0 1 17 17L145 128Z" />
    </svg>
  </button>
</template>
{% endhighlight %}

* App.vue
{% highlight vue %}
...
let filterBy = ref("");
let modalIsActive = ref(false);
...
    </div>

    <ModalWindow @closePopup="modalIsActive=false" v-if="modalIsActive"/>
  </main>
{% endhighlight %}

## slot
- passes a template fragment to a child component, and let the child component render the fragment within its own template.
- create `modal` folder and move `ModalCloseButton.vue` and `ModalCloseButton.vue` in modal folder
- create `AddTaskModal.vue` in modal folter

* AddTaskModal.vue
{% highlight vue %}
<template>
  <div class="form">
    <h3>Add a new task</h3>
    <label for="title">Title *</label>
    <input type="text" name="title" placeholder="Enter a title..."><br />
    <label for="title">Description *</label>
    <textarea name="description" rows="4" placeholder="Enter a description..." /><br />
    <button class="btn gray">Add Task</button>
  </div>
</template>
{% endhighlight %}

* App.vue
{% highlight vue %}
...
    <div class="tasks">
      <Task @toggleCompleted="toggleCompleted" v-for="(task, index) in filteredTasks" :task="task" :key="index" />
    </div>

    <ModalWindow @closePopup="modalIsActive = false" v-if="modalIsActive">
      <AddTaskModal/>
    </ModalWindow>
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/vue_taskmanager/11.jpg"><img src="/assets/img/posts/vue_taskmanager/11.jpg"></a>
	<figcaption>Modal</figcaption>
</figure>

# State
- is the source of truth of the application.
- is changed by props, set and so on.

## State Management
- there is a store to save state.
- with state management, parent and child don't need to communicate each other.
- they can just get state from the store.

<figure class="half">
  <a href="/assets/img/posts/vue_taskmanager/12.jpg"><img src="/assets/img/posts/vue_taskmanager/12.jpg"></a>
  <a href="/assets/img/posts/vue_taskmanager/13.jpg"><img src="/assets/img/posts/vue_taskmanager/13.jpg"></a>
	<figcaption>State</figcaption>
</figure>

### Pinia
- <a href="https://pinia.vuejs.org/">Pinia</a> is a single source of truth.
- type `npm i pinia` in terminal to download Pinia.

<figure>
  <a href="/assets/img/posts/vue_taskmanager/14.jpg"><img src="/assets/img/posts/vue_taskmanager/14.jpg"></a>
	<figcaption>State</figcaption>
</figure>

# taskStore
- create `stores` folder and create `tasksStore.js` file in stores folder

* taskStore.js
{% highlight js %}
import { defineStore } from "pinia";
import { reactive, ref, computed } from "vue";

export const useTasksStore = defineStore('tasks', () => {
  let tasks = reactive([
    {
      id: 1,
      name: "Website design",
      description: "Define the style guide, branding and create the webdesign on Figma.",
      completed: true
    },
    {
      id: 2,
      name: "Website development",
      description: "Develop the portfolio website using Vue JS.",
      completed: false
    },
    {
      id: 3,
      name: "Hosting and infrastructure",
      description: "Define hosting, domain and infrastructure for the portfolio website.",
      completed: false
    },
    {
      id: 4,
      name: "Composition API",
      description: "Learn how to use the composition API and how it compares to the options API.",
      completed: true
    },
    {
      id: 5,
      name: "Pinia",
      description: "Learn how to setup a store using Pinia.",
      completed: true
    },
    {
      id: 6,
      name: "Groceries",
      description: "Buy rice, apples and potatos.",
      completed: false
    },
    {
      id: 7,
      name: "Bank account",
      description: "Open a bank account for my freelance business.",
      completed: false
    }
  ]);

  let filterBy = ref("");
  let modalIsActive = ref(false);

  const filteredTasks = computed(() => {
    switch (filterBy.value) {
      case 'todo':
        return tasks.filter(task => !task.completed);
      case 'done':
        return tasks.filter(task => task.completed)
      default:
        return tasks;
    }
  })

  function setFilter(value) {
    filterBy.value = value;
  }

  function addTask(newTask) {
    if (newTask.name && newTask.description) {
      newTask.id = Math.max(...tasks.map(task => task.id)) + 1;
      tasks.push(newTask);
      closeModal();
    } else {
      alret("Please enter the title and description for the task.");
    }
  }
  
  function toggleCompleted(id) {
    tasks.forEach(task => {
      if (task.id === id) {
        task.completed = !task.completed;
      }
    });
  }

  function openModal() {
    modalIsActive.value = true;
  }

  function closeModal() {
    modalIsActive.value = false;
  }

  return { tasks, filterBy, modalIsActive, filteredTasks, setFilter, addTask, toggleCompleted, openModal, closeModal }
});
{% endhighlight %}

* App.vue
{% highlight vue %}
...
const appName = ref("My new task manager");
const store = useTasksStore();
</script>

<template>
  <main class="container">
    <div class="header">
      <div class="header-side">
        <h1>
          {{ appName }}
        </h1>
      </div>
      <div class="header-side">
        <button @click="store.openModal()" class="btn secondary">+ Add Task</button>
      </div>
      <input type="text" v-model="appName">
    </div>

    <Filter/>

    <div class="tasks">
      <Task v-for="(task, index) in store.filteredTasks" :task="task" :key="index" />
    </div>

    <ModalWindow @closePopup="store.closeModal()" v-if="store.modalIsActive">
      <AddTaskModal/>
    </ModalWindow>
  </main>
</template>
...
{% endhighlight %}

* Task.vue
{% highlight vue %}
<script setup>
import { useTasksStore } from '../stores/tasksStore';

const props = defineProps(['task']);
const store = useTasksStore();
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
      <input @click="store.toggleCompleted(task.id)" type="checkbox" :checked="task.completed" />
      <label>
        {{ task.completed ? 'Done' : 'To-Do' }}
      </label>
    </div>
  </div>
</template>
...
{% endhighlight %}

* Filter.vue
{% highlight vue %}
<script setup>
import { useTasksStore } from '../stores/tasksStore';

const props = defineProps(['filterBy']);
const store = useTasksStore();
</script>

<template>
  <div class="filters">
    <div>
      <p>Filter by state</p>
      <div class="badges">
        <div @click="store.setFilter('todo')" class="badge" :class="{ selected: store.filterBy === 'todo' }">
          To-Do
        </div>
        <div @click="store.setFilter('done')" class="badge" :class="{ selected: store.filterBy === 'done' }">
          Done
        </div>
        <span @click="store.setFilter('')" v-if="store.filterBy" class="clear">
          x clear
        </span>
      </div>
    </div>
  </div>
</template>
...
{% endhighlight %}

* ModalWindow.vue
{% highlight vue %}
<script setup>
import { useTasksStore } from '../../stores/tasksStore';
import ModalCloseButton from './ModalCloseButton.vue';

const store = useTasksStore();
</script>

<template>
    <div class="modal-wrapper" aria-modal="true" role="dialog" tabindex="-1">
        <div class="inner">
            <ModalCloseButton @click="store.closeModal()" />
            <slot></slot>
        </div>
    </div>
</template>
...
{% endhighlight %}

* AddTaskModal.vue
{% highlight vue %}
<script setup>
import { useTasksStore } from '../../stores/tasksStore';

const store = useTasksStore();
let newTask = { completed: false };
</script>

<template>
  <div class="form">
    <h3>Add a new task</h3>
    <label for="title">Title *</label>
    <input v-model="newTask.name" type="text" name="title" placeholder="Enter a title..."><br />
    <label for="title">Description *</label>
    <textarea v-model="newTask.description" name="description" rows="4" placeholder="Enter a description..." /><br />
    <button @click="store.addTask(newTask)" class="btn gray">Add Task</button>
  </div>
</template>
...
{% endhighlight %}

# Local Storage
- You can watch the state and its changes through the $subscribe() method of a store, similar to Vuex's subscribe method. 
- The advantage of using $subscribe() over a regular watch() is that subscriptions will trigger only once after patches (e.g. when using the function version from above).

* tasksStore.js
{% highlight js %}
...
export const useTasksStore = defineStore('tasks', () => {
  let tasks = reactive(JSON.parse(localStorage.getItem('tasks')) || []);
...
{% endhighlight %}

* App.vue
{% highlight vue %}
...
const store = useTasksStore();

store.$subscribe((state) => {
  localStorage.setItem('tasks', JSON.stringify(state.tasks));
});
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/vue_taskmanager/15.jpg"><img src="/assets/img/posts/vue_taskmanager/15.jpg"></a>
	<figcaption>Local Storage</figcaption>
</figure>

[Download](https://github.com/leehuhlee/vue-taskmanager){: .btn}
