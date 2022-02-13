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

# Chapter 4: Comments
  - If our programming languages were expressive enough, or if we had the talent to subtly wield those languages to express our intent, we would not need comments very much.
  - Comments are always failures.
  - Programmers can't realistically maintain them.
  - Therefore, though comments are sometimes necessary, we will expand significant energy to minimize them.

## Comments Do Not Make Up for Bad Code
  - Clear and expressive code with few comments is far superior to cluttered and complex code with lots of comments.

## Explain Yourself in Code
  - There are certainly times when code makes a poor vehicle for explanation.
  - It takes only a few seconds of thought to explain most of your intent in code.
  - In many cases it's simply a matter of creating a function that says the same thing as the comment you want to write.

### Example 1

  * Bad
  {% highlight js %}
    // Check to see if the employee is eligible for full benefits
    if((employee.flags & HOURLY_FLAG) && (employee.age > 65))
  {% endhighlight %}

  * Good
  {% highlight js %}
    if(employee.isEligibleForFullBenefits())
  {% endhighlight %}
  
## Good Comments
  - Some comments are necessary or beneficial.
  - Keep in mind, however, that the only truly good comment is the comment you found a way not to write.

## Legal Comments
  - Copyright and authorship statements are necessary and reasonable things to put into a comment at the start of each source file.
  - Where possible, refer to a standard license or other external document rather than putting all the terms and conditions into the comment.

## Informative Comments
  - Still, it might have been better, and clearer, then the comment would likely habe been superfluous.

## Explanation of Intent
  - Sometimes a comment goes beyond just useful information about the implementation and provides the intent behind a decision.

