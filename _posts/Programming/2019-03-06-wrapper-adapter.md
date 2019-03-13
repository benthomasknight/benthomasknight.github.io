---
title:  "Design Patterns - Adapter"
date:   2019-03-06 01:00:00
categories: [design-patterns]
tags: [design-patterns, programming, adapter, wrapper]
---

An adapter is a pattern of doing a conversion between an interface you cannot change to an interface that your code requires. This is commonly used with 3rd party libraries where you cannot modify the libraries code but you need it be able to access it using a particular interface.

Lets use the output example from the previous abstract post. We had an interface with three part. The content to render, a method to clear the content, and an output. Our implementation had two forms of output, to a file and to the console. Here is the code for a refresher.

{% highlight typescript %}
interface IOutput {
  content: Array<string>;
  clear: () => void;
  output: () => void;
}

/* Make the class abstract */
abstract class BaseOutput implements IOutput {
  constructor(public content: Array<string>) {}

  clear() {
    this.content = [];
  }

  /* Make the non-implemented functions abstract */
  abstract output(): void;
}

class ConsoleOutput extends BaseOutput {
  output() {
    console.log(this.content.join());
  }
}

/* NodeJS file output */
import appendFile from 'fs';

class FileOutput extends BaseOutput {  
  output() {
    appendFile('message.txt', this.content.join());
  }
}
{% endhighlight %}

Now lets say you wish to add a third type of output using a third party library that has the following interface.

{% highlight typescript %}
interface IOutputLibrary {
  refresh: () => void;
  send: (content: Array<string>) => void;
}
{% endhighlight %}

As you can see the interface no longer works for this library. What we need is some class that *wraps* or *adapts* the library with the interface we need.

Most adapters are simple wrappers that have the interface we need and just call the matching function of the wrapped class. This is exactly what we do below (made easy by this didactic example).

{% highlight typescript %}
class LibraryOutputAdapter extends BaseOutput {
  /* Have an instance of the library to call */
  private library: IOutputLibrary;
  constructor(public content: Array<string>) {
    this.library = new OutputLibrary(content);
  }

  clear() {
    this.content = [];

    /* call the equivalent method on the library */
    this.library.refresh();
  }

  output() {
    /* call the equivalent method on the library */
    this.library.send(this.content);
  }
}
{% endhighlight %}

So what we have done now is adapted the library into something we can use wherever an **IOutput** interface is required.

{% include category-list.md ordered=true category=site.categories.design-patterns %}
