---
layout: post
title: "[React] Emotianal Diary"
date: 2022-03-26
excerpt: "Javascript Node.js React"
tags: [Javascript, Node, React]
comments: false
---

# Demo
  - <a href="https://leehuhlee-emotional-diary.web.app/">Emotion Diary</a>
  - with Javascript, React and VS Code.

<iframe width="560" height="315" src="/assets/video/posts/react_emotional_diary/React-Emotion-Diary.mp4" frameborder="0"> </iframe>

# Javascript
  - CodeSandbox
    - https://codesandbox.io/
    - You can easily practice Javascript in Web with CodeSandbox.
    - Without account, Click Create `Sandbox`. Then you can create your project.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/1.jpg"><img src="/assets/img/posts/react_emotional_diary/1.jpg"></a>
  <figcaption>Javascript CodeSandbox</figcaption>
</figure>

## let vs var
  - If you want to declare variable one time and let to do not use these name for vatiable, you should use `let`.

{% highlight js %}
let num = 0;
num = 1;
let num = 2; // error!

var num = 0;
num = 1;
var num = 3; // OK!
{% endhighlight %}

## CallBack
  - When you want give function as an argument, you can use `CallBack`.
  - It makes your code more flexible.

{% highlight js %}
function checkMood(mood, goodCallBack, badCallBack){
  if (mood === "good"){
    goodCallBack();
  }
  else{
    badCallBack();
  }
}

function cry(){
  console.log("ACTION :: CRY");
}

function sing(){
  console.log("ACTION :: SING");
}

function dance(){
  console.log("ACTION :: Dance");
}

checkMood("sad", sing, dance)
{% endhighlight %}

## Function Presentation
  - You can create function like a variable.

{% highlight js %}
let hello = function() {
  return "hello world!";
}

// let hello = () => "hello world!";
{% endhighlight %}

## Object

{% highlight js %}
let person = {
  name : "Hanna",
  age : 25
  say : () => console.log(`Hello ${person.name}`);
};

person.say();

console.log(person["name"]);
console.log(person.age);

person.location = "Germany";
person["gender"] = "Female";

console.log(person["location"]);
console.log(person.gender);

delete person.age;
person.name = null;

console.log(person);

console.log(`name : ${"name" in persion}`);
console.log(`name : ${"parent" in persion}`);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/2.jpg"><img src="/assets/img/posts/react_emotional_diary/2.jpg"></a>
  <figcaption>Javascript Object</figcaption>
</figure>

## Loop
  - You can use `Object` more flexible with `Loop`

{% highlight js %}
let person = {
  name: "Hanna",
  age: 25,
  tall: 175
};

const personKeys = Object.keys(person);

for (let i = 0; i < personKeys.length; i++) {
const curKey = personKeys[i];
const curValue = person[curKey];

console.log(`${curKey} : ${curValue}`);
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/3.jpg"><img src="/assets/img/posts/react_emotional_diary/3.jpg"></a>
  <figcaption>Javascript Loop</figcaption>
</figure>

## Truthy and Falshy
  - True : Object, not empty string, number, array
  - False : undefined, null, empty string

### && and ||
  - `&&` : If left value is falshy, then return left value. If left value is turhy, then return right value.
  - `||` : If left value is falshy, then return right value. If left value is turhy, then return left value.

{% highlight js %}
const getName = (person) => {
const name = person && person.name;
return name || "not object";
};

let person;
const name = getName(person);
console.log(name);
{% endhighlight %}

## Destructing Assignment
  - You can break your declaration rule in javascript!

{% highlight js %}
let a = 10;
let b = 5;

[a, b] = [b, a];
console.log(a, b);

let object = { one: "one", two: "two", three: "three", name: "Hanna" };
let { name: myName, one: oneOne, two, three, abc = "four" } = object;
console.log(oneOne, two, three, myName, abc);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/4.jpg"><img src="/assets/img/posts/react_emotional_diary/4.jpg"></a>
  <figcaption>Javascript Destructing Assignment</figcaption>
</figure>

## Spread

{% highlight js %}
const cookie = {
  base: "cookie",
  madeIn: "korea"
};

const chocochipCookie = {
  ...cookie,
  toping: "chocochip"
};

const blueberryCookie = {
  ...cookie,
  toping: "blueberry"
};

const strawberryCookie = {
  ...cookie,
  toping: "strawberry"
};

console.log(chocochipCookie);

const noTopingCookies = ["Classic Cookie", "Base Cookie"];
const topingCookies = [
  "Banana Cookie",
  "Blueberry Cookie",
  "Strawberry Cookie",
  "Chopcochip Cookie"
];

const allCookies = [...noTopingCookies, ...topingCookies];
console.log(allCookies);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/5.jpg"><img src="/assets/img/posts/react_emotional_diary/5.jpg"></a>
  <figcaption>Javascript Spread</figcaption>
</figure>

## Syncronize and Asyncronize
  - Javascript has only single thread.
  - But javascript can run various functions at once.

{% highlight js %}
function taskA(a, b, cb) {
  setTimeout(() => {
    const res = a + b;
    cb(res);
  }, 3000);
}

function taskB(a, cb) {
  setTimeout(() => {
    const res = a \* 2;
    cb(res);
  }, 1000);
}

function taskC(a, cb) {
  setTimeout(() => {
    const res = a \* -1;
    cb(res);
  }, 2000);
}

taskA(3, 4, (a_res) => {
  console.log("A Task RESULT : ", a_res);
  taskB(a_res, (b_res) => {
    console.log("B Task Result : ", b_res);
    taskC(b_res, (c_res) => {
      console.log("C Task Result : ", c_res);
    });
  });
});

console.log("CODE END");
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/6.jpg"><img src="/assets/img/posts/react_emotional_diary/6.jpg"></a>
  <figcaption>Javascript Asyncronization</figcaption>
</figure>

## Promise
  - You can use CallBack in Asyncronize with Promise.
  - You can use `then` and `catch` for result.

{% highlight js %}
function isPositive(number) {
  const executor = (resolve, reject) => {
  setTimeout(() => {
    if (typeof number === "number") {
    console.log(number);
    resolve(number >= 0 ? "+" : "-");
    } else {
    reject("This is not a number");
    }
  }, 2000);
};

const asyncTask = new Promise(executor);
  return asyncTask;
}

const res = isPositive(101));

res
  .then((res) => {
    console.log("Done : ", res);
  })
  .catch((err) => {
    console.log("Error : ", err);
});
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/7.jpg"><img src="/assets/img/posts/react_emotional_diary/7.jpg"></a>
  <figcaption>Javascript Promise</figcaption>
</figure>

{% highlight js %}
function taskA(a, b) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
    const res = a + b;
    resolve(res);
    }, 3000);
  });
}

function taskB(a) {
  return new Promise((resolve, reject) => {
  setTimeout(() => {
    const res = a \* 2;
    resolve(res);
    }, 1000);
  });
}

