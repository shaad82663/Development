# JavaScript Interview Questions with Answers

→ For more Step by Step and Interview Q&A Course visit - www.questpond.com  
→ For offline training visit - https://www.stepbystepschools.net/  
→ Stay tuned with Questpond for more updates - t.me/questpond  

## Contents

- Why do we call JavaScript as dynamic language?  
- How does JavaScript determine data types?  
- What is “typeof” function?  
- How to check data type in JavaScript?  
- What are the different datatypes in JavaScript?  
- Explain Undefined Data types?  
- What is Null?  
- Differentiate between Null and Undefined?  
- Explain Hoisting?  
- Are JavaScript initialization hoisted?  
- What are global variables?  
- What are the issues with Global variables?  
- What happens when you declare variable without VAR?  
- What is Use Strict?  
- How to force developers to use Var keyword?  
- How can we handle Global Variables?  
- How can we avoid Global variables?  
- What are Closures?  
- Why do we need Closures?  
- Explain IIFE?  
- What is the use of IIFE?  
- What is name collision in global scope?  
- IIFE vs Normal Function  
- What are design patterns?  
- Which is the most used design pattern in JavaScript?  
- What is module Pattern and revealing module pattern?  
- How many ways are there to create JavaScript objects?  
- How can we do inheritance in JavaScript?  
- What is prototype in JavaScript?  
- Explain Prototype chaining?  
- What is Let Keyword?  
- Are Let variables hoisted?  
- Explain Temporal Dead Zone?  
- Let vs Var  
- Tricky Question around Concatenation  
- Explain class keyword?  
- So with Class Keyword can we say Javascript is OO?  
- What is difference between Class and Normal function?  
- Explain Arrow function?  
- When will you use an Arrow function?  
- Arrow functions vs Normal functions?  
- Does Arrow function have its own this?  
- Explain Synchronous execution?  
- What is a call Stack in JavaScript?  
- What is a blocking call?  
- How to avoid blocking calls?  
- Synch vs Asynch?  
- How can we do Asynch calls in Javascript?  
- Explain threads?  
- Explain Multi-threading?  
- Is JavaScript Multi-threaded?  
- Then how does Settimeout run?  
- What is a WebAPI/Browser API?  
- What is an Event loop and callback queue?  
- What is the output of the below code (Testing Eventloop and CallBack Queue)?  

## Why do we call JavaScript as dynamic language?

JavaScript is a dynamic language because data types of variables can change during runtime.

## How does JavaScript determine data types?

JavaScript determines data types based on the value assigned (e.g., 10 is a number, "Hello" is a string).

## What is “typeof” function?

It returns the data type of a value or variable as a string.

## How to check data type in JavaScript?

Use the `typeof` operator.  
**Example**:  
```javascript
let x = 100;  
console.log(typeof x);  // "number"
```

## What are the different datatypes in JavaScript?

There are 8 data types: String, Number, Boolean, Null, Undefined, BigInt, Symbol, Object (divided into primitive and non-primitive).

## Explain Undefined Data types?

Undefined means a variable is declared but not assigned a value.  
**Example**:  
```javascript
let x;  
console.log(x);  // undefined
```

## What is Null?

Null represents intentional absence of any value (not zero or empty, just no value).

## Differentiate between Null and Undefined?

- **Undefined**: Variable declared but no value assigned.  
- **Null**: Explicitly set to indicate absence of value.

## Explain Hoisting?

Hoisting moves variable and function declarations to the top of their scope during compilation.  
**Example**:  
```javascript
console.log(x);  // undefined  
var x = 5;
```

## Are JavaScript initialization hoisted?

No, only declarations are hoisted; initializations remain in place (value is undefined until assigned).

## What are global variables?

Variables accessible throughout the webpage or document.

## What are the issues with Global variables?

They make debugging hard, cause naming conflicts, and pollute the global scope.

## What happens when you declare variable without VAR?

It becomes a global variable.  
**Example**:  
```javascript
function test() { x = 5; }  
test();  
console.log(x);  // 5 (global)
```

## What is Use Strict?

It enforces stricter parsing and error handling, like requiring `var` for variables.

## How to force developers to use Var keyword?

Use `"use strict";` at the top of the script or function to throw errors for undeclared variables.

## How can we handle Global Variables?

Organize them in namespaces or use module patterns to minimize issues.

## How can we avoid Global variables?

Use closures, IIFE, or modules to encapsulate variables.

## What are Closures?

A closure is a function that retains access to its outer scope's variables even after the outer function finishes.  
**Example**:  
```javascript
function outer() {  
  let x = 10;  
  return function inner() { return x++; };  
}  
let inc = outer();  
console.log(inc());  // 10  
console.log(inc());  // 11
```

## Why do we need Closures?

For encapsulation, abstraction, and creating stateful functions without globals.

