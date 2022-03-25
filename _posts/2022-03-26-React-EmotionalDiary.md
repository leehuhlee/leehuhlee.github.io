---
layout: post
title: "[React] Emotianal Diary"
date: 2022-03-26
excerpt: "Javascript Node.js React"
tags: [Javascript, Node, React]
comments: false
---

# Javascript

* CodeSandbox
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
  - 
  
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
  <figcaption>Javascript Destructing Assignment</figcaption>
</figure>


[Download](https://github.com/leehuhlee/Python){: .btn}