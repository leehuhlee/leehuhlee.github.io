---
layout: post
title: "[Agile] Clean Code"
date: 2021-08-20
excerpt: "Clean Code"
tags: [Agile, CI/CD, Javascript]
comments: false
---

<center>Reference by <a href="https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882">Clean Code - Robert C.Martin Series</a></center>

# Foreword
* Introduction
  - I write thousands of or milions of codes one day. And I proud of myself with my codes. However, in several days, I get rid of those codes and rewrite, because I cannot sure this is clean code. Duplicating this work, I can refactorize my codes, as a result, there leaves only requirements. Moreover, it helps our maintainment. But, it needs cost of time and effor. This book is not a solution, but, through Clean Code, I can listen experts' opinions, reorganize my code style, and be more professional.
* Thanks to
  - Benjamin Nuske, who is CEO of Delta Engine GmbH, presented me this book. I really thanks to him.

# Chapter 1: Clean Code

## There Will Be Code
  - Programming makes machine to run and the program is associated with code.
  - Abstraction of programming language is advancing.
  - Code is one of language and is requirement what we express.

## Bad Code
  - Impeding other code
  - Uncareful code
  - Unorganized Code: Later equals Never

## The Total Cost Of Owning A Mess
  - One change makes two or three errors.
  - Over time the mess becomes so big and so deep.
  - And productivity approaches zero.

## The Grand Redesign In The Sky
  - Consuming your time to make clean code is associated with effectivity and professionality.
  - Programming is a race and it can be longer.

## Attitude
  - Requirements' change make bad code.
  - Too tight schedule makes bad code.
  - It is unprofessional for programmers to bend to the will of managers who don't understand the risks of making messes.

## The Primal Conundrum
  - The only way to make the deadline is to keep the code as clean as possible at all times

## The Art Of Clean Code?
  - Writing clean code requires the disciplined use of a myriad little techniques applied through a painstakingly acquired sense of `cleanliness`.
  - The `Code-sense` will help that programmer choose the best variation and guide him or her to plot a sequence of behavior preserving transformations to get from here to there.

## What Is Clean Code?
  - elegant, pleasing, readable, to be enhance, easy to care, no duplication, one thing, expressiveness, tiny abstractions, beutiful

## Schools Of Thought
  - a clean variable name, a clean function, a clean class, etc.

## We Are Authors
  - Indeed, authors are responsible for communicating well with their readers.
  - So making it easy to read actually makes it easier to write.
  - If you want to go fast, if you want to get done quickly, if you want your code to be easy to write, make it easy to read.

## The Boy Scout Rule
  - Leave the caompground cleaner than you found it.

## Prequel and Principles
  - These include the Single Responsibility Principle(SRP), the Open Closed Principle(OCP), and the Dependency Inversion Principle(DIP) among others.

# Chapter 2: Meaningful Names

## Use Intention-Revealing Names
  - Name should reveal intent.
  - With these simple name changes, it's not difficult to understand what's going on.

  ### Example 1

  * Bad
  {% highlight js %}
    var d;
  {% endhighlight %}

  * Good
  {% highlight js %}
    var elapsedTimeInDays;
    var daysSinceCreation;
    var daysSinceModification;
    var fileAgeInDays;
  {% endhighlight %}

  ### Example 2

  * Bad
  {% highlight js %}
    fuction getThem(){
      var list1 = [];
      for(var x in theList)
        if(x[0] == 4)
          list1.push(x)
      return list;
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    function getFlaggedCells(){
      var flaggedCells = [];
      for(var cell in gameBoard)
        if(cell.isFlagged())
          flaggedCells.push(cell);
      return flaggedCells;
    }
  {% endhighlight %}

## Avoid Disinformation
  - We should avoid words whose entrenched meanings vary from our intended meaning.
  - Abbreviation could be disinformative. ex) `hp`, `aix` and `sco`
  - The word list menas something specific to programmers. Just add `-s` ex) `accountList` → `accounts`
  - Using inconsistent spellings is disinformation
  - Don't use single lower-case L or uppercase O as variable names. ex) `if(O == l)`

## Make Meaningful Distinctions
  - If names must be different, then they should also mean something different.
  - Noise words are another meaningless distinction. ex) `Info` and `Data`

