---
layout: post
title:  "Understanding let and Javascript Hoisting"
date:   2019-12-15 11:34:10 +0100
categories: javascript
tags: [var, let, const, hoisting]
---

To understand how variables definition works in JavaScript we must get familiar with block scoping, and how hoisting.

## Block Scopes

Block scoping in JavaScript is defined with the opening and closing curly braces `{}` **containing at list one assignment**. If no assignment is made, JavaScript will treat it as an object literal, and create a new object.

{% highlight javascript %}
{
  // this is a block scope
  var name='Bob';
}

{
  console.log('This is not a block scope');
}

{% endhighlight %}

## Hoisting

var declarations are **hoisted** at the top of the block scope. Basically the Javascript interpreter moves all variables definitions at the very beginning of their scope.

That means this code 

{% highlight javascript %}
bar = 'bar';
{
  var foo = 'foo';
}
var bar;
{% endhighlight %}

is equivalent to 
{% highlight javascript %}
var bar = undefined;
bar = 'bar';
{
  var foo = 'foo';
}
{% endhighlight %}

JavaScript in `strict mode` does not allow variables to be used if they are not declared.

## var, let and const

`let` and `const` are different beasts:
- they are not hoisted
- they are always scoped within their block
