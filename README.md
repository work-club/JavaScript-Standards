JavaScript-Standards
====================

The front-end development teams JavaScript standards document.

* [Overview](#overview)
    * [What this guide is for](#what-this-guide-is-for)
    * [What this isn't for](#what-this-isnt-for)
    * [Assumptions](#assumptions)
* [File structure](#file-structure)
* [Conventions](#conventions)
    * [Naming conventions](#naming-conventions)
    * [Variables](#variables)
    * [Strings](#strings)
    * [Numbers](#numbers)
    * [Type coercion](#type-coercion)
    * [Objects](#ojects)
    * [Arrays](#arrays)
    * [Functions](#functions)
    * [Scope](#scope)
    * [Events](#events)
    * [Context binding](#context-binding)
    * [Comments](#comments)
    * [Operators](#operators)
* [Patterns](#patterns)
    * [Separation of concerns](#separation-of-concerns)
    * [Single responsibility principle](#single-responsibility-principle)
    * [Single point of entry](#single-point-of-entry)
    * [RequireJS](requirejs)
    * [Browsers/vendor testing](#browsers-vendor-testing)

## Overview

This document has been put together by the front-end development team at Work Club, a London based digital agency. This document is a reference for all developers in the team to follow. This document is subject to change and improvements.

### What this guide is for

This document is to be followed by all developers writing JavaScript on a project, regardless of size or complexity. It's intended to be followed to provide consistency across all projects and developers. It will point out good patterns and anti-patterns and explain why these should be used or ignored.

### What this isn't for

This guide will not dictate what libraries or frameworks you should use in a project as these will vary on the project and size. Nor will this guide teach your JavaScript.

### Assumptions

This guide will assume you are proffient in JavaScript and familiar with many of it's quirks. It will assume you have a rough understanding of problems with closures and scope and help you work around these. For more information on these problems and other common pitfalls, please refer to the [JavaScript Bonsai Garden](http://bonsaiden.github.io/JavaScript-Garden) and [Douglas Crockfords The Good Parts](http://shop.oreilly.com/product/9780596517748.do).

## File structure

As with our [CSS guidelines](https://github.com/work-club/CSS-Coding-Standards), structure of the code is important. All scripts should be within the `scripts` folders and neatly organised into sub-folders (e.g. Modules, Components, Utilities, Controllers, Routes etc). The only file that should exist at the root level of `scripts` is `app.js`.

All files should be lowercase with dashes for spaces.

## Convention
The following is a list of conventions for formatting, structure and overal code approach.
    
### Formatting

Be sure to use the following formatting rules with all block statements:

  * ***Always*** use a curley brace on all block statements
  * return 1 line after each brace
  * indent 4 spaces inside each new closure or statement
  * leave one blank return after each large logical block of code
  * return lines longer than 80 characters

Good:
```
  if(x === userInput) {
    doSomething();
  }
  
  function myFunction(){
    getValue();
  }
```

Bad:
```
  if(x === userInput)
    doSomething();
  
  function myFunction(){ getValue(); }
```
    
### Naming conventions
Use Pascal Case for Classes:
```
  var myClass = new MyClass();

```

Use camelCase for objects (including modules, singletons) and functions:
```
  var utilsObject = {
    property: 'name'
  };
  
  function myFunction() {
  
  };
  
  myFunction();
  utilsObject.property;
```

### Variables
When defining a variable, place each new variable on a new line with a `var` keyword for each variable. When defining a new variable, you ***must*** use a `var` keyword to prevent global variables being created.

Good: 
```
  var exampleVar = "exampleValue";
  var favouriteColour = "blue";
  
```

Bad:
```
  var example = "exampleValue",
      favouriteColour = "blue";
      
```

Because of variable hoisting, variables should be defined as close to the top of closure as possible. ***Remember***: only the declaration is hoisted and not the assignment.

### Strings
Use double quotes `"` for all strings and statements. Use a backslash `\` to escape all double quotes in strings.

### Numbers
Where possible, do not wrap numbers in double quotes. This is to avoid type coercion. When dealing with numbers that have been wrapped in quotes, attempt to coerce them back to numbers.

### Type coercion

As a rule, JavaScript will take the type of the first value in a statement and attempt to coerce the second value to the same type. This can produce the following results:

```
""           ==   "0"           // false
0            ==   ""            // true
0            ==   "0"           // true
false        ==   "false"       // false
false        ==   "0"           // true
false        ==   undefined     // false
false        ==   null          // false
null         ==   undefined     // true
" \t\r\n"    ==   0             // true
```

To avoid this, use strong typing when comparing values in conditions. (The above table is from [Bonsai Garden](http://bonsaiden.github.io/JavaScript-Garden/#types.equality)). Using strong coercion would result in a false in each of those statements.

### Objects

Be sure to use dot notation when referencing object properties. Only use subscript notation when accessing a property via a variable or returning a property from a function:

GOOD:
```
  var myObject = {
    name: "brian",
    age: 30
  };
  
  console.log(myObject.name, myObject.age);
  
  function getProperty(propertyName){
    return myObject[propertyName]
  }
  
  console.log(getProperty("name"));
  console.log(getProperty("age"));
  
```

BAD:
```
  var myObject = {
    name: "brian",
    age: 30
  };
  
  console.log(myObject["name"], myObject["age"]);
```

### Arrays

Use the following pattern to define an array:

GOOD:
```
  var items = [];
```

BAD:
```
  var items = new Array();
```

For more information on why this pattern is preferred, please refer to [this guide](http://bonsaiden.github.io/JavaScript-Garden/#array.constructor).

### Functions

Immediately invoked function expressions (IIFE) should only be used to kick-start an application, and not for grouping functions. Annonymous function expressions should always be assigned to a variable.

When using a function constructor, ***do not*** omit the `new` keyword. This will unintentionally create a global variable and not an instance of the class.

### Scope & Closure

The `this` keyword in JavaScript is very important. The context of `this` changes within each new closure. For example:

```
  console.log(this) /* this == global window object */
  
  var myFunction(){
    console.log(this) /* this == myFunction */
  }
  
  var myObject = {
    init: function(){
      console.log(this) /* this == myObject */
      
      function test(){
        console.log(this) /* this == test() */
      }
    }
  }
```

If you need to reference the scope of another object/closure inside another function, assign the context to a variable named `self`, like so:

GOOD:
```
  var myObject = {
    init: function(){
      var self = this;
      
      this.publicProperty = true;
      
      function test(){
        console.log(self.publicProperty) /* Would log true */
      }
    }
  }
```

BAD:
```
  var myObject = {
    init: function(){
      this.publicProperty = true;
      
      function test(){
        console.log(this.publicProperty) /* Would log undefined */
      }
    }
  }
```

Always use `self` and ***not*** `_this` or `that`.

### Events

### Comments
Use `/* */ ` for all comments, instead of `//`.

### Operators

Use the strict equality operator whenever possible (`===`). This is to prevent problems with coercion of loosely typed variables. 

## Patterns
The following are some general guiding principals of development. They are not meant to inform a general development strategy.

### Separation of concerns

You should be creating a new file for each class or object. The class/object inside this file should be well encapsulated and concerned with only logic required in that class/object. This code should not be tightly coupled to code in another file. Functionality in your class or module should be available via a well thoughout interface to hide all private information.

### Single responsibility principle

If the above section is concerned with code organisation at a class/object level, this section relates to code on a method/function level. Each method/function should be responsible for doing one task and doing it well. The name of the method or function should be representative of what that code is actually doing. If you find that the code is starting to deal with more and more work and the naming is no longer clear, you should break this down into smaller functions with better names for each stage. 

Consider carefully the name of each method, class or object. Names that end in 'er' tend to be ambigous and unclear (stateManager for example does give a clear idea of how that state is being managed and for what purpose).

### Notifications

In order to have neatly organised code it may be possible to implement some form of notification process. Notifications are a way of avoiding functions directly calling other functions. This could take the form of [Pub/Sub](http://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) or more [local notification patterns](hhttp://www.bennadel.com/blog/2283-Keeping-Modules-Decoupled-Using-Signals-And-Mediators.htm).

One module can dispatch a notification that an unrelated module can react to, decoupling the need for each module to be aware of each other.

### Single point of entry

The culmination of the above sections should be a single point of entry. If each of your classes is neatly organised in to its own class, you will need somewhere to instantiate them and start your application/website. This should take the form of a 'single point of entry'. This is a single file, usually named 'app.js' that is responsible for starting the required classes and establishing any required event listeners. This file shouldn't handle any logic directly, just invoke classes.

### RequireJS

The use of a dependency management library such as [RequireJS](http://requirejs.org/) is strongly encouraged. RequireJS allows you to neatly separate your code into smaller files and pull them in as needed. 

### Browsers/vendor testing

Browsers and features should not be tested for directly, instead a more methodical abstraction should be implemented, similar to the [technique used by the BBC](http://responsivenews.co.uk/post/18948466399/cutting-the-mustard). This approach advocates creating a core experience for all users first and then layering on features depending on their availability. 

GOOD:
```
  /* utils.js */
  var featureTests = {
    isGradeA: function(){
      return $(html).hasClass("pushstate") && $(html).hasClass("localstorage")
    },
    isGradeB: function(){
      return $(html).hasClass("pushstate")
    }
  };
  
  /* app.js */
  
  function init(){
    if(utils.isGradeA()){
      /* call a module */
    } else if(utils.isGradeB()) {
      /* call a backup module */
    } else {
      /* rely on core experience */
    }
  }
```

BAD:

```
  /* app.js */
  
  function init(){
    if($(html).hasClass("pushstate") && $(html).hasClass("localstorage")){
      /* call a module */
    } else if($(html).hasClass("pushstate")) {
      /* call a backup module */
    } else {
      /* rely on core experience */
    }
  }
```

The second example is repetitive, messy and makes the assumption that this is the only place we will test for this and that the requirements for this feature will never change. In practice, this pattern would be dependant on a library such as [Modernizr](http://modernizr.com/).