function taskC(a) {
  return new Promise((resolve, reject) => {
  setTimeout(() => {
    const res = a \* -1;
    resolve(res);
    }, 2000);
  });
}

const bPromiseResult = taskA(5, 1).then((a_res) => {
  console.log("A RESULT : ", a_res);
  return taskB(a_res);
});

console.log("Break Time!!!");

bPromiseResult
  .then((b_res) => {
    console.log("B RESULT : ", b_res);
    return taskC(b_res);
  })
  .then((c_res) => {
    console.log("C RESUlT : ", c_res);
});
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/8.jpg"><img src="/assets/img/posts/react_emotional_diary/8.jpg"></a>
  <figcaption>Javascript Promise</figcaption>
</figure>

## Async and Await
  - `Async` set a function to asyncronize.
  - `Await` process a function like syncronize. So, before await line is finished, never other code is ran.
  - async function return always `Promise`.

{% highlight js %}
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function helloAsync() {
  await delay(3000);
  return "hello Async";
}

async function main() {
  console.log(helloAsync());

  const res = await helloAsync();
  console.log(res);
}

main();
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/9.jpg"><img src="/assets/img/posts/react_emotional_diary/9.jpg"></a>
  <figcaption>Javascript Async and Await</figcaption>
</figure>

## API
  - Get data with specific url.
  - `fetch` call the url but it raw data. So you should change it to json.

### JSONPlaceHolder
  - https://jsonplaceholder.typicode.com/
  - You can use JSONPlaceHolder website to practice.
<figure>
  <a href="/assets/img/posts/react_emotional_diary/10.jpg"><img src="/assets/img/posts/react_emotional_diary/10.jpg"></a>
  <figcaption>Javascript Async and Await</figcaption>
</figure>

{% highlight js %}
async function getDate() {
  let rawResponse = await fetch("https://jsonplaceholder.typicode.com/posts");
  let jsonResponse = await rawResponse.json();
  console.log(jsonResponse);
}

getDate();
{% endhighlight %}

# VS Code

## Prettier
  - Set indentation automatically in VS Code.

<figure class="half">
  <a href="/assets/img/posts/react_emotional_diary/11.jpg"><img src="/assets/img/posts/react_emotional_diary/11.jpg"></a>
  <a href="/assets/img/posts/react_emotional_diary/12.jpg"><img src="/assets/img/posts/react_emotional_diary/12.jpg"></a>
  <figcaption>Javascript VS Code</figcaption>
</figure>

## Terminal
  - You can open the `Terminal` in VS Code with `Ctrl` + `J`.
  - You can run javascript file with `node`.

## module.exports
  - When you want to use result of the js file in other js file, you can use `module.exports` and `require`
  
* calc.js
{% highlight js %}
const add = (a, b) => a + b;
const sub = (a, b) => a - b;

module.exports = {
  moduleName : "calc module",
  add: add,
  sub: sub,
};
{% endhighlight %}

* index.js
{% highlight js %}
const calc = require("./calc");

console.log(calc);
console.log(calc.add(1,2));
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/13.jpg"><img src="/assets/img/posts/react_emotional_diary/13.jpg"></a>
  <figcaption>Javascript Terminal</figcaption>
</figure>

# Node.js

