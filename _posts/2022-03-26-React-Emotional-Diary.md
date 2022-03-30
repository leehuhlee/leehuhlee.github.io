---
layout: post
title: "[React] Emotianal Diary"
date: 2022-03-26
excerpt: "Javascript Node.js React"
tags: [Javascript, Node, React]
comments: false
---

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

[Download](https://github.com/leehuhlee/InteractiveWeb){: .btn}
