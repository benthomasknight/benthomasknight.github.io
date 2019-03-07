---
title:  "Design Patterns - Facade"
date:   2019-03-07 00:00:00
categories: [design-patterns]
tags: [design-patterns, programming, facade, wrapper]
---
A Facade is probably the most simple example of a wrapper function as it is just a class that calls other classes to hide the complexity. Let's dive in with an classic example of how a vehicle works.

{% highlight typescript %}
class Clutch {
  In() {/* */}

  Out() {/* */}
}

class Engine {
  Start() {/* */}
  Stop() {/* */}
  Rev() {/* */}
}

class Wheels {
  SpeedUp() {/* */}
  SlowDown() {/* */}
}
{% endhighlight %}

Here we have a few components that make up a car. Now lets look at what would be required for us to start the car.

{% highlight typescript %}
let clutch = new Clutch();
let engine = new Engine();
let wheels = new Wheels();

/* Lets start the engine */
clutch.In();
engine.Start();

/* Now lets move */
engine.Rev();
clutch.In();
wheels.SpeedUp();
{% endhighlight %}

This is simple enough to write but once we have multiple vehicles it would become somewhat cumbersome. Wouldn't it be great if we had something to combine all these elements? Lets bring in the car facade.

{% highlight typescript %}
class Car {
  constructor() {
    this.clutch = new Clutch();
    this.engine = new Engine();
    this.wheels = new Wheels();
  }

  Start() {
    this.clutch.In();
    this.engine.Start();
  }

  Move() {
    this.engine.Rev();
    this.clutch.In();
    this.wheels.SpeedUp();
  }
}
{% endhighlight %}

Now all we have to do is call "Start" and "Move" for each car. This is what I classically thought of as a basic Class. An object used to group functionality and hide complexity so I don't have to think about it.

{% include category-list.md ordered=true category=site.categories.design-patterns %}
