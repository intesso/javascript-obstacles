title: javascript obstacles
author:
  name: Andi Neck
  twitter: andineck
  url: http://intesso.com
controls: true
progress: true
style: style.css
output: index.html

--
# javascript-obstacles


> http://intesso.github.io/javascript-obstacles

feedback and pull requests welcome on [GitHub ](https://github.com/intesso/javascript-obstacles)[![github](img/github.png)](https://github.com/intesso/javascript-obstacles)

*navigate with left, right keys*

--
### js obstacles

> Functions are first class citizens

```js
function test () {};
typeof test === 'function';
typeof test.prototype === 'object';

test.one = 1; // functions can have properties
console.log(test.one); // -> 1
```

--
### js obstacles

> Object properties can be named almost everything

*but you can only access them with strings in square brackets*

```js
var obj = {};
obj.a = 'A';
console.log(obj.a) // -> 'A'

// same as
obj['a'] = 'A';
console.log(obj['a']) // -> 'A'

// names that DON'T work with dot notation 
obj.1 = 1; // Uncaught SyntaxError: Unexpected number
obj.a-b = 'ab'; // Uncaught ReferenceError: b is not defined
obj.@#!? = 'wat?'; // SyntaxError: Invalid or unexpected token
obj.┬──┬ ノ( ゜-゜ノ) = 'mood'; // SyntaxError: Invalid or unexpected token

// but work as strings in square brackets
obj['1'] = 1;
obj['a-b'] = 'ab';
obj['@#!?'] = 'wat?';
obj['┬──┬ ノ( ゜-゜ノ)'] = 'mood';
```

--
### js obstacles

> Callback Functions

```js
// dom stuff
var body = document.querySelector('body');

// event listener
body.addEventListener('click', myCallback);

function myCallback(e) {
  confirm("DONT CLICK!");
}

// same in green, but with nested function
body.addEventListener('click', function myCallback(e) {
  confirm("DONT CLICK!");
});
```

--
### js obstacles

> [Callback Hell](http://callbackhell.com/) is when your code looks like this

```js
function add(i, callback) {
  i = i+1;
  setTimeout(function(){
    console.log('count', i);
    callback(i);
  }, 100);
}

add(1, function(i){
  add(i, function(i) {
    add(i, function(i){
      add(i, function(i){
        console.log('done', i);
      });
    });
  });
});
```

**Ways out: name and reference functions, use libraries like [async](https://github.com/caolan/async), [run-series](https://www.npmjs.com/package/run-series), [run-parallel](https://www.npmjs.com/package/run-parallel) and modularize your stuff**

--
### js obstacles

> Prototypal Inheritance

```js
// constructor function
function Hello (announcement) {
    if (!(this instanceof Hello)) return new Hello(announcement);
    this.announcement = announcement;
}

// method
Hello.prototype.world = function () {
    console.log(this.announcement, 'hello world');
};

// instantiating an object
var hello = Hello(); // or new Hello();
hello.world(); // prints out: hello world

// method added (on the fly)
Hello.prototype.ch = function () {
    console.log(this.announcement, 'sali duu');
};
hello.ch(); // prints out: sali duu
```

--
### js obstacles

> problem with `this`

```js
// constructor function
function Hello (announcement) {
    if (!(this instanceof Hello)) return new Hello(announcement);
    this.announcement = announcement;
}
// wrong method
Hello.prototype.world = function () {
    setTimeout(function(){
        // !!! this is `undefined` in the inner function
        console.log(this.announcement, 'hello world');
    }, 1000);
};
// correct method
Hello.prototype.world = function () {
    // save `this` to use in inner function
    var self = this;
    setTimeout(function(){
        // works as expected
        console.log(self.announcement, 'hello world');
    }, 1000);
};
Hello('I SAY:').world();
```

--
### js obstacles

> Grasp Functional Beauty

```js
function repeater (who, what) {
  var text = 'said';
  // returning the inner function creates a closure at run time
  return function repeat() {
    console.log(who, text, what);
  }
}

var i = repeater('I','hello');
var you = repeater('you','world');

i();  // I said hello
you(); // you said world

repeater('everyone', 'yay!')();  //everyone said yay!
```

--
### js obstacles

> problem with `for` loop

```js
// works as expected
for(var i = 0; i < 10; i++) {
  console.log('i', i);
  //0 1 2  ... 9
} // **only use for loop for synchronous actions**

// hmmm not really what we wanted
for(var i = 0; i < 10; i++) {
  setTimeout(function (){console.log('i', i)}, 100);
  //10 10 10  ... 10
} // **don't use for loop for asynchronous actions**
```

--
### js obstacles

> solutions to `for` loop problems

```js
// Array.forEach function
var arr = [];
for(var i = 0; i < 10; i++) {
  arr.push(i);
}
arr.forEach(function(i){
  setTimeout(function (){console.log('i', i)}, 100);
  //0 1 2  ... 9
});  // **Array.forEach works well for asynchronous actions**

// anonymous immediately invoked function expression (IIFE)
for(var i = 0; i < 10; i++) {
  (function(i){
    setTimeout(function (){console.log('i', i)}, 100);
    //0 1 2  ... 9
  })(i);
}
```

--
### js obstacles

> Variable Hoisting

*Variables can be used, before they were declared. Variable declarations (not initialization) are moved to the top of the function or global scope (hoisted). It is therefore recommended to declare the variables at the top of the function or global scope*
```js
var myvar = 'hello outer';
function myfunction () {
  console.log(myvar); // undefined
  var myvar = 'hello inner';
}
myfunction();
```
*myfunction above is equivalent to this:*
```js
function myfunction () {
  var myvar; // declaration
  console.log(myvar); // undefined
  myvar = 'hello inner'; // initializations
}
```

--
### js obstacles

> Function Hoisting

*Function definitions are also hoisted* 

*Function declarations are NOT hoisted: `var myfunc = function(){}`*

```js
hoisted(); // works
notHoisted(); // TypeError: notHoisted is not a function
noHoist(); // ReferenceError: noHoist is not defined

function hoisted(){
  console.log('hoisted');
}

var notHoisted = function noHoist(){
  console.log('notHoisted');
}
```

--
### js obstacles

> NO Block Scope

```js
var x = 'hallo';
console.log(x); // 'hallo'
if (true) { // block
	var x = 'welt'; // overwrites var x outside block
	console.log(x); // 'welt'
}
console.log(x); // 'welt'
```

--
### js obstacles

> but Function Scope

```js
var x = 'hallo';
console.log(x); // 'hallo'
function welt () { // function scope
    if (true) {
  	var x = 'welt'; // own declaration of x inside function
  	console.log(x); // 'welt'
  }
}
welt();
console.log(x); // 'hallo'
```

**JavaScript has function scope (no block scope)**

--
### js obstacles

> Function Scope, same in green

```js
var x = 'hallo';
console.log(x); // 'hallo'
(function () { // function scope
    if (true) {
  	var x = 'welt'; // own declaration of x inside function
  	console.log(x); // 'welt'
  }
})();

console.log(x); // 'hallo'
```

*with immediately invoked function expression (IIFE)*

--
### js obstacles

> Immediately-Invoked Function Expression

```js
// functions can be declared in brackets (function(){})
// and be Immediately-Invoked with following function call ()
(function () {
  console.log('hallo welt');
})();

// this can be used to get function scope and alias names
// often used with jQuery
(function ($) {
  console.log($('body'));
}(jQuery));
```

--
### js obstacles

> NO Dynamic Scope in JavaScript

```js
function repeat (who, what) {
  var text = 'said';
  repeater(who, what);
}

function repeater(who, what) {
  console.log(who, text, what); // ReferenceError: text is not defined
}
// the repeater function wasn't defined inside the
// caller function that has the text variable defined.
repeat('I', ', hey YOU!');
```

**JavaScript does NOT support dynamic scope, where the variable resolution happens in the scope where the function is called at run time.**

--
### js obstacles

> BUT Lexical Scope

```js
function whoSaysWhat (who, what) {
  var text = 'says';
  function say() { // inner function can access the outer scope
    console.log(who, text, what); // text is declared in parent function
  }
  say();
}
whoSaysWhat('elephant', 'trörööö');  // elephant says trörööö
```

**The scope of a variable is statically defined by its location in the source code (not dynamically at run time). This is called lexical scope.**

*Note: inner functions can access variables from outer functions.*

--
### js obstacles

> Closures

*"Closures are functions that refer to independent (free) variables. In other words, the function defined in the closure 'remembers' the environment in which it was created." [quote from mozilla](https://developer.mozilla.org/en/docs/Web/JavaScript/Closures)*

```js
function repeater (who, what) {
  var text = 'said';
  // inner function is returned and creates a creates closure
  // where the returned function has access to the outer scope
  // it was defined in, even after the outer function has terminated.
  return function repeat() {
    console.log(who, text, what);
  }
}
repeater('everyone', 'yay!')();  //everyone said yay!
```

--
### js obstacles

> different JavaScript / ECMAScript / node.js Versions

- ES5 :: **node.js** <= 0.x :: [**browser** caniuse +](http://caniuse.com/#search=es5)
- ES6 (ECMAScript 2015) :: **io.js, node.js** > 0.x :: [**browser** caniuse ~](http://caniuse.com/#search=es6)

*Transpilers might be helpful in using the next version of JavaScript even it is not fully supported by the engine (browser or node.js) by transpiling it into the currently supported version, but they also come at a cost and add another complexity.*

--
### js obstacles

> - The JavaScript universe is changing very fast ....
> - Many tools/frameworks to pick from
> - **It's hard to choose the *right* Tool** for the job

*one can easily forget the original problem that needed to be solved by adding new fancy stuff and solving problems that were not there in the first place*

--
### js obstacles with ES6

> Variable Hoisting

*`let` and `const` variables are not hoisted*

```js
function l () {
  typeof foo; // ReferenceError: foo is not defined
  let foo = 'bar';
}
l();

function c () {
  typeof foo; // ReferenceError: foo is not defined
  const foo = 'bar';
}
c();

typeof foo; // 'undefined'
```

--
### js obstacles with ES6

> Variable Hoisting

*checking with **typeof** is NOT SAFE anymore!*

```js
typeof undefinedVariable // safe: -> 'undefined'

bar = 'milkbar'; // error: not defined
typeof bar // IMPORTANT: typeof is not safe anymore and throws an error!
let bar = ''; // because of this line (same for `let` and `const`)
```

--
### js obstacles with ES6

> Variable Scope

*inner functions/blocks can access `var`, `let` and `const` variables from outer scope/block*

```js
function myfunction () {
  console.log(myvar); // -> 'hello var'
  console.log(mylet); // -> 'hello let'
  console.log(myconst); // -> 'HELLO_CONST'
}
var myvar = 'hello var'
let mylet = 'hello let';
const myconst = 'HELLO_CONST';
if (mylet && myconst) {
  console.log('mylet:', mylet, 'myconst:', myconst); 
  // -> mylet: hello let myconst: HELLO_CONST
}
myfunction();
```

--
### js obstacles with ES6

> Variable Scope

*`let` and `const` are not accessible outside of the block they were defined in*

*but variables declared with `var` are accessible outside of the defining block*

```js
for (let i = 0; i < 3; i++) {
  var myvar = i;
  let mylet = i;
  if (!i) {
    const myconst = i;
  }
  console.log(myconst); // ReferenceError: myconst is not defined
}
console.log(myvar); // -> 2
console.log(mylet); // ReferenceError: mylet is not defined
console.log(myconst); // ReferenceError: myconst is not defined
```

--
### js obstacles with ES6

> problem with `for` loop

```js
// hmmm not really what we wanted
for(var i = 0; i < 10; i++) {
  setTimeout(function (){console.log('i', i)}, 100);
  //10 10 10  ... 10
} // **don't use for loop for asynchronous actions**
```
you can now easily fix this with `let` with ES6

```js
// you can fix this with `let`
for(let i = 0; i < 10; i++) {
  setTimeout(function (){console.log('i', i)}, 100);
  //0 1 2  ... 9
}
```

--
### js obstacles with ES6

> Arrow Function and `this` 

*an arrow function does not create its own `this` context*

*in most cases, this is helpful and we can get rid of `var self = this`*

*however arrow functions cannot be used with apis that create their own `this` context.*

```js
document.body.addEventListener('click', function() {
  console.log(this instanceof HTMLBodyElement); // true
});
```

```js
document.body.addEventListener('click', () => {
  console.log(this instanceof HTMLBodyElement); // false -> window object
});
```

--
### js obstacles with ES6

> `class` objects can't be instantiated without the `new` keyword

*this breaks with what is possible in ES5*

API with a constructor function:

```js
function Greeter (greeter) {
  if (!(this instanceof Greeter)) return new Greeter(greeter);
  this.greeter = greeter;
}
Greeter.prototype.greet = function greet(person) {
  console.log(this.greeter + ' greets ' + person);
}
Greeter('Tom').greet('Jerry'); // -> Tom greets Jerry
```

--
### js obstacles with ES6

> `class` objects can't be instantiated without the `new` keyword

*this breaks with what is possible in ES5*

same API as with a closure:

```js
function Greeter(greeter) {
  return {
    greet: function greet(person) {
      console.log(greeter + ' greets ' + person);
    }
  }
}
Greeter('Tom').greet('Jerry'); // -> Tom greets Jerry
```

--
### js obstacles with ES6

> `class` objects can't be instantiated without the `new` keyword

*this breaks with what is possible in ES5*

with `class` the API breaks! `new` is required.

```js
class Greeter {
  constructor(greeter) {
    this.greeter = greeter;
  }
  greet(person) {
    console.log(this.greeter + ' greets ' + person);
  }
}
new Greeter('Tom').greet('Jerry'); // -> Tom greets Jerry
Greeter('Tom').greet('Jerry'); // TypeError: 
// Class constructor Greeter cannot be invoked without 'new'
```

--
### js obstacles with ES6

> Promise Error Handling

```js
var p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('waited');
    reject(new Error()); 
    // Promise is already resolved, -> no Error
  }, 100);
});

p .then(console.log) // -> waited
  .catch((err) => console.error(`ERROR: ${err}`)) 
  // not being called
```

> NICE! no Error is thrown after the Promise resolved

--
### js obstacles with ES6

> Promise Error Handling

```js
var p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('waited'); // resolves
    setTimeout(() => { throw new Error('uups') }, 100); 
    // Erorr is thrown after resolved, without being catched
  }, 100);
});

p .then(console.log) // -> waited
  .catch((err) => console.error(`ERROR: ${err}`)) 
  // not being catched
```

> UUPS! With Promises, Errors can still be thrown without being able to catch them!

--
### js obstacles with ES6

> Promise Error Handling

```js
var p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('waited'); // resolves
  }, 100);
});

p .then((val) => val, (err) => err) // -> forward to next handler
  .then((val) => {
    throw new Error(val)
  }, (err) => {
    console.error(`FIRST_ERROR: ${err}`) // not being called
  })
  .catch((err) => console.error(`ERROR: ${err}`)) // -> ERROR
```

> If an error is thrown in then's first function, it is not being catched by the same then's second error handler function.