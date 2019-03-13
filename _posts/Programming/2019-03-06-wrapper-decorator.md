---
title:  "Design Patterns - Decorator"
date:   2019-03-06 02:00:00
categories: [design-patterns]
tags: [design-patterns, programming, decorator, wrapper]
---
Decorators are a design that allows you to add additional functionality to an object instance without modifying the base code for all instances of that class. This is used for cases where it does not make sense to create a new class for all new types. For example, you may have a table component class that needs to be scrollable. You can create a ScrollableTable class for this. Next you may need a validated table so you would have to create a ScrollableValidatedTable and a ValidatedTable. If you then have a third feature you would have to create 4 new classes to handle it. This keeps growing the more features you need.

Instead of having to manage the issue through inheritance, you can use decorators that add additional functionality. Lets define that base table class first.

{% highlight typescript %}
interface ITable {
  render: () => void;
}

class Table implements ITable {
  render() {
    /* code to render the table */
  }
}
{% endhighlight %}

So now we have the base we can look at how to work a decorator. Lets work with the scrollable table first.

{% highlight typescript %}
/* Note this implements the interface */
class ScrollableTableDecorator implements ITable {
  /* Table to decorate */
  protected table: ITable;

  constructor(table: ITable) {
    this.table = table;
  }
  
  render() {
    /* the additional decorator code */
    this.drawScrollbar();

    /* the base code */
    this.table.render();
  }

  private drawScrollbar() {
    /* code to draw the scrollbar */
  }
}
{% endhighlight %}

So how does this work?

The first important part is that the decorator implements the original interface that the table does. This is important as a decorator should act as the original table. You should also be able to pass the decorator to other decorators and have it work as if the original table was passed.

The next important part is that the decorator should call each function that is available on the original table that it does not override and handle itself. We are adding additional functionality on top so most of the time the base functionality still needs to be called.

Now that we have one decorator lets add one more to show nesting.

{% highlight typescript %}
/* Note this implements the interface */
class ValidatedTableDecorator implements ITable {
  /* Table to decorate */
  protected table: ITable;

  constructor(table: ITable) {
    this.table = table;
  }
  
  render() {
    /* the additional decorator code */
    this.drawValidation();

    /* the base code */
    this.table.render();
  }

  private drawValidation() {
    /* code to draw the scrollbar */
  }
}
{% endhighlight %}

As you can see this is pretty similar to the scrollable table class. This will often not be the case but a blog cannot handle too much code at once...

So now to call these decorators you can do the following:

{% highlight typescript %}
/* Basic table with no decorators */
const normalTable = new Table();

/* table with one decorator */
const validatedTable = new ValidatedTableDecorator(
  new Table()
);

/* table with mayn decorators */
const scrollValidatedTable = new ScrollableTableDecorator(
  new ValidatedTableDecorator(
    new Table()
  )
);
{% endhighlight %}

So there we have it. You can now add as many decorators as you like to the table class and it should always call all render functions of all previous decorators.

{% include category-list.md ordered=true category=site.categories.design-patterns %}