## Use Pronounceable Names
  - Single-letter names and numeric constants have a particular problem in that they are not easy to locate across a body of text.
  - Programming is a social activity.
  
  ### Example 1

  * Bad
  {% highlight js %}
    class DtaRcrd102{
      constructor(genymdhms, modymdhms, pszqint){
        this.genymdhms = genymdhms;
        this.modymdhms = modymdhms;
        this.pszqint = "102";
      }
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    class Customer{
      constructor(generationTimestamp, modificationTimestamp, recordId){
        this.generationTimestamp = generationTimestamp;
        this.modificationTimestamp = modificationTimestamp;
        this.recordId = "102";
      }
    }
  {% endhighlight %}
  
## Avoid Encodings
  - Encoding type or scope information into names simply adds an extra burden of deciphering.
  - Encoded names are seldom pronounceable and are easy to mis-type.

## Hungarian Notation
  - Nowdays Hungarian Notation and other forms of type encoding are simply impediments.
  - They make it harder to change the name or type of variable, function, or class.
  - They make it ahrder to read the code.
  - They create the possibility that the encoding system will mislead the reader

## Member Prefixes
  - You should be using an editing environment that highlights or colorizes members to make them distinct.
  - The more we read the code, the less we see the prefixes.

  ### Example 1

  * Bad
  {% highlight js %}
    class Part{
      var m_dsc;
      function setName(name){
        m_dsc = name;
      }
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    class Part{
      var description;
      function setName(description){
        this.description = description;
      }
    }
  {% endhighlight %}

## Interfaces and Implementations
  - The preceding `I`, so common in today's legacy wads, is a distraction at best and too much information at worst.
  - Don't `IShapeFactory`, use `ShapeFactoryImp` or `CshapeFactory`

## Avoid Mental Mapping
  - This is a problem with single-letter variable names.
  - It's just a place holder that the reader must mentally map to the actual concept.
  - Professionals use their powers for good and write code that others can understand.

## Class Names
  - Classes and objects should have noun or noun pharse names.

## Method Names
  - Methods should have verb or berb phrase name.
  - Predicates should be named for their value and prefixed with `get`, `set`, and `is`.

  ### Example 1

  * Good
  {% highlight js %}
    var name = employee.getName();
    customer.setName("mike");
    if(paycheck.isPosted())...
  {% endhighlight %}

  - when constructors are overloaded, use static factory methods with names that describe the arguments. ex) `var fulcrumPoint = Complex.FromRealNumber(23.0);`

## Don't be Cute
  - Choose clarity over entertainment value.
  - Say what you mean. Mean what you say.

## Pick One Word per Concept
  - It's confusing to have `fetch`, `retrieve`, and `get` as equivalent methods of different classes.
  - The function names have to stand alone, and they have to be consistent in order for you to pick the correct method without any additional exploration.
  - A consistent lexicon is a great boon to the programmers who must use your code. 

## Don't Pun
  - Avoid using the same word for two purpose.
  - Out goal, as authors, is to make our code as easy as possible to understand.
  - We wnat our code to be a quick skim, not an intense study.

## Use Solution Domain Names
  - Go ahead and use computer science terms, algorithm names, pattern names, math terms, and so forth. ex) `AccountVisitor` → `JobQueue`
  - Choosing technical names for those things is usually the appropriate course.

## Use Problem Domain Names
  - Separating solution and problem domain concepts is part of the job of a good programmer and designer.
  - The code that has more to do with problem domain concepts should have names drawn from the problem domain.

## Add Meaningful Context
  - Prefixing the name may be necessary as a last resort.
  - The improvement of context also allows the algorithm to be made much cleaner by breaking it into many smaller functions.

  ### Example 1

  * Bad
  {% highlight js %}
    function printGuessStatistics(candidate, count){
      var number;
      var verb;
      var pluralModifier;
      if(count==0){
        number = "no";
        verb = "are";
        pluralModifier = "s";
      }
      else if (count == 1){
        number = "1";
        verb = "is";
        pluralModifier = " ";
      }
      else{
        number = String(count);
        verb = "are";
        pluralModifier = "s";
      }
      var guessMessage = "There ${verb} ${number} ${candidate}${pluralModifier}";
      console.log(guessMessage);
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    class GuessStatisticsMessage{
      var number;
      var verb;
      var pluralModifier;

      function make(candicate, count){
        createPluralDependentMessageParts(count);
        return "There ${verb} ${number} ${candidate}${pluralModifier}";
      }
    }

    function createPluralDependentMessageParts(count){
      if(count == 0){
        thereAreNoLetters();
      }
      else if(count == 1){
        thereIsOneLetter();
      }
      else{
        thereAreManyLetters(count);
      }
    }

    function thereAreManyLetters(count){
      number = String(count);
      verb = "are";
      pluralModifier = "s";
    }

    function thereIsOneLetter(){
      number = "1";
      verb = "is";
      pluralModifier = " ";
    }

    function thereAreNoLetters(){
      number = "no";
      verb = "are";
      pluralModifier = "s";
    }
  {% endhighlight %}

## Don't Add Gratuitous Context
  - Shorter names are generally better than longer ones, so long as they are clear.
  - Add no more context to a name than is necessary.
  - The resulting names are more precise, which is the point of all naming.

## Final Words
  - We do not share that fear and find that we are actually grateful when names changes for the better.
  - You will probably end up surprising someone when you rename, just like you might with any other code improvement.
  - If you are maintaining someone else's code, use refactoring tools to help resolve these problems.
  - It will pay off in the short term and continue to pay in the long term.
  