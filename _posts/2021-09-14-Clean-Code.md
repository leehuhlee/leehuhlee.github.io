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

# Chapter 3: Functions

## Introduction
  - Functions are the first line of organization in any program.
  
### Example 1

  * Bad
  {% highlight js %}
    function testableHtml(pageData, includeSuiteSetup){
      var wikiPage = pageData.getWikiPage();
      var buffer;
      if(pageData.hasAttribute("Test")){
        if(includeSuiteSetup){
          var suiteSetup = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_SETUP_NAME, wikiPage);
          if(suiteSetup != null){
            var pagePath = suiteSetup.getPageCrawler().getFullPath(suiteSetup);
            var pagePathName = PathParser.render(pagePath);
            buffer.append("!include -setup .").append(pagePathName).append("\n");
          }
        }
        var setup = PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
        if(setup != null){
          var setupPath = wikiPage.getPageCrawler().getFullPath(setup);
          var setupPathName = PathParser.render(setupPath);
          buffer.append("!include -setup .").append(setupPathName).append("\n");
        }
      }
      buffer.append(pageData.getContent());
      if(pageData.hasAttribute("Test")){
        var teardown = PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
        if(teardown != null){
          var tearDownPath = wikiPage.getPageCrawler().getFullPath(teardown);
          var tearDownPathName = PathParser.render(tearDownPath);
          buffer.append("\n").append("!include -teardown .").append(tearDownPathName).append("\n");
        }
        if(includeSuiteSetup){
          var suiteTeardown = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME, wikiPage);
          if(suiteTeardown != null){
            var pagePath = suiteTeardown.getPageCrawler().getPullPath(suiteTeardown);
            var pagePathName = PathParser.render(pagePath);
            buffer.append("!include -teardown .").append(pagePathName).append("\n");
          }
        }
      }
      pageData.setContent(String(buffer));
      return pageData.getHtml();
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    function renderPageWithSetupsAndTeardowns(){
      var isTestPage = pageData.hasAttribute("Test");
      if(isTestPage){
        var testPage = pageData.getWikiPage();
        var newPageContent;
        includeSetupPages(testPage, newPageContent, isSuite);
        newPageContent.append(pageData.getContent());
        includeTearDownPages(testPage, newPageContent, isSuite);
        pageData.setContent((string)newPageContent);
      }
      return pageData.getHtml();
    }
  {% endhighlight %}

## Small!
  - The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.
  
### Example 1

  * Good
  {% highlight js %}
    function renderPageWithSetupsAndTeardowns(pageData, isSuite){
      if(isTestPage(pageData))
        includeSetupAndTeardownPages(pageData, isSuite);
      return pageData.getHtml();
    }
  {% endhighlight %}

## Blocks and Indenting
  - This implies that the blocks within `if` statements, `else` statements, `while` statements, and so on should be one line long.
  - The indent level of a function should not be greater than one or two.

## Do One Thing
  - We can describe the function by describing it as a brief `TO` paragraph
  - Another way to know that a function is doing more then one thing is if you can extract another function from it with a name that is not merely a restatement of its implementation.

## Sections within Functions
  - Functions that do one thing cannot be reasonably divided into sections.

## One Level of Abstraction per Function
  - In order to make sure our functions are doing one thing, we need to make sure that the statements within our function are all at the same level of abstraction.
  - Mixing levels of abstraction within a function is always confusing.

## Reading Code from Top to Bottom: The Stepdown Rule
  - We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions.
  - We want to be able to read the program as though it were a set of TO paragraphs, each of which is describing the current level of abstraction and referencing subsequent TO paragraphs at the next level down.
  - Making the code read like a top-down set of TO paragraphs is an effective technique for keeping the abstraction level consistent.

## Switch Statements
  - By the nature, `switch` statements always do N things.
  - Unfortunately we can't always avoid `switch` statements, but we can make sure that each `switch` statement is buried in a low-level class ans is never repeated with polymorphism.
  - My general rule for `switch` statements is that they can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance relationship so that the rest of the system can't see them.

