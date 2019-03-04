---
title:  "Design Patterns - Interfaces"
date:   2019-03-04 01:00:00
categories: [design-patterns]
tags: [design-patterns, programming, interfaces]
---

Interfaces are one of the most basic tools in programming. All they do is say to the compliler *I expect all objects of this type to have these* without detailing the specifics of what *these* are. They describe what an object is expected to have but not the specifics of the implementation.

For instance, below are two classes implementing the same interface. Class A will *throw a compiler error* but Class B will compile correctly. This is because the interface says that it expects the **MyExpectedFunction** to exist but it didn't find it. You will also notice that you cand define properties that may *exist*.

{% highlight typescript %}
interface MyInterface {
  MyExpectedFunction: void;
  MyOptionalFunction?: void;
}

class A implements MyInterface {}

class B implements MyInterface {
  public MyExpectedFunction() {
    /* ... */
  }
}
{% endhighlight %}

So why is this useful?

## It provides some Expectations

In programming there are a lot of assumptions that are made about the type and structure of objects and variables. For an untyped, interpreted language like Javascript it is fine for you to check if an array is a boolean, or a number a string as it will do the conversion for you. This is called *Type Coercion* and is incredibly useful for quick mockups as you don't have to worry about the wrong data coming in.

The problem is that Javascript is getting used for bigger things now and sometime is best to ensure that what is being given to a function adheres with what we expect. Typescript handles this with primatives for you, and for objects you can define it yourself through interfaces.

## Generic

Due to the expections that given objects will have certain properties, generic code can be written across multiple classes. This is the beginnings of more complex features such as dependency injection, but I will write more about that later.

The below code is a simple example of the benefits of using interfaces. It shows that you can specify the way you want to output a message at runtime without the need to know exactly which output you wish at compile time.

{% highlight typescript %}
interface IWriter {
  write: (output: any) => void;
}

class CanvasWriter implements IWriter {
  write(output: any) {
    /* Output to console */
  }
}

class ConsoleWriter implements IWriter {
  write(output: any) {
    /* Output to console */
  }
}

class Renderer {
  constructor(private writer:IWriter) {
    /**
     * No matter the writer they use the same interface, so the
     * write function will exist.
     */
    writer.write("Output Message");
  }
}
{% endhighlight %}

## Expectations


{% include category-list.md ordered=true category=site.categories.design-patterns %}