## Explain IIFE?

Immediately Invoked Function Expression: An anonymous function that executes immediately.  
**Example**:  
```javascript
(function() { console.log("IIFE executed"); })();
```

## What is the use of IIFE?

To create a private scope, avoid global pollution, and prevent name collisions.

## What is name collision in global scope?

When two variables or functions share the same name, causing conflicts.

## IIFE vs Normal Function

- **Normal Function**: Named, reusable, can cause name collisions.  
- **IIFE**: Anonymous, immediate execution, no name collisions.

## What are design patterns?

Proven architectural solutions for common problems (e.g., singleton for single instances).

## Which is the most used design pattern in JavaScript?

Module/Revealing Module Pattern.

## What is module Pattern and revealing module pattern?

Module Pattern uses closures for private state; Revealing Module exposes public methods.  
**Example (Revealing)**:  
```javascript
let module = (function() {  
  let privateVar = 0;  
  function privateFunc() { return privateVar++; }  
  return { get: privateFunc };  
})();  
console.log(module.get());  // 0
```

## How many ways are there to create JavaScript objects?

Four: Literals, `Object.create()`, constructors, ES6 classes.

## How can we do inheritance in JavaScript?

Using prototypes (prototypal inheritance).  
**Example**:  
```javascript
function Parent() {}  
function Child() {}  
Child.prototype = new Parent();
```

## What is prototype in JavaScript?

An object that provides shared properties/methods to other objects via inheritance.

## Explain Prototype chaining?

Searching for properties/methods up the prototype chain until null.  
**Example**: If `obj.prop` not found, checks `obj.__proto__.prop`, and so on.

## What is Let Keyword?

Declares block-scoped variables.

## Are Let variables hoisted?

Yes, but not initialized (ReferenceError if accessed before declaration).

## Explain Temporal Dead Zone?

Period where `let` variables are hoisted but uninitialized, causing errors on access.

## Let vs Var

- **Var**: Function-scoped, initialized as undefined.  
- **Let**: Block-scoped, not initialized until declaration.

## Tricky Question around Concatenation

**Code**:  
```javascript
console.log("10" + "10");  // "1010"  
console.log(10 + 10);      // 20  
console.log(1 + 1 + "4");  // "24"
```

## Explain class keyword?

Syntactic sugar for constructor functions and prototypes.  
**Example**:  
```javascript
class Person {  
  constructor(name) { this.name = name; }  
}
```

## So with Class Keyword can we say Javascript is OO?

No, JavaScript remains prototype-based; classes are sugar over functions.

## What is difference between Class and Normal function?

- **Class**: Represents entities, requires `new`, avoids global `this` pollution.  
- **Function**: General-purpose, stateless, can be called without `new`.

## Explain Arrow function?

Concise syntax for functions.  
**Example**:  
```javascript
const add = (a, b) => a + b;
```

## When will you use an Arrow function?

For short, non-reusable functions in callbacks (e.g., `map`, `filter`), where lexical `this` is needed.

## Arrow functions vs Normal functions?

- **Normal**: Own `this`, reusable.  
- **Arrow**: Lexical `this`, concise for predicates.

## Does Arrow function have its own this?

No, it borrows from the outer scope.

## Explain Synchronous execution?

Code executes sequentially; each statement waits for the previous to finish.

## What is a call Stack in JavaScript?

A stack tracking function calls (LIFO execution).

## What is a blocking call?

A call that halts further execution until complete.

## How to avoid blocking calls?

Use async patterns like callbacks, promises, or async/await.

## Synch vs Asynch?

- **Sync**: Sequential, blocking.  
- **Async**: Non-blocking, concurrent execution.

## How can we do Asynch calls in Javascript?

Via Web APIs (e.g., `setTimeout`), Promises, or Web Workers.

## Explain threads?

A thread is an execution context (code + data) run by the CPU.

## Explain Multi-threading?

Running multiple threads in parallel.

## Is JavaScript Multi-threaded?

No, it's single-threaded.

## Then how does Settimeout run?

It runs in the browser's Web API, not the JS call stack.

## What is a WebAPI/Browser API?

Interfaces for browser features (e.g., timers, HTTP, geolocation).

## What is an Event loop and callback queue?

Event loop checks if call stack is empty, then moves callbacks from queue to stack. Queue holds async task completions.

## What is the output of the below code (Testing Eventloop and CallBack Queue)?

**Code**:  
```javascript
function DisplayMessage() { console.log("Message is displayed"); }  
function fun1() {  
  console.log("Fun1");  
  setTimeout(() => DisplayMessage(), 0);  
  fun2();  
}  
function fun2() { console.log("Fun2"); }  
fun1();
```  
**Output**:  
Fun1  
Fun2  
Message is displayed  
(Callback queue has lower priority than call stack.)