## Create Package
  - You can create the package with `npm init` and run javascript with `npm start`.
{% highlight json %}
{
  "name": "package-example1",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "leehuhlee",
  "license": "ISC"
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/14.jpg"><img src="/assets/img/posts/react_emotional_diary/14.jpg"></a>
  <figcaption>Javascript Node.js</figcaption>
</figure>

## Other's Package
  - npmjs : https://www.npmjs.com/
  - Website for npm packages.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/15.jpg"><img src="/assets/img/posts/react_emotional_diary/15.jpg"></a>
  <figcaption>Javascript Node.js</figcaption>
</figure>

### Download Package
  - You can download a package with `npm i` or `npm install`

<figure>
  <a href="/assets/img/posts/react_emotional_diary/16.jpg"><img src="/assets/img/posts/react_emotional_diary/16.jpg"></a>
  <figcaption>Javascript Node.js</figcaption>
</figure>

### Use Package
  - You can use a package with modulename.

{% highlight js %}
const randomColor = require('randomcolor'); // import the script
let color1 = randomColor();
let color2 = randomColor();
let color3 = randomColor();

console.log(color1, color2, color3);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/17.jpg"><img src="/assets/img/posts/react_emotional_diary/17.jpg"></a>
  <figcaption>Javascript Node.js</figcaption>
</figure>

# React
  - Javascript UI Library.
  - Use for code diet and virtual DOM.

## React Application
  - You can create a react application with `npx create-react-app {app name}` in terminal.
  - Run the application with `npm start`.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/18.jpg"><img src="/assets/img/posts/react_emotional_diary/18.jpg"></a>
  <figcaption>Javascript React</figcaption>
</figure>

## export and import
  - In React, you can use a js file in other js file with `export` and `import`.

* MyHeader.js
{% highlight js %}
const MyHeader = () => {
    return <header>Header</header>;
};

export default MyHeader;
{% endhighlight %}

* App.js
{% highlight js %}
import MyFooter from './MyFooter';
...

return (
    ...
        <MyHeader/>
        ...
  );
{% endhighlight %}

## React.Fragment
  - In React, html code should be closed with a root element like `div`, `React.Fragment` or `<>`.

* App.js
{% highlight js %}
return (
    <React.Fragment>
      ...
    </React.Fragment>
  );
{% endhighlight %}

## className
  - You cannot use `class` in js code because `class` is reserved word already in html.
  - Instead, you can use `className`.

* App.js
{% highlight js %}
return (
    ...
      <div className="bold">
      ...
  );
{% endhighlight %}

## Inline Style 
  - You can use style in js like a variable.

* App.js
{% highlight js %}
...

  const style ={
    color_text : {
      color : "green",
    }
  }

return (
    ...
      <h2 style={style.color_text}>Hello React</h2>
      ...
  );
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/react_emotional_diary/19.jpg"><img src="/assets/img/posts/react_emotional_diary/19.jpg"></a>
  <figcaption>Javascript React</figcaption>
</figure>

## State
  - `State` can change the state of page.
  - With `useState`, you can set a value on a first value of array and set function to change first value's state on a second value of array.
  - So first value of array is a variable and second value of array is a function.
  - You can call the function which change the state with `{}`.

* Counter.js
{% highlight js %}
import React, { useState } from 'react';

const Counter = () =>{

    const [count, setCount] = useState(0)

    const onIncrease = () =>{
        setCount(count + 1);
    };

    const onDecrease = () =>{
        setCount(count - 1);
    }

    return (
        <div>
            <h2>{count}</h2>
            <button onClick={onIncrease}>+</button>
            <button onClick={onDecrease}>-</button>
        </div>
    );
};

export default Counter;
{% endhighlight %}

* App.js
{% highlight js %}
import './App.css';
import MyHeader from './MyHeader';
import React from "react";
import Counter from './Counter';

function App() {

  return (
    <React.Fragment>
      <MyHeader/>
      <Counter/>      
    </React.Fragment>
  );
};

export default App;
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/react_emotional_diary/React-State.mp4" frameborder="0"> </iframe>

## Props
  - `Props` means property.
  - When you want to pass some values to the component, you can use `Props`.

* App.js
{% highlight js %}
import './App.css';
import MyHeader from './MyHeader';
import React from "react";
import Counter from './Counter';
import Container from './Container';

function App() {

  const counterProps = {
    a: 1,
    b: 2,
    c: 3,
    d: 4,
    e: 5,
    initialValue: 5
  };

  return (
    <Container>
      <div>
        <MyHeader/>
        <Counter {...counterProps}/>      
      </div>
    </Container>
  );
}

export default App;
{% endhighlight %}

* Container.js
{% highlight js %}
const Container = ({ children }) => {
    return(
        <div style={{ margin: 20, padding: 20, border: "1px solid gray" }}>
            {children}
        </div>
    );
};

export default Container;
{% endhighlight %}

* Counter.js
{% highlight js %}
import React, { useState } from 'react';
import OddEvenResult from './OddEvenResult';

const Counter = ({initialValue}) =>{

    const [count, setCount] = useState(initialValue);

    const onIncrease = () =>{
        setCount(count + 1);
    };

    const onDecrease = () =>{
        setCount(count - 1);
    }

    return (
        <div>
            <h2>{count}</h2>
            <button onClick={onIncrease}>+</button>
            <button onClick={onDecrease}>-</button>
            <OddEvenResult count={count}/>
        </div>
    );
};

Counter.defaultProps = {
    initialValue: 0
};

export default Counter;
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/react_emotional_diary/React-Props.mp4" frameborder="0"> </iframe>

## React Developer tools
  - Eneter Chrome tools and install React Developer tools in your chrome.
  - In react pages, you can see component process, context and etc.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/20.jpg"><img src="/assets/img/posts/react_emotional_diary/20.jpg"></a>
  <figcaption>Javascript React</figcaption>
</figure>

# Simple Diary

## CRUD

### useRef
  - You can set a html element as a variable or create a dynamic variable in javascript with `useRef`.
  - With the value in `()`, you can initialize the reference variable.

### focus
  - If you have a reference of the element, you can set mouse focusing to the element with `current.focus()`.

### Date
  - `new Date()` create Date object and `getTime()` create the time to milisecond.
  - `toLocaleString()` change the milisecond to local time.

### e.target
  - You can get element value and name with `e.target.value` and `e.target.name`.

### onChange
  - `onChange` set the function to change value of the html element.

### Key
  - When you want to pass an array to the component, you should set `Key` to specific array items.
  - In Normal, just use index of array items.

## Optimization

### React.createContext
  - In a html, context is child elements of a parent element.
  - `React.createContext` create context as a variable.
  - `Provider` can envelop elements in html.
  - With `useContext`, you can use Prop value in other js files.

### useReducer
  - `useReducer` can replace `useState` and with reduce code lines with switch-case statement.
  - In left, it is always array. First value is a variable and second value is a function to change value state.
  - In right, there is always `useReducer` function. In function parameters, first parameter is a function with switch-case statement to return the changed variable and second parameter is a initial value of the variable.

### useEffect
  - In React, there is 3 part of life cycle: Mount, Update, Unmount. 
  - `useEffect` set life cycle of the function.
  - To use mount time, you should add empty array for second argument.
  - To use update time, you should not use second argument.
  - To use unmount time, you should return destructing function on mount.
  
### useCallback
  - `useCallback` return memoized component to reuse.
  - You can set life time like an `useEffect`.

### React.memo
  - `React.memo` return the memoized variable.
  - This is for skip rerendering when inner elements is not updated.

### useMemo
  - `useMemo` skips to render when components has same value with previous render.
  - You can set life time like an `useEffect`.

## Code

* App.js
{% highlight js %}
import React, { useMemo, useEffect, useRef, useCallback, useReducer } from 'react';
import './App.css';
import DiaryEditor from './DiaryEditor';
import DiaryList from './DiaryList';
import LifeCycle from './LifeCycle';

const reducer = (state, action) => {
  switch(action.type){
    case "INIT": {
      return action.data;
    }
    case "CREATE": {
      const created_date = new Date().getTime();
      const newItem = {
        ...action.data,
        created_date
      };
      return [newItem, ...state];
    }
    case "DELETE": {
      return state.filter((it) => it.id !== action.targetId);
    }
    case "UPDATE": {
      return state.map((it) => 
        it.id === action.targetId ? 
        {...it, content: action.newContent} : it);
    }
    default: 
      return state;
  }
}

export const DiaryStateContext = React.createContext();
export const DiaryDispatchContext = React.createContext();

const App = () => {
  const [data, dispatch] = useReducer(reducer, []);

  const dataId = useRef(0);

  const getData = async() => {
    const res = await fetch(
      'https://jsonplaceholder.typicode.com/comments'
      ).then((res) => res.json());
      
      const initData = res.slice(0, 20).map((it) => {
        return {
          author: it.email,
          content : it.body,
          emotion : Math.floor(Math.random() * 5 ) + 1,
          created_date : new Date().getTime(),
          id : dataId.current++
        };
      });

      dispatch({type: "INIT", data: initData});
  };

  useEffect(() => {
    getData();
  }, []);

  const onCreate = useCallback((
    author, content, emotion) => {
      dispatch({type: "CREATE", data: {author, content, emotion, id:dataId.current++}});
    }, []);

  const onDelete = useCallback((targetId) => {
    dispatch({type: "DELETE", targetId})
  }, []);

  const onUpdate = useCallback((targetId, newContent) => {
    dispatch({type: "UPDATE", targetId, newContent});
  }, []);

  const memoizedDispatches = useMemo(() => {
    return {onCreate, onDelete, onUpdate};
  }, []);

  const getDiaryAnalysis = useMemo(
    () => {
      const goodCount = data.filter((it) => it.emotion >= 3).length;
      const badCount = data.length - goodCount;
      const goodRatio = (goodCount / data.length) * 100;
      return {goodCount, badCount, goodRatio};
    }, [data.length]
  );

  const {goodCount, badCount, goodRatio} = getDiaryAnalysis;

  return (
    <DiaryStateContext.Provider value={data}>
      <DiaryDispatchContext.Provider value={memoizedDispatches}>
        <div className="App">
          <LifeCycle/>
          <DiaryEditor/>
          <div>Total Diary : {data.length}</div>
          <div>Good Emotion Diary Count : {goodCount}</div>
          <div>Bad Emotion Diary Count : {badCount}</div>
          <div>Good Emotion Diary Ratio : {goodRatio}</div>
          <DiaryList/>
        </div>
      </DiaryDispatchContext.Provider>
    </DiaryStateContext.Provider>
  );
}

export default App;
{% endhighlight %}

* DiaryEditor.js
{% highlight js %}
import React, { useContext, useRef, useState} from "react";
import { DiaryDispatchContext } from "./App";

const DiaryEditor = () => {
    const {onCreate} = useContext(DiaryDispatchContext);
    const authorInput = useRef();
    const contentInput = useRef();

    const [state, setState] = useState({
        author: "",
        content: "",
        emotion: 1
    });

    const handleChangeState = (e) => {
        setState({
            ...state,
            [e.target.name]: e.target.value,
        });
    };

    const handleSubmit = () => {
        if(state.author.length < 1){
            authorInput.current.focus();
            return;
        }

        if(state.content.length < 5){
            contentInput.current.focus();
            return;
        }

        onCreate(state.author, state.content, state.emotion);
        setState({
            author: "",
            content: "",
            emotion: 1
        })
    };

    return(
     <div className="DiaryEditor">
         <h2>Today's Diary</h2>
         <div>
             <input 
             ref={authorInput}
             name="author"
             value={state.author} 
             onChange={handleChangeState}/>
         </div>
         <div>
             <textarea 
             ref={contentInput}
             name="content"
             value={state.content} 
             onChange={handleChangeState}/>
         </div>
        <div>
            <span>Today's emotion point : </span>
            <select 
            name="emotion" 
            value={state.emotion} 
            onChange={handleChangeState}>
                <option value={1}>1</option>
                <option value={2}>2</option>
                <option value={3}>3</option>
                <option value={4}>4</option>
                <option value={5}>5</option>
            </select>
        </div>
        <div>
            <button onClick={handleSubmit}>Save</button>
        </div>
     </div>
    );
};

export default React.memo(DiaryEditor);
{% endhighlight %}

* DiaryList.js
{% highlight js %}
import { useContext } from "react";
import { DiaryStateContext } from "./App.js";
import DiaryItem from "./DiaryItem.js";

const DiaryList = () => {
    const diaryList = useContext(DiaryStateContext);

    return (
    <div className="DiaryList">
        <h2>Diary List</h2>
        <h4>There are {diaryList.length} diaries.</h4>
        <div>
            {diaryList.map((it) => (
                <DiaryItem key={it.id} {...it}/>
            ))}
        </div>
    </div>
    );
};

DiaryList.defaultProps={
    diaryList:[],
};

export default DiaryList;
{% endhighlight %}

* DiaryItem.js
{% highlight js %}
import React, { useContext, useRef, useState } from "react";
import { DiaryDispatchContext } from "./App";

const DiaryItem = ({
    id, 
    author, 
    content, 
    emotion, 
    created_date
}) => {
    const {onDelete, onUpdate} = useContext(DiaryDispatchContext);

    const [isEdit, SetIsEdit] = useState(false);
    const toggleIsEdit = () => SetIsEdit(!isEdit);

    const [localContent, setLocalContent] = useState(content);
    const localContentInput = useRef();

    const handleRemove = () => {
        if(window.confirm(`Do you want to delete ${id}th diary?`)){
            onDelete(id);
        };
    };

    const handleCancel = () =>{
        SetIsEdit(false);
        setLocalContent(content);
    }

    const handleUpdate = () => {
        if(localContent.length < 5){
            localContentInput.current.focus();
            return;
        }

        if(window.confirm(`Do you want to update ${id}th diary?`)){
            onUpdate(id, localContent);
            toggleIsEdit();
        }
    };

    return (
    <div className="DiaryItem">
        <div className="info">
            <span>
                Author : {author} | Emotion Point : {emotion}
            </span>
            <br />
            <span className="date">{new Date(created_date).toLocaleString()}</span>
        </div>
        <div className="content">
            {isEdit ? (
            <>
                <textarea 
                ref={localContentInput}
                value={localContent} 
                onChange={(e) => setLocalContent(e.target.value)}
                />
            </>
            ) : (
            <>{content}</>
            )}
        </div>

        {isEdit ? (
        <>
            <button onClick={handleCancel}>Cancel</button>
            <button onClick={handleUpdate}>Save</button>
        </>
        ) : (
        <>
            <button onClick={handleRemove}>Delete</button>
            <button onClick={toggleIsEdit}>Update</button>
        </>
        )}
    </div>
    );
};

export default React.memo(DiaryItem);
{% endhighlight %}

* LifeCycle.js
{% highlight js %}
import React, {useEffect, useState} from 'react';

const UnmountTest = () => {
    useEffect(() => {
        console.log("Mount!");

        return () => {
            console.log("Unmount!");
        }
    }, []);

    return (
        <div>
            Unmount Testing Component
        </div>
    );
};

const LifeCycle = () => {
    const [isVisible, setVisible] = useState(false);
    const toggle = () => setVisible(!isVisible);

    return (
        <div style={{ padding: 20}}>
            <button onClick={toggle}>ON/OFF</button>
            {isVisible && <UnmountTest/>}
        </div>
    );
};

export default LifeCycle;
{% endhighlight %}

* App.css
{% highlight css %}
.DiaryEditor{
  border: 1px solid gray;
  text-align: center;
  padding: 20px;
}

.DiaryEditor input, textarea{
  margin-bottom: 20px;
  width: 500px;
  padding: 10px;
}

.DiaryEditor textarea{
  height: 150px;
}

.DiaryEditor select{
  width: 300px;
  padding: 10px;
  margin-bottom: 20px;
}

.DiaryEditor button{
  width: 500px;
  padding: 10px;
  cursor: pointer;
}

/* List */

.DiaryList{
  border: 1px solid gray;
  padding: 20px;
  margin-top: 20px;
}

.DiaryList h2{
  text-align: center;
}

/* Item */

.DiaryItem {
  background-color: rgb(240, 240, 240);
  margin-bottom: 10px;
  padding: 20px;
}

.DiaryItem .info{
  border-bottom: 1px solid gray;
  padding-bottom: 10px;
  margin-bottom: 10px;
}

.DiaryItem .date{
  color: gray;
}

.DiaryItem .content{
  font-weight: bold;
  margin-bottom: 30px;
  margin-top: 30px;
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/react_emotional_diary/React-Simple-Diary.mp4" frameborder="0"> </iframe>

# Emotion Diary

## localStorage
  - Save your diary data in local storage in web.
  - To save the data, you need key and value.
  - Value can be number, string, object or etc.
  - `setItem()` sets your data on web. First value is key which should be a string and second value is the value.
  - `getItem()` gets your data from web. You can get a data by key string.
  - When you use object as a value, then you should use `JSON.stringify()` to set and `JSON.parse()` to get.

<figure class="half">
  <a href="/assets/img/posts/react_emotional_diary/21.jpg"><img src="/assets/img/posts/react_emotional_diary/21.jpg"></a>
  <a href="/assets/img/posts/react_emotional_diary/22.jpg"><img src="/assets/img/posts/react_emotional_diary/22.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

## Route
  - Set URL rule.
  - `path` is a url and `element` is a page to show.
  - `:id` gets id from the url.
  - `useParams()` can get the id from url on each page.
  - When you want to move to specific site, then use `<Link to={}></Link>`.

## navigate
  - `navigate()` has two values, first is url and second is option.
  - `{replace: true}` means you cannot go back.

## Title
  - You can get the title of window with `document.getElementsByTagName("title")[0]`. You should use `[0]`, because `getElementsByTagName` get always an array.
  - And set your title with `innerHTML`;
  - You can set title also in `index.html`.

* public/index.html
{% highlight html %}
  <title>Emotion Diary</title>
{% endhighlight %}

## getFullYear() and getMonth()
  - `getFullYear()` gets year for 4-digits.
  - `getMonth()` gets month but January is 0 and December is 11. So you want to show actual month, add 1 like `getMonth() + 1`.

## Google Fonts
  - Import fonts with `@import`.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/23.jpg"><img src="/assets/img/posts/react_emotional_diary/23.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

## Open Graph
  - `og` sets image, site name, description and etc of the web.

* public/index.html
{% highlight html %}
  <meta property="og:image" content="%PUBLIC_URL%/thumbnail.png"/>
  <meta property="og:site_name" content="Emotion Diary"/>
  <meta property="og:description" content="My emotion diary."/>
{% endhighlight %}

## Code

* App.js
{% highlight js %}
import React, { useEffect, useReducer, useRef } from 'react';
import {BrowserRouter, Route, Routes} from 'react-router-dom';

import './App.css';
import Home from './pages/Home';
import New from './pages/New';
import Edit from './pages/Edit';
import Diary from './pages/Diary';

const reducer = (state, action) => {
  let newState = [];
  switch (action.type){
    case 'INIT': 
      return action.data;
    case 'CREATE':
      newState = [action.data, ...state];
      break;
    case 'REMOVE':
      newState = state.filter((it) => 
        it.id !== action.targetId);
      break;
    case 'EDIT': 
      newState = state.map((it) => 
        it.id === action.data.id ? action.data : it);
      break;
    default:
      return state;
  };
  localStorage.setItem("diary", JSON.stringify(newState));
  return newState;
};

export const DiaryStateContext = React.createContext();
export const DiaryDispatchContext = React.createContext();

function App() {
  const [data, dispatch] = useReducer(reducer, []);

  useEffect(() => {
    const localData = localStorage.getItem("diary");
    if(localData){
      const diaryList = JSON.parse(localData)
        .sort((a, b) => parseInt(b.id) - parseInt(a.id));

      if(diaryList.length >= 1){
        dataId.current = parseInt(diaryList[0].id + 1);
        dispatch({type: "INIT", data: diaryList});
      }      
    }
  }, []);

  const dataId = useRef(0);

  const onCreate = (date, content, emotion) => {
    dispatch({type: "CREATE", data:{
      id: dataId.current++,
      date: new Date(date).getTime(),
      content,
      emotion,
      }
    });
  };

  const onRemove = (targetId) => {
    dispatch({type: "REMOVE", targetId});
  };

  const onEdit = (targetId, date, content, emotion) => {
    dispatch({
      type: "EDIT",
      data: {
        id : targetId,
        date : new Date(date).getTime(),
        content,
        emotion
      }
    });
  };

  return (
    <DiaryStateContext.Provider value={data}>
      <DiaryDispatchContext.Provider value={{
        onCreate,
        onEdit,
        onRemove
      }}>
        <BrowserRouter>  
          <div className="App">
            <Routes>
              <Route path='/' element={<Home/>}/>
              <Route path='/new' element={<New/>}/>
              <Route path='/edit/:id' element={<Edit/>}/>
              <Route path='/diary/:id' element={<Diary/>}/>
            </Routes>
          </div>
        </BrowserRouter>
      </DiaryDispatchContext.Provider>
    </DiaryStateContext.Provider>
  );
};

export default App;
{% endhighlight %}

* pages/Home.js
{% highlight js %}
import { useContext, useEffect, useState } from 'react';
import { DiaryStateContext } from '../App';

import MyHeader from '../components/MyHeader';
import MyButton from '../components/MyButton';
import DiaryList from '../components/DiaryList';

const Home = () => {
    const diaryList = useContext(DiaryStateContext);

    const [data, setData] = useState([]);
    const [curDate, setCurDate] = useState(new Date());
    const headText = `${curDate.getFullYear()}. ${curDate.getMonth() + 1}`;

    useEffect(() => {
        const titleElement = document.getElementsByTagName("title")[0];
        titleElement.innerHTML = `Emotion Diary`;
    }, []);

    useEffect(() => {
        const firstDay = new Date(
            curDate.getFullYear(), curDate.getMonth(), 1
        ).getTime();

        const lastDay = new Date(
            curDate.getFullYear(), curDate.getMonth() + 1, 0, 23, 59, 59
        ).getTime();

        setData(diaryList.filter((it) => 
            firstDay <= it.date && it.date <= lastDay));
    }, [diaryList, curDate]);

    const increaseMonth = () => {
        setCurDate(
            new Date(curDate.getFullYear(), curDate.getMonth() + 1)
        );
    };

    const decreaseMonth = () => {
        setCurDate(
            new Date(curDate.getFullYear(), curDate.getMonth() - 1)
        );
    };

    return (
        <div>
            <MyHeader 
                headText={headText}
                leftChild={
                    <MyButton 
                        text={"<"} 
                        onClick={decreaseMonth} 
                    />
                }
                rightChild={
                    <MyButton text={">"} 
                    onClick={increaseMonth} 
                    />
                }
            />
            <DiaryList diaryList={data}/>
        </div>
    );
};

export default Home;
{% endhighlight %}

* pages/New.js
{% highlight js %}
import { useEffect } from 'react';

import DiaryEditor from '../components/DiaryEditor';

const New = () => {
    useEffect(() => {
        const titleElement = document.getElementsByTagName("title")[0];
        titleElement.innerHTML = `Emotion Diary - new diary`;
    }, []);

    return(
        <div>
            <DiaryEditor/>
        </div>
    );
};

export default New;
{% endhighlight %}

* pages/Edit.js
{% highlight js %}
import { useNavigate, useParams } from 'react-router-dom';
import { useContext, useEffect, useState } from 'react';
import { DiaryStateContext } from '../App';

import DiaryEditor from '../components/DiaryEditor';

const Edit = () => {
    const [originData, setOriginData] = useState();

    const navigate = useNavigate();
    const { id } = useParams();

    const diaryList = useContext(DiaryStateContext);

    useEffect(() => {
        const titleElement = document.getElementsByTagName("title")[0];
        titleElement.innerHTML = `Emotion Diary - ${id}th diary edit`;
    }, []);

    useEffect(() => {
        if(diaryList.length >= 1){
            const targetDiary = diaryList.find(
                (it) => parseInt(it.id) === parseInt(id)
            );

            if(targetDiary){
                setOriginData(targetDiary);
            }
            else{
                alert("No Diary");
                navigate('/', {replace: true});
            }
        }
    }, [id, diaryList]);

    return (
        <div className="Edit">
            {originData && 
            <DiaryEditor 
                isEdit={true} 
                originData={originData}
            />}
        </div>
    );
};

export default Edit;
{% endhighlight %}

* pages/Diary.js
{% highlight js %}
import { useContext, useEffect, useState } from 'react';
import { useNavigate, useParams } from 'react-router-dom';
import { DiaryStateContext } from '../App';
import { getStringDate } from '../util/date';
import { emotionList } from '../util/emotion';

import MyButton from '../components/MyButton';
import MyHeader from '../components/MyHeader';

const Diary = () => {
    const { id } = useParams();
    const diaryList = useContext(DiaryStateContext);
    const navigate = useNavigate();
    const [data, setData] = useState();

    useEffect(() => {
        const titleElement = document.getElementsByTagName("title")[0];
        titleElement.innerHTML = `Emotion Diary - ${id}th diary`;
    }, []);

    useEffect(() => {
        if(diaryList.length >= 1){
            const targetDiary = diaryList.find(
                (it) => parseInt(it.id) === parseInt(id)
            );

            if(targetDiary){
                setData(targetDiary);
            }
            else{
                alert("No Diary");
                navigate('/', {replace: true});
            }
        }
    }, [id, diaryList]);

    if(!data){
        return (
            <div className="DiaryPage">Loading...</div>
        );
    }
    else{
        const curEmotionData = emotionList.find(
            (it) => parseInt(it.emotion_id) === parseInt(data.emotion)
        );

        return(
            <div className="DiaryPage">
                <MyHeader 
                    headText={`${getStringDate(new Date(data.date))} Diary`}
                    leftChild={
                        <MyButton 
                            text={'< Back'} 
                            onClick={() => navigate(-1)}
                        />
                    }
                    rightChild={
                        <MyButton 
                            text={'Edit'} 
                            onClick={() => navigate(`/edit/${data.id}`)}
                        />
                    }
                />
                <article>
                    <section>
                        <h4>Today's emotion</h4>
                        <div className={["diary_img_wrapper", 
                            `diary_img_wrapper_${data.emotion}`].join(" ")}>
                            <img src={curEmotionData.emotion_img} />
                            <div className="emotion_descript">
                                {curEmotionData.emotion_descript}
                            </div>
                        </div>
                    </section>
                    <section>
                        <h4>Today's Diary</h4>
                        <div className="diary_content_wrapper">
                            <p>{data.content}</p>
                        </div>
                    </section>
                </article>
            </div>
        );
    }
};

export default Diary;
{% endhighlight %}

* util/date.js
{% highlight js %}
export const getStringDate = (date) => {
    let year = date.getFullYear();
    let month = date.getMonth() + 1;
    let day = date.getDate();
  
    if (month < 10) {
      month = `0${month}`;
    }
  
    if (day < 10) {
      day = `0${day}`;
    }
  
    return `${year}-${month}-${day}`;
};
{% endhighlight %}

* util/emotion.js
{% highlight js %}
export const emotionList = [
    {
        emotion_id: 1,
        emotion_img: process.env.PUBLIC_URL + '/assets/emotion1.png',
        emotion_descript: 'very good'
    },
    {
        emotion_id: 2,
        emotion_img: process.env.PUBLIC_URL + '/assets/emotion2.png',
        emotion_descript: 'good'
    },
    {
        emotion_id: 3,
        emotion_img: process.env.PUBLIC_URL + '/assets/emotion3.png',
        emotion_descript: 'so so'
    },
    {
        emotion_id: 4,
        emotion_img: process.env.PUBLIC_URL + '/assets/emotion4.png',
        emotion_descript: 'bad'
    },
    {
        emotion_id: 5,
        emotion_img: process.env.PUBLIC_URL + '/assets/emotion5.png',
        emotion_descript: 'very bad'
    }
];
{% endhighlight %}

* components/DiaryEditor.js
{% highlight js %}
import { useCallback, useContext, useEffect, useRef, useState } from "react";
import { useNavigate } from "react-router-dom";
import { DiaryDispatchContext } from "../App"
import { getStringDate } from "../util/date";
import { emotionList } from "../util/emotion";

import EmotionItem from "./EmotionItem";
import MyButton from "./MyButton";
import MyHeader from "./MyHeader";

const DiaryEditor = ({isEdit, originData}) => {
    const contentRef = useRef();
    const [content, setContent] = useState("");
    const [emotion, setEmotion] = useState(3);
    const [date, setDate] = useState(getStringDate(new Date()));

    const {onCreate, onEdit, onRemove} = useContext(DiaryDispatchContext);
    const handleClickEmote = useCallback((emotion) => {
        setEmotion(emotion);
    }, []);
    const navigate = useNavigate();

    const handleSubmit = () =>{
        if(content.length < 1){
            contentRef.current.focus();
            return;
        }

        if(window.confirm(isEdit 
            ? "Do you want to edit this diary?" : "Do you want to write new diary?")){
            if(!isEdit){
                onCreate(date, content, emotion);
            }
            else{
                onEdit(originData.id, date, content, emotion);
            }
        }
        navigate('/', {replace: true});
    };

    const handleRemove = () => {
        if(window.confirm("Do you want to remove?")){
            onRemove(originData.id);
            navigate('/', {replace: true});
        }
    };

    useEffect(() => {
        if(isEdit){
            setDate(getStringDate(new Date(parseInt(originData.date))));
            setEmotion(originData.emotion);
            setContent(originData.content);
        }
    }, [isEdit, originData]);

    return (
        <div className="DiaryEditor">
            <MyHeader 
                headText={isEdit ? "Edit Diary" : "Write New Diary"} 
                leftChild={
                    <MyButton 
                        text={"< Back"} 
                        onClick={() => navigate(-1)}
                    />
                }
                rightChild={
                    isEdit && (
                    <MyButton  
                        text={"Remove"} 
                        type={"negative"} 
                        onClick={handleRemove}
                    />)
                }
            />
            <div>
                <section>
                    <h4>What date is it today?</h4>
                    <div className="input_box">
                        <input className="input_date"
                            type="date"
                            value={date}
                            onChange={(e) => setDate(e.target.value)}
                        />
                    </div>
                </section>
                <section>
                    <h4>Today's Emotion</h4>
                    <div className="input_box emotion_list_wrapper">
                        {emotionList.map((it) => (
                            <EmotionItem 
                                key={it.emotion_id} {...it} 
                                onClick={handleClickEmote}
                                isSelected={it.emotion_id === emotion}
                            />    
                        ))}
                    </div>
                </section>
                <section>
                    <h4>Today's Diary</h4>
                    <div className="input_box text_wrapper">
                        <textarea 
                            placeholder="How was it today?" 
                            ref={contentRef} 
                            value={content} 
                            onChange={(e) => setContent(e.target.value)}
                        />
                    </div> 
                </section>
                <section>
                    <div className="control_box">
                        <MyButton 
                            text={'Cancel'} 
                            onClick={() => navigate(-1)}
                        />
                        <MyButton
                            text={'Save'}
                            onClick={handleSubmit}
                        />
                    </div>
                </section>
            </div>
        </div>
    );
};

export default DiaryEditor;
{% endhighlight %}

* components/DiaryItem.js
{% highlight js %}
import React from "react";
import { useNavigate } from "react-router-dom";

import MyButton from "./MyButton";

const DiaryItem = ({id, emotion, content, date}) => {
    const navigate = useNavigate();
    const strDate = new Date(parseInt(date))
        .toLocaleDateString();

    const goDetail = () => {
        navigate(`/diary/${id}`);
    };

    const goEdit = () => {
        navigate(`/edit/${id}`);
    };

    return (
        <div className="DiaryItem">
            <div className={[
                "emotion_img_wrapper", 
                `emotion_img_wrapper_${emotion}`
            ].join(" ")}
                onClick={goDetail}>
                <img src={process.env.PUBLIC_URL + 
                    `assets/emotion${emotion}.png`}/>
            </div>
            <div className="info_wrapper"
                 onClick={goDetail}>
                <div className="diary_date">{strDate}</div>
                <div className="diary_content_preview">
                    {content.slice(0, 25)}
                </div>
            </div>
            <div className="btn_wrapper"
                 onClick={goEdit}>
                <MyButton text={"Edit"}/>
            </div>
        </div>
    );
};

export default React.memo(DiaryItem);
{% endhighlight %}

* components/DiaryList.js
{% highlight js %}
import React, { useState } from "react";
import { useNavigate } from "react-router-dom";

import DiaryItem from "./DiaryItem";
import MyButton from "./MyButton";

const sortOptionList = [
    {value: "latest", name: "latest"},
    {value: "oldest", name: "oldest"}
];

const filterOptionList = [
    {value: "all", name: "all"},
    {value: "good", name: "good"},
    {value: "bad", name: "bad"}
]

const ControlMenu = React.memo(({value, onChange, optionList}) => {
    return (
        <select className="ControlMenu" 
            value={value} 
            onChange={(e) => onChange(e.target.value)}>
            {optionList.map((it, idx) => (
                <option key={idx} value={it.value}>
                    {it.name}
                </option>
            ))}
        </select>
    );
});

const DiaryList = ({diaryList}) => {
    const navigate = useNavigate();

    const [sortType, setSortType] = useState('latest');
    const [filter, setFilter] = useState('all');

    const getProcessedDiaryList = () => {

        const filterCallBack = (item) => {
            if(filter === 'good'){
                return parseInt(item.emotion) <= 3;
            }
            else{
                return parseInt(item.emotion) > 3;
            }
        };

        const compare = (a,b) => {
            if(sortType == 'latest'){
                return parseInt(b.date) - parseInt(a.date);
            }
            else {
                return parseInt(a.date) - parseInt(b.date);
            }
        };
        
        const copyList = JSON.parse(JSON.stringify(diaryList));

        const filteredList = 
            filter === "all" ?
                copyList : copyList.filter((it) => filterCallBack(it));
        const sortedList = filteredList.sort(compare);
        return sortedList;
    };

    return (
        <div className="DiaryList">
            <div className="menu_wrapper">
                <div className="left_col">
                    <ControlMenu 
                        value={sortType} 
                        onChange={setSortType}
                        optionList={sortOptionList}
                    />
                    <ControlMenu
                        value={filter}
                        onChange={setFilter}
                        optionList={filterOptionList}
                    />
                </div>
                <div className="right_col">
                    <MyButton 
                        type={'positive'} 
                        text={'Write New Diary'} 
                        onClick={() => navigate('/new')}
                    />
                </div>
            </div>

            {getProcessedDiaryList().map((it) => (
                <DiaryItem key={it.id} {...it}/>
            ))}
        </div>
    );
};

DiaryList.defaultProps = {
    diaryList: []
};

export default DiaryList;
{% endhighlight %}

* components/EmotionItem.js
{% highlight js %}
import React from "react";

const EmotionItem = ({
    emotion_id, 
    emotion_img, 
    emotion_descript, 
    onClick,
    isSelected,
}) => {
    return (
        <div className={["EmotionItem", isSelected ? 
            `EmotionItem_on_${emotion_id}` : `EmotionItem_off`].join(" ")}
            onClick={() => onClick(emotion_id)}>
            <img src={emotion_img}/>
            <span>{emotion_descript}</span>
        </div>
    );
};

export default React.memo(EmotionItem);
{% endhighlight %}

* components/MyButton.js
{% highlight js %}
const MyButton = ({text, type, onClick}) => {
    const btnType = ['positive', 'negative'].includes(type) ?
        type : "default";

    return (
        <button className={["MyButton", `MyButton_${type}`].join(" ")} 
            onClick={onClick}>
            {text}
        </button>
    );
};

MyButton.defaultProps = {
    type: 'default'
};

export default MyButton;
{% endhighlight %}

* components/MyHeader.js
{% highlight js %}
const MyHeader = ({headText, leftChild, rightChild}) => {
    return (
        <header>
            <div className="head_btn_left">
                {leftChild}
            </div>
            <div className="head_text">
                {headText}
            </div>
            <div className="head_btn_right">
                {rightChild}
            </div>
        </header>
    );
};

export default MyHeader;
{% endhighlight %}

* App.css
{% highlight js %}
@import url('https://fonts.googleapis.com/css2?family=Nanum+Pen+Script&family=Yeon+Sung&display=swap');

body{
  background-color: #f6f6f6;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: 'Nanum Pen Script', cursive;
  min-height: 100vh;
  margin: 0px;
}

@media (min-width : 650px) {
  .App{
    width: 640px;
  }
}

@media (max-width: 650px) {
  .App{
    width: 90vw;
  }
}

#root{
  background-color: white;
  box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
}

.App{
  min-height: 100vh;
  padding-left: 20px;
  padding-right: 20px;
}

/* MyButton */

.MyButton {
  cursor: pointer;
  border: none;
  border-radius: 5px;

  padding-top: 10px;
  padding-bottom: 10px;
  padding-left: 20px;
  padding-right: 20px;

  font-size: 18px;

  white-space: nowrap;
  font-family: 'Nanum Pen Script', cursive;
}

.MyButton_default {
  background-color: #ececec;
  color: black;
}

.MyButton_positive{
  background-color: #64c964;
  color: white;
}

.MyButton_negative{
  background-color: #fd565f;
  color: white;
}

/* MyHeader */

header{
  padding-top: 20px;
  padding-bottom: 20px;

  display: flex;
  align-items: center;
  border-bottom: 1px solid #e2e2e2;
}

header > div {
  display: flex;
}

header .head_text {
  width: 50%;
  font-size: 25px;
  justify-content: center;
}

header .head_btn_left{
  width: 25%;
  justify-content: start;
}

header .head_btn_right{
  width: 25%;
  justify-content: end;
}

header button{
  font-family: "Nanum Pen Script";
}

/* DiaryList */

.DiaryList .menu_wrapper{
  margin-top: 20px;
  margin-bottom: 30px;

  display: flex;
  justify-content: space-between;
}

.DiaryList .menu_wrapper .right_col{
  flex-grow: 1;
}

.DiaryList .menu_wrapper .right_col button{
  width: 100%;
}

.DiaryList .ControlMenu{
  margin-right: 10px;
  border: none;
  border-radius: 5px;
  background-color: #ececec;

  padding-top: 10px;
  padding-bottom: 10px;
  padding-left: 20px;
  padding-right: 20px;

  cursor: pointer;
  font-family: "Nanum Pen Script";
  font-size: 18px;
}

/* DiaryItem */

.DiaryItem{
  padding-top: 15px;
  padding-bottom: 15px;

  border-bottom: 1px solid #e2e2e2 ;
  display: flex;
  justify-content: space-between;
}

.DiaryItem .emotion_img_wrapper{
  cursor: pointer;
  min-width: 120px;
  height: 80px;
  border-radius: 5px;
  display: flex;
  justify-content: center;
}

.DiaryItem .emotion_img_wrapper_1{
  background-color: #64c964;
}

.DiaryItem .emotion_img_wrapper_2{
  background-color: #9dd772;
}

.DiaryItem .emotion_img_wrapper_3{
  background-color: #fdce17;
}

.DiaryItem .emotion_img_wrapper_4{
  background-color: #fd8446;
}

.DiaryItem .emotion_img_wrapper_5{
  background-color: #fd565f;
}

.DiaryItem .emotion_img_wrapper img{
  width: 50%;
}

.DiaryItem .info_wrapper{
  flex-grow: 1;
  margin-left: 20px;
  cursor: pointer;
}

.DiaryItem .diary_date{
  font-weight: bold;
  font-size: 25px;
  margin-bottom: 5px;
}

.DiaryItem .diary_content_preview{
  font-size: 18px;
}

.DiaryItem .btn_wrapper{
  min-width: 70px;
}

/* DiaryEditor */

.DiaryEditor{

}

.DiaryEditor section{
  margin-bottom: 40px;
}

.DiaryEditor h4{
  font-size: 22px;
  font-weight: bold;
}

.DiaryEditor .input_date{
  border: none;
  border-radius: 5px;
  background-color: #ececec;

  padding-top: 10px;
  padding-bottom: 10px;
  padding-left: 20px;
  padding-right: 20px;

  cursor: pointer;
  font-family: "Nanum Pen Script";
  font-size: 20px;
}

.DiaryEditor .emotion_list_wrapper{
  display: grid;
  grid-template-columns: repeat(5, auto);
  gap: 2%;
}

.DiaryEditor textarea{
  font-family: "Nanum Pen Script";
  font-size: 20px;

  box-sizing: border-box;
  width: 100%;
  min-height: 200px;
  resize: vertical;

  border: none;
  border-radius: 5px;
  background-color: #ececec;

  padding: 20px;
}

.DiaryEditor .control_box{
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* EmotionItem*/

.EmotionItem{
  cursor: pointer;

  border-radius: 5px;
  padding-top: 20px;
  padding-bottom: 20px;

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.EmotionItem img{
  width: 50%;
  margin-bottom: 10px;
}

.EmotionItem span{
  font-size: 18px;
}

.EmotionItem_off{
  background-color: #ececec;
}

.EmotionItem_on_1{
  background-color: #64c964;
  color: white;
}

.EmotionItem_on_2{
  background-color: #9dd772;
  color: white;
}

.EmotionItem_on_3{
  background-color: #fdce17;
  color: white;
}

.EmotionItem_on_4{
  background-color: #fd8446;
  color: white;
}

.EmotionItem_on_5{
  background-color: #fd565f;
  color: white;
}

/* Diary */

.DiaryPage{

}

.DiaryPage section {
  width: 100%;
  margin-bottom: 100px;

  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.DiaryPage h4{
  font-size: 22px;
  font-weight: bold;
}

.DiaryPage .diary_img_wrapper{
  background-color: #ececec;
  width: 250px;
  height: 250px;
  border-radius: 5px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
}

.DiaryPage .diary_img_wrapper_1{
  background-color: #64c964;
}

.DiaryPage .diary_img_wrapper_2{
  background-color: #9dd772;
}

.DiaryPage .diary_img_wrapper_3{
  background-color: #fdce17;
}

.DiaryPage .diary_img_wrapper_4{
  background-color: #fd8446;
}

.DiaryPage .diary_img_wrapper_5{
  background-color: #fd565f;
}

.DiaryPage .emotion_descript {
  font-size: 25px;
  color: white;
}

.DiaryPage .diary_content_wrapper{
  width: 100%;
  background-color: #ececec;
  border-radius: 5px;
  word-break: keep-all;
  overflow-wrap: break-word;
}

.DiaryPage .diary_content_wrapper p {
  padding: 20px;
  text-align: left;
  font-size: 20px;
  font-family: 'Yeon Sung';
  line-height: 2.5;
}
{% endhighlight %}

* components/MyHeader.js
{% highlight js %}

{% endhighlight %}

* components/MyHeader.js
{% highlight js %}

{% endhighlight %}

## build
  - `npm run build` will make a build folder. In the folder, there are some files for build. For instance, js files which is compressed without white space.

## Publish

### Firebase
  - Create Project on console.
  - Go to hosting and click start.
  - Open command and type `npm install -g firebase-tools`.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/24.jpg"><img src="/assets/img/posts/react_emotional_diary/24.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

  - In VS Code terminal, type `firebase login` and allow authorization for firebase.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/25.jpg"><img src="/assets/img/posts/react_emotional_diary/25.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

  - Type `firebase init`.

<figure class="half">
  <a href="/assets/img/posts/react_emotional_diary/26.jpg"><img src="/assets/img/posts/react_emotional_diary/26.jpg"></a>
  <a href="/assets/img/posts/react_emotional_diary/27.jpg"><img src="/assets/img/posts/react_emotional_diary/27.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

<figure class="half">
  <a href="/assets/img/posts/react_emotional_diary/28.jpg"><img src="/assets/img/posts/react_emotional_diary/28.jpg"></a>
  <a href="/assets/img/posts/react_emotional_diary/29.jpg"><img src="/assets/img/posts/react_emotional_diary/29.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

  - Create hosting and set the site name on firebase.

<figure>
  <a href="/assets/img/posts/react_emotional_diary/30.jpg"><img src="/assets/img/posts/react_emotional_diary/30.jpg"></a>
  <figcaption>Javascript Emotion Diary</figcaption>
</figure>

  - Set the site on firebase.json
{% highlight json %}
"hosting": {
  "site": "..."
}
{% endhighlight %}

## Demo
  - https://leehuhlee-emotional-diary.web.app/

<iframe width="560" height="315" src="/assets/video/posts/react_emotional_diary/React-Emotion-Diary.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/EmotionDiary){: .btn}
