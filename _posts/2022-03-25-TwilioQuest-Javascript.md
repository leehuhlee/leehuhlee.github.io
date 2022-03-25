---
layout: post
title: "[Javascript] Twilio Quest : Javascript Test Lab"
date: 2022-03-25
excerpt: "Javascript Algorithm"
tags: [Javascript, Algorithm, Grammer]
comments: false
---

# Twilio Quest
  - https://www.twilio.com/quest
  - TwilioQuest is an educational video game designed to teach a new generation of developers how to change the world with code.
  - From learning how to use your terminal, to coding in Python, JavaScript, and contributing to Open Source projects, TwilioQuest will help you develop practical engineering skills.

# Process.argv
  - Get input string from the console.
  - Output is string array.

{% highlight js %}
const argumentValue = process.argv[2];
const treeLifeStatus = Number(argumentValue);

if(treeLifeStatus == 0)
    console.log("alive");
else if(treeLifeStatus == 1)
    console.log("flowering");
else if(treeLifeStatus == 2)
    console.log("shedding");
else
    console.log("other");
{% endhighlight %}

# String
  * `localCompare`
    - Get compared result by number(-1, 0, 1) with specific language and options.

{% highlight js %}
const firstValue = process.argv[2];
const secondValue = process.argv[3];

console.log(firstValue.localeCompare(secondValue, 'en', { sensitivity: 'base' }));
{% endhighlight %}

# Type Casting
  - string => int, float, double ... : `Number`
  - int, float, double ... => string : `String`

## Number
  - In javascript, you don't need to separate Number Type.
  - int, float, double, log and etc. are all `Number`.

{% highlight js %}
const numberInput = Number(process.argv[2]);
let output = '';

if (numberInput % 3 == 0) {
  output += 'Java';
}

if (numberInput % 5 == 0) {
  output += 'Script';
}

if (output == "") {
  output = String(numberInput);
}

console.log(output);
{% endhighlight %}

# Array
  - `map` : mapping result from array.
{% highlight js %}
function mutate(freightItems){
    let mutatedItems = [];

    mutatedItems = freightItems.map(item => item.toUpperCase());

    return mutatedItems;
}

const mutated = mutate(['dog', 'ray gun', 'cat']);
console.log('Mutated Items');
console.log(mutated);
{% endhighlight %}

  - `reduce` : add result from the array. You can also set initial value.
{% highlight js %}
function calculateMass(freightItems){
    let totalMass = 0;

    totalMass = freightItems.reduce((sum, current) => sum + current.length, 0);

    return totalMass;
}
{% endhighlight %}

const mass = calculateMass(['dog', 'donkey', 'cat']);
console.log('Total mass of items is ' + mass);
{% endhighlight %}

  - `slice` : slice array with specific indexes.
{% highlight js %}
function getFirstAmountSorted(inputArray, numberOfItems) {
    inputArray.sort();
    let outputArray = [];
    if(numberOfItems > 0)
        outputArray = inputArray.slice(0, numberOfItems)
    return outputArray;
  }
  
const newArray = getFirstAmountSorted(['cat', 'apple', 'bat'], 2);
console.log(newArray);
{% endhighlight %}

  - `filter` : filter array with specific function.
{% highlight js %}
function scanAndFilter(freightItems, forbiddenString) {
    let filteredItems = [];
  
    filteredItems = freightItems.filter((item) => {
      return item !== forbiddenString;
    });
  
    return filteredItems;
  }
  
  const filtered = scanAndFilter(
    ["dog", "ray gun", "cat", "zippers", "ray gun"],
    "ray gun"
  );
  console.log("Filtered Items");
  console.log(filtered);
{% endhighlight %}

# Class
  - `Class` can reduce code and make it more reuseable.
  
## Constructor
  - `constructor` can initialize members and methods.

## Function
  - When you create function in class, you shouldn't type `function`.

{% highlight js %}
class Materializer{
    constructor(targetName){
        this.target = targetName;
        this.activated = false;
    }

    activate(){
        this.activated = true;
    }

    materialize(){
        if(this.activated) return this.target;
        return undefined;
    }
}
{% endhighlight %}

[Download](https://github.com/leehuhlee/Javascript){: .btn}