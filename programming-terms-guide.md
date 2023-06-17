# Programming Terms

### First Class Functions

Functions that can be passed as arguments, i.e , first class entities of language

```js
// example 1
function add (first,second, callback){
   console.log(first + second);
   callback();
}   

add(2, 3, function(){
   console.log('done');
});


// example 2
function logDone(){
   console.log('done');
}
add(2, 3, logDone); //passing as if it is a variable..but not invoking it using ();
```

**Explanation:**

- in javascript functions are also a variable
- it means that it can be passed as variables also
- for using functions as first class, we do not invoke them instead we wait for something to happen and then after these functions take effect
- in our example we pass the `logDone` as a parameter instead of invoking it
- so when the addition is done than the function takes effect and fires and do something
- we can also return a function from a function
- functions passed as parameter are called first class functions

### Higher Order Functions

Functions that accepts functions as arguments or returns function as its result. E.g map, reduce, filter etc.

### Event Driven Environment

Executing all the code and than waiting for the events to happen

```js
var a = 1;
console.log('setting this up');

$(document).ready(function(){
  $('button').on('click', function(){
      alert(a);
  });
});
```

**Explanation:**

- javascript is not like php which loads all the data and dies after that becoz its work is over, unless we reload
- javascript loads up our page and wait in the memory for user events, such as typing click hover etc.
- js fires the callback function when document is ready and then it adds an event listener on button for click
- it is listening for the click event this is event driven
- our example only runs once. So if we keep clicking the button we don't see console message again and again, instead only the function runs, only that line.
- this makes js fast because it stays in memory and fires only that is to be fired and not the whole page

### Closures

Retains state and scope after the execution. 

```js
$(document).ready(function(){
  var a = 1;

  $('button').on('click', function(){
      alert(a);
  });
});
```

**Explanation:**

- when the document ready function starts, it executes & finishes his work, sets up the event listener
- but the problem is we don't want the var a to die.
- and for this javascript does its work.
- because var `a` has reference in another function i.e on click function
- so javascript instead of garbage collecting the var a, keeps the var alive in memory land 
- but it is alive as long as on click func has reference to it
- when the function dies the reference dies and same with var
- javascript remembers the var in the outside scope 
- this is closure. retaining the scope and state after execution

### Context

Context is the value of `this` is for the code that is running

- We can change the context by using call, bind, apply
- obj.foo.call(window, 1, 2, 3) -> obj to bind and other parameter
- obj.foo.apply(window, [1, 2, 3])
- myBoundFoo = obj.foo.bind(window); calling myBoundFoo will run for window as context

### Combination and Permutation

**Combination:** It refers to all the different way we can group something where order doesn't matter

```
[1, 2, 3]
combinations of 2 numbers will be: 
[1, 2], [1, 3], [2, 3]
```

**Permutation:** It refers to all the different groups we can make where order matters

```
[1, 2, 3]
permutations of 3 numbers will be:
[1, 2, 3], [3, 2, 1] etc.
```