### Example 1

  * Bad
  {% highlight js %}
    function compareTo(o){
      if(o instanceof WikiPagePath)
      {
        var p = o;
        var compressedName = StringUtil.join(names, "");
        var compressedArgumentName = StringUtil.join(p.name, "");
        return compressdName.compareTo(compressedArgumentName);
      }
      return 1; // we are greater because we are the right type.
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    function testConcurrentAddwidgets() throws Exceiption{
      var widgetBuilder = new WidgetBuilder(new Class[]{BoldWidget.class});
      var text = "'''bold text'''";
      var parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
      var failFlag = new AtomicBoolean();
      failFlag.set(false);

      // This is our best attempt to get a race condition
      // by creating large number of threads.
      for(var i = 0; i < 25000; i++){
        var widgetBuilderThread = new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
        var thread = new Thread(widgetBuilderThread);
        thread.start();
      }
      assertEquals(false, failFlag.get());
    }
  {% endhighlight %}

## Clarification
  - When its part of the standard library, or in code that you cannot alter, then a helpful clarifying comment can be useful.
  - Before writing comments like this, take care that there is no better way, and then take even more care that they are accurate.

## Warning of Consequences
  - It will prevent some overly eager programmer from using a static initializer in the name of efficiency.
  
### Example 1

  * Good
  {% highlight js %}
    // Don't run unless you
    // have some time to kill.
    function _testWithReallyBigFile(){
      writeLinesToFile(1000000000);

      response.setBody(testFile);
      response.readyToSend(this);
      var responseString = output.toString();
      assertSubString("Content-Length: 1000000000", responseString);
      assertTrue(bytesSent > 1000000000);
    }
  {% endhighlight %}

  * Good
  {% highlight js %}
    function testConcurrentAddwidgets() throws Exceiption{
      // SimpleDateFormat is not thread sage.
      // so we need to create each instance independently.
      var df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
      df.setTimeZone(TimeZone.getTimeZone("GMT"));
      return df;
    }
  {% endhighlight %}

## TODO Comments
  - It is sometimes resonable to leave "To do" notes in the form of `//TODO` comments.
  - The `TODO` comments explains why the function has a degenerate implementation and what that function's future should be.
  - `TODOs` are jobs that the programmer thinks should be done, but for some reason can't do at the moment.
  - So scan through them regularly and eliminate the ones you can.

### Example 1

  * Good
  {% highlight js %}
    // TODO-MDM these are not needed
    // We expect this to go away when we do the checkout model
    function makeVersion() throw Exceptions{
      return null;
    }
  {% endhighlight %}

## Amplification
  - A comment may be used to amplify the importance of something that may otherwise seem inconsequential.

### Example 1

  * Good
  {% highlight js %}
    var listItemContent = match.group(3).trim();
    // the trim is real important. It removes the starting 
    // spaces that could cause the item to be recognised
    // as another list.
    new ListItemWidget(this, ListItemContent, this.level + 1);
    return buildList.substring(match.end());
  {% endhighlight %}

## Bad Comments
  - Usually they are crutches or excuses for poor code or justifications for insufficient decisions, amounting to little more than the programmer talking to himself.

## Mumbling
  - If you decide to write a comment, then spend the time necessary to make sure it is the best comment you can write.
  - Any commment that forces you to look in another module for the meaning of that comment ahs failed to communicate to you and is not worth th bits if consumes.

### Example 1

  * Bad
  {% highlight js %}
    function loadProperties(){
      try{
        var propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
        var peropertiesStream = new FileInputStream(propertiesPath);
        loadedProperties.load(propertiesStream);
      }
      catch(e){
        // No peoperties files means all defaults are loaded
      }
    }
  {% endhighlight %}

## Redundant Comments
  - It's certainly not more informative than the code.
  
### Example 1

  * Bad
  {% highlight js %}
    // Utility method that returns when this.closed is true. Throws an exception
    // if the timeout is reached.
    function waitForClose(timeoutMillis) throws Exception{
      if(!closed){
        wait(timeoutMillis);
        if(!closed)
          throw new Exception("MockResponseSender could not be closed");
      }
    }
  {% endhighlight %}

## Misleading Comments
  - This subtle bit of misinformation, couched in a comment that is harder to read than the body of the code, coyld cause another programmer to blithely call this function in the expectation.
  - That poor programmer would then find himself in a debugging session trying to figure out why his code executed so slowly.

## Mandated Comments
  - It is just plain silly to have a rule that says every function must have a javadoc, or every variable must have a comment.
  - This clutter adds nothing and serves only to obfuscate the code and create the potential for lies and misdirection.

### Example 1

  * Bad
  {% highlight js %}
    /*
     @ param title The title of the CD
     @ param author The  author of the CD
     @ param tracks The number of tracks on the CD
     @ param durationInMinutes The duration of the CD in minutes
    */
  {% endhighlight %}

## Journal Comments
  - These comments accumulate as a kind of journal, or log, of every change that has ever been made.
  - They should be completely removed.

## Noise Comments
  - They restate the obvious and provide no new information.
  - Eventually the comments begin to lie as the code around them changes.
  - Rather than venting in a worthless and noisy comment, the programmer should have recognized that his frustration could be resolved by improving the structure of his code.
  - Replace the temptation to create noise with the determination to clean your code.

### Example 1

  * Bad
  {% highlight js %}
    /*
     Default constructor.
    */
    function AnnualDateRule(){ }
  {% endhighlight %}

### Example 2

  * Bad
  {% highlight js %}
    /* The day of the month */
    var dayOfMonth;
  {% endhighlight %}

### Example 3

  * Bad
    {% highlight js %}
      /*
       Returns the day of the month.
       
       @return the day of the month.
      */
      function getDayOfMonth(){
        return dayOfMonth;
      }
    {% endhighlight %}

### Example 4

  * Bad
    {% highlight js %}
      function startSending(){
        try{
          doSending();
        }
        catch(SocketException e){
          // normal. someone stopped the request.
        }
        catch(Exception e){
          try{
            response.add(ErrorResponder.makeExceptionString(e));
            response.closeAll();
          }
          catch{
            // Give me a break!
          }
        }
      }
    {% endhighlight %}

  * Good
    {% highlight js %}
      function startSending(){
        try{
          doSending();
        }
        catch(SocketException e){
          // normal. someone stopped the request.
        }
        catch(Exception e){
          addExceptionAndCloseResponse(e);
        }
      }

      function addExceptionAndCloseResponse(Exception e){
        try{
          response.add(ErorResponder.makeExceptionString(e));
          response.closeAll();
        }
        catch(Exception e){ }
      }
    {% endhighlight %}

## Scary Noise
  - They are just redundant noisy comments written out of some misplaced desire to provide documentation.

### Example 1

  * Bad
    {% highlight js %}
      /** The name. */
      var name;

      /** The version. */
      var version;

      /** The licenceName */
      var licenceName;

      /** The version */
      var info;
    {% endhighlight %}
  
## Don't Use a Comment When You Can Use a Function or a Variable
  - The author of the original may have written the comment first and then written the code to fulfill the comment.

 ### Example 1

  * Bad
    {% highlight js %}
      // does the module from the global list <mod> depend on the 
      // subsystem we are part of?
      if (smodule.getDependSubsystems().contains(subSysMod.getSubsystem()))
    {% endhighlight %}  
  
  * Good
    {% highlight js %}
      var moduleDependees = smodule.getDependSubsystems();
      var ourSubSystem = subSysMod.getSubSystem();
      if (moduleDependees.contains(ourSubSystem))
    {% endhighlight %}  

## Position Markers
  - So use them very sparingly, and only when the benefit is significant.
  - If you overuse banners, they'll fall into the background noise and be ignored.

### Example 1

  * Bad
    {% highlight js %}
      // Actions //////////////////////////////
    {% endhighlight %}

## Closing Brace Comments
  - Although this might make sense for long functions with deeply nested structures, it serves only to clutter the kind of small and encapsulated functions that we prefer.

### Example 1

  * Bad
    {% highlight js %}
      class wc{
        function main(args){
          var in = new BufferedReader(new InputStreamReader(System.in));
          var line;
          var lineCount = 0;
          var charCount = 0;
          var wordCount = 0;
          try{
            while((line = in.readLine()) != null){
              lineCount++;
              charCount += line.length();
              var words = line.split("\\W");
              wordCount += words.length;
            } // while
            console.log("wordCount = " + wordCount);
            console.log("lineCount = " + lineCount);
            console.log("CharCount = " + charCount);
          } // try
          catch(e){
            console.log("Error:" +)
          }
        }
      }
    {% endhighlight %} 

## Attributions and Bylines
  - Again, the source code control system is a better place for this kind of information.

### Example 1

  * Bad
    {% highlight js %}
      /* Added by Rick */
    {% endhighlight %}

## Commented-Out Code
  - Few practices are as odious as commenting-out code.
  - Other will think it is there for a reason and is too important to delete.
  - Just delete the code.

### Example 1

  * Bad
    {% highlight js %}
      InputStreamResponse response = new InputStreamResponse();
      response.setBody(formatter.getResultStream(), formatter.getByteCount());
      // InputStream resultsStream = formatter.getResultStream();
      // StreamReader reader = new StreamReader(resultsStream);
      // response.setContent(reader.read(formatter.getByteCount()));
    {% endhighlight %}

### Example 2

  * Bad
    {% highlight js %}
      this.bytePos = writeBytes(pngIdBytes, 0);
      // hdrPos = bytePos;
      writeHeader();
      writeResolution();
      // dataPos = bytePos;
      if(writeImageData()){
        writeEnd();
        this.pngBytes = resizeByteArray(this.pngBytes, this.maxPos);
      }
      else{
        this.pngBytes = null;
      }
      return this.pngBytes;
    {% endhighlight %}

## HTML Comments
  - It makes the comments hard to read in the one place where they should be easy to read=the ediot/IDE.

### Example 1

  * Bad
    {% highlight js %}
      /*
        Task to run fit tests.
        This task runs fitnesse tests and publishes the results.
        <p/>
        <pre>
        Usage:
        &lt;taskdef name=&quot;execute-fitnesse-tests&quot;
            classname=&quot;fitnesse.ant.ExecuteFitnesseTestsTask&quot;
            classpathref=&quot;classpath&quot; /&gt;
        OR
        &lt;taskdef classpathref=&quot;classpath&quot;
                    resource=&quot;tasks.properties&quot; /&gt;
        <p/>
        &lt;execute-fitnesse-tests
            suitepage=&quot;FitNesse.SuitAcceptanceTests&quot;
            fitnesseport=&quot;8082&quot;
            resultsdir=&quot;${results.dir}&quot;
            resultshtmlpage=&quot;fit-results.html&quot;
            classpathref=&quot;classpath&quot; /&gt;
        </pre>
      */
    {% endhighlight %}

## Nonlocal Information
  - If you must write a comment, then make sure it describes the code it appears near.

### Example 1

  * Bad
    {% highlight js %}
      /*
       Port on which fitnesse would run. Defaults to <b>8082</b>.
       
       @oaram fitnessePort
      */
      public void setFitnessePort(int fitnessePort){
        this.fitnessePort = fitnessePort;
      }
    {% endhighlight %}

## Too Much Information
  - Don't put interesting historical discussions or irrelevant descriptions of details into your comments.

### Example 1

  * Bad
    {% highlight js %}
      /*
        RFC 2045 - Multipurpose Internet Mail Extensions (MIME)
        Part One: Format of Internet Message Bodies
        section 6.8. Base64 Content-Transfer-Encoding
        The encoding process represents 24-bit groups of input bits as output
        strings of 4 encoded characters. Proceeding from left to right, a 
        24-bit input group is formed by concatenating 3 8-bit input groups.
        These 24 bits are then treated as 4 concatenated 6-bit groups, each
        of which is translated into a single digit in the base64 alphabet.
        When encoding a bit stream via the base64 encoding, the bit first.
        That is, the first bit in the stream will be the high-order bit in 
        the first 8-bit byte, and the eighth bit will be the low-order bit in
        the first 8-bit byte, and so on.
      */
    {% endhighlight %}

## Inobvious Connection
  - The connection between an comment and the code it describes should be obvious.
  - The purpose of a comment is to explain code that does not explain itself.

### Example 1

  * Bad
    {% highlight js %}
      /* 
        start with an array that is big enough to hold all the pixels
        (plus filter bytes), and an extra 200 bytes for header info
       */
       this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
    {% endhighlight %}

## Function Headers 
  - Short functions don't need much description.
  - A well-chosen name for a small function that does one thing is usually better a comment header.

# Chapter 5: Formatting
  - You should take care that your code is nicely formatted.
  - You should choose a set of simple rules that govern the format of your code, and then you consistently apply those rules.

## The Purpose of Formatting
  - Code formatting is about communication, and communication is the professional developer's first order of business.
  - The coding style and readabilty set precedents that continue to affect maintainability and extensibility long after the original code has been changed beyond recognition.

## Vertical Formatting
  - It turns out that there is a huge range of sizes and some remarkable differences in style.
  - small files are usually easier to understand than large files are.

## The Newspaper Metaphor
  - You read it vertically.
  - The name should be simple but explanatory.
  - Detail should increase as we move downward, until at the end we find the lowest level functions and details in the source file.

## Vertical Openness Between Concepts
  - Each line represents an expression or a clause, and each group of lines represents a complete thought. Those thoughts should be separated from each other iwht blank lines.

### Example 1

  * Bad
    {% highlight js %}
      class BoldWidget extends ParentWidget{
        var REGEXP = "'''.+?'''";
        var pattern = Pattern.compile("'''(.+?)'''", Pattern.MULTILINE + Pattern.DOTALL);
        function BoldWidget(ParentWidget parent, String text) throws Exception{
          super(parent);
          var match = pattern.matcher(text);
          match.find();
          addChildWidgets(match.group(1));}
        function render() throws Exception{
          var html = new StringBuffer("<b>");
          html.append(childHtml()).append("</b>");
          return html.toString();
        }
      }
    {% endhighlight %}

  * Good
    {% highlight js %}
      class BoldWidget extends ParentWidget{
        var REGEXP = "'''.+?'''";
        var pattern = Pattern.compile("'''(.+?)'''", 
          Pattern.MULTILINE + Pattern.DOTALL
        );

        function BoldWidget(ParentWidget parent, String text) throws Exception{
          super(parent);
          var match = pattern.matcher(text);
          match.find();
          addChildWidgets(match.group(1));
        }

        function render() throws Exception{
          var html = new StringBuffer("<b>");
          html.append(childHtml()).append("</b>");
          return html.toString();
        }
      }
    {% endhighlight %}

## Vertical Density
- So lines of code that are tightly related appear vertically dense.

### Example 1

  * Bad
    {% highlight js %}
      class ReporterConfig{
        /*
           The class name of the reporter listener
        */
        var m_className;

        /*
           The properties of the reporter listener
        */
        var m_properties = new ArrayList<Property>();

        function(property){
          m_properties.add(property);
        }
      }
    {% endhighlight %} 

    * Good
    {% highlight js %}
      class ReporterConfig{
        var m_className;
        var m_properties = new ArrayList<Property>();

        function(property){
          m_properties.add(property);
        }
      }
    {% endhighlight %} 

## Vertical Distance
- Concepts that are closely related should be kept vertically close to each other.
- Clearly this rule doesn't work for concepts that belong in separate files.
- But then closely related concepts should not be separated into different files.
- Indeed, this is one of the reasons that protected variables hould be avoided.
- For those concepts that are so closely related that they belong in the same source file, their vertical separation should be a measure of how importnat each is to the understandability of the other.

* Variable Declarations
- Variables should be declared as close to their usage as possible.
- Because our functions are very short, local variables should appear at the top of each function.
- Control variables for loops should usually be declared within the loop statement.
- In rare cases a variable might be declared at the top of a block or just before a loop in a longish function.

### Example 1
  
  * Good
    {% highlight js %}
      function readPreferences(){
        InputStream is = null;
        try{
          is = new FileInputStream(getPreferencesFile());
          setPreferences(new Properties(getPreferences()));
          getPreferences().load(is);
        }
        catch(e){
          try{
            if(is != null)
              is.close();
          }
          catch(e){
          }
        }
      } 
    {% endhighlight %} 

  * Good
    {% highlight js %}
      function countTestCases(){
        int count = 0;
        for(Test each : tests)
          count += each.countTestCases();
        return count;
      }
    {% endhighlight %} 

  * Good
    {% highlight js %}
      for(XmlTest test : m_suite.getTests()){
        TestRunner tr = m_runnerFactory.newTestRunner(this, test);
        tr.addListener(m_textReporter);
        m_testRunners.add(tr);

        invoker = tr.getInvoker();

        for(ITestNGMethod m : tr.getBeforeSuiteMethods()){
          beforeSuiteMethods.put(m.getMethod(), m);
        }

        for(ITestNGMethod m : tr.getAfterSuiteMethods()){
          afterSuiteMethods.put(m.getMethod(), m);
        }
      }
    {% endhighlight %} 

* Instance variables
- Instance variables, on the other hand, should be declared at the top of the class.
- The important thing is for the instance variables to be declared in one well-known place.

### Example 1

  * Bad
    {% highlight js %}
      class TestSuite implements Test{
        function createTest(theClass, name){
          ...
        }

        function getTestConstructor(theClass) throws NoSuchMethodException{
          ...
        }

        function warning(message){
          ...
        }

        function exceptionToString(t){
          ...
        }

        var fName;

        var fTests = new Vector<Test>(10);

        function TestSuite(){
          ...
        }

        function TestSuite(theClass){
          ...
        }

        function TestStuite(theClass, name){
          ...
        }
      }
    {% endhighlight %}

* Dependent Functions
- If one function calls another, they should be vertically close, and the caller should be above the callee, if at all possible.
- It was better to pass that constant down from the place where it makes sense know it to the place that actually uses it.

### Example 1
  
  * Good
    {% highlight js %}
      class WikiPageResponder implements SecureResponder{
        var page;
        var pageData;
        var pageTitle;
        var request;
        var crawler;

        function makeResponse(context, request) throws Exception{
          var pageName = getPageNameOrDefault(request, "FontPage");
          loadPage(pageName, context);
          if(page==null)
            return notFoundResponse(context, request);
          else
            return makePageResponse(context);
        }

        function getPageNameOrDefault(request, defaultPageName){
          var pageName = request.getResource();
          if(StringUrill.isBlank(pageName))
            pageName = defaultPageName;
          
          return pageName;
        }

        function loadPage(resource, context) throws Exception{
          var path = PathParser.parse(resource);
          crawler = context.root.getPageCrawler();
          crawler.setDeadEndstrategy(new VirtualEnabledPageCrawler());
          page = crawler.getPage(context.root, path);
          if(page != null)
            pageData = page.getData();
        }

        function notFoundResponse(context, request) throws Exception{
          return new NotFoundResponder().makeResponse(context, request);
        }

        function makePageResponse(context) throws Exception{
          pageTitle = PathParser.render(crawler.getFullPath(page));
          var html = makeHtml(context);

          var response = new SimpleResponse();
          response.setMaxAge(0);
          response.setContent(html);
          return response;
        }
      }
    {% endhighlight %} 

* Conceptual Affinity
- The stronger that affinity, the less vertical distance there shold be between them.
- Affinity might be caused because a group of functions perform a similar operation.

### Example 1
  
  * Good
    {% highlight js %}
      class Assert{
        function(message, condition){
          if(!condition)
            fail(message);
        }

        function assertTrue(condition){
          assertTrue(null, condition);
        }

        function assertFalse(message, condition){
          assertTrue(message, !condition);
        }

        function assertFalse(condition){
          assertFalse(null, condition);
        }
      }
    {% endhighlight %} 

## Vertical Ordering
- In general we want function call dependencies to point in the downward direction. That is, a function that is called should be below a function that does the calling.
- We expect the low-level details to come last. This allows us to skim source files, getting the gist from the first few functions, without having to immerse ourselves in the details.

## Horizontal Formatting
- This suggests that we should strive to keep our lines short.

## Horizontal Openness and Density
- We use horizontal white space to associate things that are strongly related and disassociate things that are more weakly related.
- Assignment statements have two distinct and major elements: the left side and the right side.
- I separate arguments within the function call parenthesis to accentuate the comma and show that the arguments are separte.
- Another use for white space is to accentuate the precedence of operators.
- The terms are separated by white space because addition and subtraction are lower precedence.

### Example 1
  
  * Good
    {% highlight js %}
      function(line){
        lineCount++;
        var lineSize = line.length();
        totalChars += lineSize;
        lineWidthHistogram.addLine(lineSize, lineCount);
        recordWidestLine(lineSize);
      }
    {% endhighlight %} 
  
  * Good
    {% highlight js %}
      class Quadratic{
        function root1(a, b, c){
          var determinant = determinant(a, b, c);
          return (-b + Math.sqrt(determinant)) / (2*a);
        }

        function root2(a, b, c){
          var determinant = determinant(a, b, c);
          return (-b - Math.sqrt(determinant)) / (2*a);
        }

        function determinant(a, b, c){
          return b*b - 4*a*c;
        }
      }
    {% endhighlight %} 

## Horizontal Alignment
  - Nowadays I prefer unaligned declarations and assignments, because they point out an important deficiency.

### Example 1
  
  * Bad
    {% highlight js %}
      function(s,
               context) throws Exception
      {
        this.context =            context;
        socket =                  s;
        input =                   s.getInputStream();
        output =                  s.getOutputStream();
        requestParsingTimeLimit = 10000;
      }
    {% endhighlight %}

  * Good
    {% highlight js %}
      function(s, context) throws Exception
      {
        this.context = context;
        socket = s;
        input = s.getInputStream();
        output = s.getOutputStream();
        requestParsingTimeLimit = 10000;
      }
    {% endhighlight %}

## Indentation
  - There is information that pertains to the file as a whole, to the individual classes within the file, to the methods within the classes, to the blocks within the methods, and recursively to the blocks within the blocks.
  - To make this hierarchy of scopes visible, we indent the lines of source code in proportion to their position in the hierarchy.
  - Without indentation, programs would be virtually unreadable by humans.

### Example 1
  
  * Bad
    {% highlight js %}
      function FitNesseServer implements SocketServer { FitNessContext context; constructor(context) { this.context = context; } function serve(s) { serve(s, 10000); } function serve(s, requestTimeout) { try { FitNesseExpediter sender = new FitNesseExpediter(s, context);
      sender.setRequestParsingTimeLimit(requestTimeout); sender.start()}
      catch(e) { e.printStackTrace(); } } }
    {% endhighlight %}

  * Good
    {% highlight js %}
      function FitNesseServer implements SocketServer { 
        FitNessContext context; 
        
        constructor(context) { 
          this.context = context; 
        } 
        
        function serve(s) { 
          serve(s, 10000); 
        } 
        
        function serve(s, requestTimeout) { 
          try { 
            FitNesseExpediter sender = new FitNesseExpediter(s, context);
            sender.setRequestParsingTimeLimit(requestTimeout); sender.start()
          }
          catch(e) { 
            e.printStackTrace(); 
          } 
        } 
      }
    {% endhighlight %}
  
* Breaking Indentation
  - It is sometimes tempting to break the indentation rule for short `if` statemtns, short `while` loops, or short functions.
  - I avoid colapsing scopes down to one line.

### Example 1
  
  * Bad
    {% highlight js %}
      class CommentWidget extends TextWidget {
        REFEXP = "^#[^\r\n]*(?:(?:\r\n)|\n\r)?";

        constructor(parent, text) { super(parent, text); }
        function render() throws Exception { return ""; }
      }
    {% endhighlight %}

  * Good
    {% highlight js %}
      class CommentWidget extends TextWidget {
        REFEXP = "^#[^\r\n]*(?:(?:\r\n)|\n\r)?";

        constructor(parent, text) { 
          super(parent, text); 
          }

        function render() throws Exception { 
          return ""; 
          }
      }
    {% endhighlight %}

## Dummy Scopes
  - I make sure that the dummy body is properly indented and surrounded by braces.
  - Unless you make that semicolon visible by indenting it on it's own line, it's jut too hard to see.

### Example 1
  
  * Good
    {% highlight js %}
      while (dis.read(buf, 0, readBufferSize) != -1 )
        ;
    {% endhighlight %}

## Team Rules
  - A team of developers should agree upon a single formatting style, and then every member of that team should use that style.
  - Remember, a good software system is composed of a set of documents that read nicely.

# Chapter 6: Objects and Data Structures

## Data Abstraction
  - The methods enforce an access policy.
  - Hiding implementation is about abstraction.
  - It exposes abstract interfaces that allow its users to manipulate the essence of the data, without having to know its implementation.
  - In the abstract case you have no clue at all about the form of the data.
  - We do not want to expose the details of our data. Rather we want to express our data in abstract terms.

### Example 1
  
  * Bad
    {% highlight js %}
      class Point{
        var x;
        var y;
      }
    {% endhighlight %}

  * Good
    {% highlight js %}
      interface Point{
        getX(): double;
        getY(): double;
        setCartesian(x, y): void;
        getR(): double;
        getTheta(): double;
        setPolar(r, theta): void;
      }
    {% endhighlight %}

### Example 2
  
  * Bad
    {% highlight js %}
      interface Vehicle{
        getFuelTankCapacityInGallons(): double;
        getGallonsOfGasoline(): double;
      }
    {% endhighlight %}

  * Good
    {% highlight js %}
      interface Vehicle{
        getPercentFuelRemaining(): double;
      }
    {% endhighlight %}

## Data/Object Anti-Symmetry
  - Objects hide their data behind abstractions and expose functions that perate on that data.
  - Data structure espose their data and have no meaningful functions.
  - Procedural code makes it hard to add new data structures because all the functions must change.
  - OO code makes it hard to add new functions because all the classes must change.

### Example

  * Procedural
  {% highlight js %}
      class Square{
        Point topLeft;
        double side;
      }

      class Rectangle{
        Point topLeft;
        double height;
        double width;
      }

      class Circle{
        Point center;
        double radius;
      }

      class Geometry{
        double PI = 3.141592653589793;

        function double area(shape) throws NoSuchShapeException{
          if(shape instanceof Square){
            Square s = (Square) shape;
            return s.sie * s.side;
          }
          else if(shape instanceof Rectangle){
            Rectangle r = (Rectangle) shape;
            return r.height * r.width;
          }
          else if(shape instanceof Circle){
            Circle c = (Circle) shape;
            return PI * c.radius * c.radius;
          }
          throw new NoSuchShapeException();
        }
      }
    {% endhighlight %}
  
  * Polymorphic
  {% highlight js %}
      class Square implements Shape{
        Point topLeft;
        double side;

        function double area(){
          return side * side;
        }
      }

      class Rectangle implements Shape{
        Point topLeft;
        double height;
        double width;

        double area(){
          return height * width;
        }
      }

      class Circle implements Shape{
        Point center;
        double radius;

        function double area(){
          return PI * radius * radius;
        }
      }
    {% endhighlight %}

## The Law of Demeter
  - A method f of a class C should only call the methods these: 
    - C
    - An object created by f
    - An Object passed as an argument to f
    - An Object held in an instance variable of C

### Example

  * Bad
  {% highlight js %}
    string outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
  {% endhighlight %}

## Train Wrecks
  - It is usually best to split them up.
  - This issue would be a lot less confusing if data structures simply had public variables and no functions, whereas objects had private variables and public functions.

### Example

  * Good
  {% highlight js %}
    Options opts = ctxt.getOptions();
    File scratchDir = opts.getScratchDir();
    string outputDir = scratchDir.getAbsolutePath();
  {% endhighlight %}

  * Good
  {% highlight js %}
    string outputDir = ctxt.options.scratchDir.absolutePath;
  {% endhighlight %}

## Hybrids
  - They have functions that do significant things, and they also have either public variables or public accessors and mutators that, for all intents and purposes, make the private variables public, tempting other external functions to use those variables the way a procedural program would use a data structure.
  - Such hybrids make it hard to add new functions but also make it hard to add new data structures.

## Hiding Structure
  - We should not be asking it about its internals.

### Example

  * Good
  {% highlight js %}
    BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
  {% endhighlight %}

## Data Transfer Objects
  - The quintessential form of a data structure is a class with public variables and no functions.
  - DTOs are very useful structures, especially when communication with databases or parsing messages from sockets, and so on.

## Active Record
  - They are data structures with public variables; but they typically have navigational methods like `save` and `find`.

# Chapter 7: Error Handeling

## Use Exceptions Rather Than Return Codes
  - The caller must check for errors immediately after the call.
  - For this reason it is better to throw an exception when you encounter an error.
  - The code is better because two concerns that were tangled, the algorithm for device shutdown and error handling, are now separated.

### Example
  * Bad
  {% highlight js %}
  class DeviceController {
    funtion sendShutDown(){
      DeviceHandle handle = getHandle(DEV1);
      if(handle != DeviceHandle.INVALID){
        retrieveDeviceRecord(handle);
        if(record.getStatus() != DEVICE_SUSPENDED){
          pauseDevice(handle);
          clearDeviceWorkQueue(handle);
          closeDevice(handle);
        }
        else{
          logger.log("Device suspended. Unable to shut down");
        }
      }
      else{
        logger.log("Invalid handle for: " + DEV1.toString());
      }
    }
  }
  {% endhighlight %}

  * Good 
  {% highlight js %}
  class DeviceController{
    function sendShutDown(){
      try{
        tryToShutDown();
      }
      catch(DeviceShutDownError e){
        logger.log(e);
      }
    }
  }
  {% endhighlight %}

## Write Your `Try-Catch-Finally` Statement First
  - When you execute code in the `try` portion of a `try-catch-finally` statement, you are stating that execution can abort at any point and then resume at the `catch`.
  - Your `catch` has to leave your program in a consistent state, no matter what happens in the `try`.

## Use Unchecked Exceptions
  - Given that the purpose of exceptions is to allow you to handle errors at a distance, it is a shame that checked exceptions break encapsulation in this way.

## Provide Context with Exceptions
  - Each exception that you throw should provide enough context to determine the source and location of an error.
  - Create informative error messages and pass them along with your exceptions.

## Define Exception Classes in Therms of a Caller's Needs
  - When we define exception classes in an pplication, our most important concern should be how they are cought.
  - Wrapping also makes it easier to mock out third-party calls when you are testing your own code.
  - One final advantage of wrapping is that you aren't tied to a particular vendor's API design choices.

### Examples
  * Bad 
  {% highlight js %}
  ACMEPort port = new ACMEPort(12);

  try{
    port.open();
  }
  catch(DeviceResponseException e){
    reportPortErroe(e);
    logger.log("Device response exception", e);
  }
  catch(ATM1212UnlockedException e){
    reportPortError(e);
    logger.log("Unlock exception", e);
  }
  catch(GMXError e){
    reportPortError(e);
    logger.log("Device response exception");
  }
  finally{...}
  {% endhighlight %}

  * Good
  {% highlight js %}
  LocalPort port = new LocalPort(12);
  try{
    port.open();
  }
  catch(PortDeviceFailure e){
    reportError(e);
    logger.log(e.getMessage(), e);
  }
  finally {...}

  class LocalPort(){
    ACMEPort innerPort;

    public LocalPort(int portNumber){
      innerPort = new ACMEPort(portNumber);
    }

    function open(){
      try{
        innerport.open();
      }
      catch(DeviceResponseException e){
        throw new PortDeviceFailure(e);
      }
      catch(ATM1212UnlockedException e){
        throw new PortDeviceFrailure(e);
      }
      catch(GMXError e){
        throw new PortDeviceFailure(e);
      }
    }
  }
  {% endhighlight %}

## Define the Normal Flow
  - You create a class or configure an object so that it handles a special case for you.
  - When you do, the client code doesn't have to deal with exceptional behavior.
  - That behavior is encapsulated in the special case object.

  ### Example
  * Bad
  {% highlight js %}
  try{
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
  }
  catch(MealExpensesNotFound e){
    m_total += getMealPerDiem();
  }
  {% endhighlight %}

  * Good
  {% highlight js %}
  MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
  m_total += expenses.getTotal();

  class PerDiemMealExpenses implements MealExpenses{
    function getTotal(){
      // return the per diem default
    }
  }
  {% endhighlight %}

## Dont't Return Null
  - If you are tempted to return null from a method, consider throwing an exception or returning a Special Case object instead.
  - If you are calling a null-returning method from a third-party API, consider wrapping that method with a method that either throws an exception or returns a special case object.
  - If you code this way, you will minimize the chance of `NullPointerExceptions` and your code will be cleaner.

## Dont't Pass Null
  - In most programming languages there is no good way to deal with a `null` that is passed by a caller accidentally.
  - Because this is the case, the rational approach is to forbid passing `null` by default.
  - When you do, you can code with the knowledge that a `null` in an argument list is an indication of a problem, and end up with far fewer careless mistaken.

# Chapter 8: Boundaries

## Using Third-Party Code
  - If you use a boundary interface, keep it inside the class, or close family of classes, where it is used.
  - Avoid returning it from, or accepting it as an argument to, public APIs.

## Exploring and Learning Boundaries
  - We could write some tests to explore our understanding of the third-party code.
  - Jim Newkirk calls such tests `learning tests`.

## Learning Test Are Better Than Free
  - The learning tests were precise experiments that helped increase our understanding.
  - Not only are learning tests free, they have a positive return on investment.
  - Learning tests verify that the third-party packages we are using work the way we expect them to.
  - Whether you need the learning provided by the learning tests or not, a clean boundary should be supported by a set of outbound tests that exercise the interface the same way the production code does.

## Using Code That Does Not Yet Exist
  - There are often places in the code where our knowledge seems to drop off the edge.
  - Sometimes what is on the other side of the boundary is unknowable.
  - Sometimes we choose to look no farther than the boundary.

## Clean Boundaries
  - Good software designs accommodate change without huge investments and rework.
  - When we use code that is out of our control, special care must be taken to protect our investment and make sure future change is not too costly.
  - Code at the boundaries needs clear separation and tests that define expectations.
  - We should avoid letting too much of our code know about the third-party particulars.
  - Either way our code speaks to us better, promotes internally consistent usage across the boundary, and has fewer maintenance points when the third-party code changes.

# Chapter 9 : Unit Tests
  - The Agile and TDD(Test Driven Development) movements have encouraged many programmers to write automated unit tests, and more are joining their tanks every day.

## The Three Laws of TDD
  - You may not write production code until you have written a failing unit test.
  - You may not write more of a unit test than is sufficient to fail, and not compiling is failing.
  - You may not write more production code than is sufficient to pass the currently failing test.

## Keeping Test Clean
  - The dirtier the tests, the harder they are to change.
  - Test code is just as important as production code.
  - It must be kept as clean as production code.

## Tests Enable the -ilities
  - It is `unit tests` that keep our code flexible, maintainable and reusable.
  - Having an automated suite of unit tests that cover the production code is the key to keeping your design and architecture as clean as possible.
  - Tests enable all the -ilities, because tests enable <b>change</b>.

## Clean Tests
  - Readability is perhaps even more important in unit tests than it is in production code.
  - The same thing that makes all code readable: clarity, simplicity, and density of expression.
  - Notice that the vast majority of annoying detail has been eliminated.
  - The tests get right to the point and use only the data types and functions that they truly need.

### Example

* Bad
{% highlight js %}
function testGetPageHieratchyAsXml() throws Exception{
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response = 
    (SimpleResponse) responder.makeResponse(
      new FitNesseContext(root), request);
  string Xml = response.getContent();

  assertEquals("test/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}

function testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throw Exception{
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  
  Responder responder = new SerializedPageResponder();
  SimpleResponse response = 
    (SimpleResponse) responder.makeResponse(
      new FitNesseContext(root), request);
  string xml = response.getContext();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}

function testGetDataAsHtml() throws Exception{
  crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

  request.setResource("TestPageOne");
  request.addInput("type", "data");

  Responder responder = new SerializedPageResponder();
  SimpleResponse response = 
    (SimpleResponse) responder.makeResponse(
      new FitNesseContext(root), request);
  string xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("test page", xml);
  assertSubString("<Test>", xml);
}
{% endhighlight %}

* Good
{% highlight js %}
function testGetPageHierarchyAsXml() throws Exception{
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
}

function testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception{
  WikiPage page = makePage("PageOne");
  makePage("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  );

  assertResponseDoesNotContain("SymPage");
}

function testGetDataAsXml() throw Exception{
  makePageWithContent("TestPageOne", "test page");

  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test>");
}
{% endhighlight %}

## Domain-Specific Testing Language
  - They are a testing language that programmers use to help themselves to write their tests and to help those who must read those tests later on.
  - This testing API is not designed up front; rather it evolves from the continued refactoring of test code that has gotten too tainted by obfuscating detail.

## A Dual Standard
  - After all, it runs in a test environment, not a production environment, and those two environment have very different needs.
  - There are things that you might never do in a production environment that are perfectly fine in a test environment.

### Example 1

* Bad
{% highlight js %}
@Test
function turnOnLoTempAlarmAtThreashold() throws Exception(){
  hw.setTemp(WAY_TOO_COLD);
  controller.tic();
  assertTrue(hw.heaterState());
  assertTrue(hw.blowerState());
  assertFalse(hw.coolerState());
  assertFalse(hw.hiTempAlarm()));
  assertTrue(hw.lowTempAlarm()));
}
{% endhighlight %}

* Good
{% highlight js %}
@Test
function turnOnLoTempAlarmAtThreshold() throws Exception{
  wayTooCold();
  assertEquals("HBchL", hw.getState());
}
{% endhighlight %}

### Example 2

* Bad
{% highlight js %}
@Test
function turnOnCoolerAndBlowerIfTooHot() throws Exception{
  tooHot();
  assertEquals("hBChl", hw.getState());
}

@Test
function turnOnHeaterAndBlowerIfTooCold() throws Exception{
  tooHot();
  assertEquals("HBchl", hw.getState());
}

@Test
function turnOnHiTempAlarmAtThreshold() throws Exception{
  tooHot();
  assertEquals("hBCHl", hw.getState());
}

@Test
function turnOnLoTempAlarmAtThreshold() throws Exception{
  tooHot();
  assertEquals("HBchL", hw.getState());
}
{% endhighlight %}

* Good
{% highlight js %}
function getState(){
  string state = "";
  state += heater ? "H" : "h";
  state += blower ? "B" : "b";
  state += cooler ? "C" : "c";
  state += hiTempAlarm ? "H" : "h";
  state += loTempAlarm ? "L" : "l";
  return state;
}
{% endhighlight %}

## One Assert per Test
  - `given-when-then` makes the tests even easier to read.
  - We can eliminate the duplication by using the template method pattern and putting the `given/when` parts in the base class, and the `then` parts in different derivatives.
  - Or we could create a completely separate test class and put the `given` and `when` parts in the `@Before` function, and the `when` parts in each `@Test` function. 

### Example

* Good
{% highlight js %}
function testGetPageHierarchyAsXml() throws Exception{
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

  whenRequestIsIssued("root", "type:pages");

  thenResponseSchouldBeXML();
}

function testGetPageHierarchyHasRightTags() throws Exception{
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

  whenRequestIsIssued("root", "type:pages");

  thenResponseShouldContain(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  );
}
{% endhighlight %}

## Single Concept per Test
  - Merging them all together into the same function forces the reader to figure out why each each section is there and what is being tested by that section.
  - So probably the best rule is that you should minimize the number of asserts per concept and test just one concept per test function.

## Example

{% highlight js %}
function testAddMonths(){
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

  SerialDate d2 = SerialDate.addMonths(1, d1);
  assertEquals(30, d2.getDayOfMonth());
  assertEquals(6, d2.getMonth());
  assertEquals(2004, d2.getYYYY());

  SerialDate d3 = SerialDate.addMonths(2, d1);
  assertEquals(31, d3.getDayOfMonth());
  assertEquals(7, d3.getMonth());
  assertEquals(2004, d3.getYYYY());

  SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1));
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
}
{% endhighlight %}

## F.I.R.S.T
  - First : Tests should be fast.
  - Independent : Test should not depend on each other.
  - Repeatable : Test should be repeatable in any environment.
  - Self-Validating: The tests should have a boolean output.
  - Timely : The tests need to be written in a timely fashion.
