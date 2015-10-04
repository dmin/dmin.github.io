---
layout: post
title:  "An Experiment in Unit Testing Closures in Javascript"
date:   2015-10-03 22:34:27
categories: unit testing javascript closures curry ramda mocking
---

Putting aside the pros and cons of testing closures (more broadly private functions), I was interested in finding a way to unit test javascript closures. I wanted to find a way to test them in isolation while still being able to access the 'outer scope' of the closure function.

To briefly recap closures:

{% highlight javascript %}
// Lets say you have a function, myFunc():
function myFunc(argOne) {

  var varOne = 1;

  // Inside of which is a function expression we'll
  // identify here as doSomething():
  return function doSomething() {
    // Inside doSomething() you have access to all the
    // variables in myFunc()'s scope. These variables
    // can be accessed by doSomething() whenever it is
    // invoked.
    return argOne + varOne;
  }
}

var doSomethingClosure = myFunc(1);

// When you invoke doSomethingClosure it still has
// access to the variables that were in scope to
// myFunc().
doSomethingClosure(); // => 2
{% endhighlight %}

The entire point of a closure is having access to the scope where the closure function was created. That said, unit testing a closure function in isolation doesn't really make much sense. Or does it?

No, it really doesn't.

Nevertheless, I wanted the option of testing a closure function without needing any knowledge of the function in which it is defined (i.e. no knowledge of any scope outside the function under test). I forgot to mention - TL;DR its not really possible.

The only way to achieve the goal of testing closure functions in isolation is to cheat and not use closures at all. The function you were once using as a closure must be defined elsewhere and any dependencies must be passed in as arguments.

You can think of a closure function as a function that has some 'preset' arguments. We can translate that idea into code with the ```Function.prototype.bind``` method - using it to create a 'bound' function with preset arguments:

{% highlight javascript %}
// Now we'll define doSomething() separately,
// explicitly declaring its dependencies through its
// arguments.
function doSomething(argOne, varOne) {
  return argOne + varOne;
}

function myFunc(argOne) {

  var varOne = 1;

  // And instead of creating a closure function we'll
  // just return a reference to doSomething() and use
  // bind() to preset two arguments. (The first
  // argument to bind() (and why it is set to
  // 'undefined') is outside the scope of this
  // article, but in short, it is used to set the
  // value of 'this' inside the 'bound' function.)
  return doSomething.bind(undefined, argOne, varOne);
}

var doSomethingBound = myFunc(1);

// When you invoke the bound function it still access
// to the variables it needs from the scope of myFunc,
// we just had to explicitly preset them with bind().
doSomethingBound() // => 2
{% endhighlight %}

Now that we've declared ```doSomething()``` separately from ```myFunc()``` we can test it without relying on any knowledge of ```myFunc()```.

{% highlight javascript %}
// using 'tape' (npm install tape)

var test = require('tape');

test('doSomething()', function (assert) {
  var actual = doSomething(1, 1);
  var expected = 2;

  assert.equal(
    actual,
    expected,
    'should return the sum of its first two arguments'
  );

  assert.end();
});

{% endhighlight %}


#### References:
- [Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [Tape](https://github.com/substack/tape)
