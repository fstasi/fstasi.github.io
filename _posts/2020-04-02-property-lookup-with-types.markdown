---
layout: post
title:  "Lookup an object property including its type "
date:   2020-04-02 11:34:10 +0100
categories: typescript
tags: [Type, Generics, keyof, object]
---

Developing in typescript you probably come across similar code

{% highlight typescript %}
type Person = { name: string; age: number };
const person: Person = {
  name: "frank",
  age: 34,
};

function getProperty(obj: object, key: string) {
  return obj[key];
}
let personName = getProperty(person, 'name'); // personName type is `any`
{% endhighlight %}

This is essentially valid typescript, but as you can see we are not leveraging it's full potential as the `getProperty` function is loosing important info about the type of the property we get.

Ok, let's try to fix that...

## A common bad implementation

{% highlight typescript %}
function getProperty(obj: Person, key: keyof Person) {
  return obj[key];
}
const personName = getProperty(person, 'name'); // personName is `string | number`
{% endhighlight %}

This code is essentially even worse than the first version:

- we are not getting the right type
- we are hard coding a relation between `getProperty` and the `Person` type

## Type Generics

Luckily enough, Typescript provides us **type generics**, which essentially allow us to rewrite the code in a much cleaner way, capturing the types of the values we are passing in and use them later.

{% highlight typescript %}
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}
const personName = getProperty(person, 'name'); // personName is `string`
{% endhighlight %}

The content wrapped inside the `<` `>` is the **type variable(s)**

In this scenario we are saying:

- `T` is the type of the param `obj`
- `K`, the type of the second param, is a subtype (`extends`) of the union type (`keyof`) of the properties of T

This T allows us to capture the type the user provides (Person, in this case), so that we can use that information later.

If you are interested diving deep into the world of Typescript's Generics, take a look at [typescriptlang](https://www.typescriptlang.org/docs/handbook/generics.html).
