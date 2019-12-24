---
layout: post
title:  "How This works in JavaScript"
date:   2019-12-22 11:34:10 +0100
categories: javascript
tags: [this, functions, scope, apply, call, bind, arrow functions]
---

One of the most common interview question in javascript is how `this` works. With this post I'll try to explain the core concepts behind it, showing some examples and exceptions to the default behavior.

In fact, even a simple example like 

{% highlight javascript %}
const person = {
  name: 'bob',
  getName() {
    return `${this.name} is my first name`
  }
};
{% endhighlight %}

leads to non obvious answers and thougts, as the value of `this` cannot be determined without knowing how the `getName` function was invoked.

Let's start with the basics!

## The golden rules

There are 3 questions that, answered, will always help you find what is `this` referring to. These are:

- Was the function invoked?
- How was it invoked?
- In what context, was it invoked in?

Let's make a few examples to clarify a bit this concepts.

## Implicit Binding

> Implicit binding occurs when dot notation is used to invoke a function.

For example:

{% highlight javascript %}
const person = {
  name: 'bob',
  getName() {
    return `${this.name} is my first name`
  }
};
console.log(person.getName()); // <-- implicit binding
{% endhighlight %}

So, let's try to answer our 3 questions

- Was the function invoked? --> yes
- How was it invoked? --> using implicit binding
- In what context, was it invoked in? --> what is to the left of the function is the context

This means that,  when using implicit binding, the context of `this` is what's left to the dot. In the example, it's context is person, and the code will output the expected `bob is my first name`.

But let's complicate a bit our example:

{% highlight javascript %}
const person = {
  name: 'bob',
  getName() {
    return `${this.name} is my first name`
  }
};

const anotherPerson = {};
anotherPerson.getName = person.getName();
console.log(anotherPerson.getName());
{% endhighlight %}

In this scenario the output is `undefined is my first name`, and the reason for that is because the context of `getName` is `anotherPerson`, in which the propery `name` doesn't exists, hence the *undefined*

## Explicit Binding (or call, apply, and bind)

Let's get back to how golden rule and see three propotypes that change the answer to the second question

> How was it invoked? --> using ***explicit*** binding

Explicit bindings is the simple concept of assigning the scope of `this` via a parameter.

{% highlight javascript %}
const person = {
  name: 'bob'
}
function getName() {
  return `${this.name} is my first name`
}

console.log(getName.call(person) // bob is my first name
{% endhighlight %}

the parameter of the `call` method is the scope that the function will have once called. In this example it will always refer to `person`.

There are 2 others prototypes we can leverage to achieve the same result: `apply` and `bind`.

While `apply` is very similar to `call` (the only difference being the other arguments after the first one will be passed as an array), the third method, `bind` is radically different.

Rather then calling the function overriding the `this` keyword, it will return a new function with `this` always set to the parameter we passed to the `bind` function: we will be able to use the new function everywhere in our code and `this` will always be set to that value we passed to the `bind` function.

## Arrow Functions and `this`

When Arrow functions came into existence, with ES6, they have been considered a quick fix solution when running into problems with `this`. However there are som caveats on how `this` keyword works within the arrow functions. Let's dig a little bit into that.

Arrow function don't have the concept of `this`. That means that `this` inside an arrow function is basically handled as a regular variable: its value is based on the **lexical scope** of the arrow function.
The lexical scope is the scope defined at author time, and it's based upon the fact that every function creates it's own scope.

Since arrow functions don't have the concept of `this`, they inherit it from the first *non-arrow __function__* in the lexical scope.

Let's make a couple of examples to fix this concept

{% highlight javascript %}
const person = {
  name: 'bob',
  getName: () => {
    return `${this.name} is my first name`
  }
};

console.log(person.getName()); // undefined is my first name
{% endhighlight %}

In this example you might be wondering why it outputs `undefined is my first name`; The reason is because the **lexical scope** refers to the first non-arrow **function**. In this case `person` is just an object, not a function, and the scope is the main `window`, where `name` is not defined.

To fix this problem we can rewrite the example like this

{% highlight javascript %}
const person = {
  name: 'bob',
  getName() { // shorthand for getName: function() { ...
  return () => {
  console.log(`${this.name} is my first name)
    }
  }
}
{% endhighlight %}

This leads to the expected result, as the lexical scope of `this` points to the scope of the first non arrow function in the chain of parent functions: `getName`.

These concepts are extremely powerful, yet quite coonfusing: consider that  **`this` is normally runtime dependant, but it's lexical dependant when used inside an arrow function**