### Example 1

  * Bad
  {% highlight js %}
    function calculatePay(e){
      switch(e.type){
        case COMMISSIONED;
          return calculateCommissionedPay(e);
        case HOURLY:
          return calculate HourlyPay(e);
        case SALARIED:
          return calculateSalariedPay(e);
        default:
          throw new InvalidEmployeeType(e.type);
      }
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    class Employee{
      isPayday();
      calculatePay();
      deliverPay(pay);
    }

    interface EmployeeFactory{
      function makeEmployee(r);
    }

    class EmployeeFactoryImpl implements EmployeeFactory{
      function makeEmployee(r){
        switch(r.type){
          case COMISSIONED:
            return new CommissionedEmployee(r);
          case HOURLY:
            return new HourlyEmployee(r);
          case SALARIED:
            return new SalariedEmploye(r);
          default:
            throw new InvalidEmployeeType(r.type);
        }
      }
    }
  {% endhighlight %}

## Use Descriptive Names
  - The smaller and more focused a function is, the easier it is to choose a descriptive name.
  - A long descriptive name is better than a short enigmatic name.
  - A long descriptive name is better than a long descriptive comment.
  - Choosing descriptive names will clarify the design of the module in your mind and help you to improve it.
  - Use the same phrases, nouns and verbs in the function names you choose for your modules.

## Function Arguments
  - The ideal number of arguments for a function is zero(niladic). Next come one(monadic), follwed closely by two(dyadic). Three arguments(triadic) should be avoided where possible.
  - They take a lot of conceptual power. The argument is at a different level of abstraction than the function name and forces you to know a detail that isn't particularly important at that point.
  - Output arguments are harder to understand than input arguments. When we read a function, we are used to the idea of informatio going `in` to the function through arguments and `out` through the return value.

## Common Monadic Forms
  - `function fileExists(MyFile)`, `function fileOpen(MyFile)`
  - Event

## Flag Arguments
  - It immediately complicates the signature of the method, loudly proclaiming that this function does more than one thing. It does one thing if the flag is true and another if the flag is false.

## Dyadic Functions
  - The parts we ignore are where the bugs will hide.
  - `Point p = new Point(0, 0;`
  - You should be aware that they come at a cost and should take advantage of what mechanisms may be available to you to convert them into monads.

## Triads
  - I suggest you think very carefully before creating a triad.

## Argument Objects
  - When a function seems to need more than two or three arguments, it is likely that some of those arguments ought to be wrapped into a class of their own.

### Example 1

  * Bad
  {% highlight js %}
    function makeCircle(x, y, radius){...}
  {% endhighlight %}

  * Good
  {% highlight js %}
    function makeCircle(center, radius){...}
  {% endhighlight %}

## Argument Lists
  - `String.format` is actually dyadic.
  - Functions that take variable arguments can be monads, dyads, or even triads. But it would be a mistake to five them more arguments than that.

## Verbs and Keywords
  - In the case of a monad, the function and argument should form a very nice verb/noun pair.
  - `WrieteField(name)`

## Have No Side Effects
  - Your function promises to do one thing, but it also does other hidden things.
  - If you must have a temporal coupling, you should make it clear in the name of the function.

### Example 1

  * Bad
  {% highlight js %}
    class EmployeeFactoryImpl implements EmployeeFactory{
      var cryptographer;

      function checkPassword(userName, password){
        var user = UserGateway.findByName(userName);
        if(user != User.NULL){
          var codedPhrase = user.getPhraseEncodedByPassword();
          var phrase = cryptographer.decrypt(codedPhrase, password);
          if("Valid Password".equals(phrase)){
            Session.initialize();
            return true;
          }
        }
        return false;
      }
    }
  {% endhighlight %}

## Output Arguments
  - Arguments are most naturally interpreted as input to a function.
  - Anything that forces you to check the function signature is equivalent to a double-take. It's a cognitive break and should be avoided.
  - Much of the need for output arguments disappears in OO languages because `this` is intended to act as an output argument.
  - In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object.

## Command Query Separation
  - Function should either do something or answer something, but not both.
  - Either your function should change the state of an object, or it should return some information about that object.
  - Doing both often leads to confusion.
  - `arrtivuteExists("username")`, `setAttribute("username","unclebob")`

## Prefer Exceptions to Returning Error Codes
  - Returning error codes from command functions is a subtle violation of command query separtation. It promotes commands being used as expressions in the predicates of `if` statements.
  - On the other hand, if you use exceiptions instead of returned error codes, then the error processing code can be separated from the happy path code and can be simplified.

## Extract Try/Catch Blocks
  - It is better to extract the boides of the `try` and `catch` blocks out into functions of their own.

### Example 1

  * Good
  {% highlight js %}
    function delete(page){
      try{
        deletePageAndAllReference(page);
      }
      catch(e){
        logError(e);
      }
    }

    function deletePageAndAllReference(page) throw Exception{
      deletePage(page);
      registry.deleteReference(page.name);
      configKeys.deleteKey(page.name.makeKey());
    }

    function logError(e){
      logger.log(e.getMessage());
    }
  {% endhighlight %}

## Error Handling Is One Thing
  - A function that handles errors should do nothing else.
  - This implies that if the keyword `try` exists in a function it should be the very first word in the function and that there should be nothing after the `catch`/`finally` blocks.

## Don't Repeat Yourself
  - This duplication was remedied by the `include`method
  - Consider also how object-oriented programing werves to concentrate code into base classes that would otherwise be redundant.
  - Structured programming, Aspect Oriented PRogramming, Component Oriented Programming, are all, in part, strategies for eliminating duplication.
  - It would appear that since the invention of the subroutine, innovations in software development have been an ongoing attempt to eliminate duplication from our source code.

## Structured Programming
  - Dijkstra said that every function, and every block within a function, should have one entry and one exit. Following these rules means that there should only be one `return` statement in a function, no `break` or `continue` statements in a loop, and never, ever, any `goto` statements.
  - If you keep your functions small, then the occasional multiple `return`, `break`, or `continue` statement does no harm and can sometimes even be more expressive than the single-entry, single-exit rule.
  - On the other hand, `goto` only makes sense in large functions, so it should be avoided.

## How Do You Write Functions Like This?
  - I also have a suite of unit tests that cover every one of those clumsy lines of code.
  - I massage and refine that code, splitting out functions, changing names, eliminating duplication.
  - I shrink the methods and reorder them.
  - Sometimes I break out whole classes, all the while keeping the tests passing.

## Conclusion
  - Functions are the verbs of that language, and classes are the nouns.
  - But, never forget that your real goal is to tell the story of the system, and that the functions you write need to fit cleanly together into a clear and precise language to help you with that telling.
  