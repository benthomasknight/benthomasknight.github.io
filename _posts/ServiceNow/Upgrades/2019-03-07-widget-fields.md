---
title:  "Widget Fields"
date:   2019-03-07 00:00:00
header:
  image: /assets/images/splash/snow-widget-editor.png
  image_description: "ServiceNow Widget Editor"
---
Since the Service Portal was brought in during the Helsinki release you have been able to add widgets to catalog variables through the use of the *widget* field. This allowed you to put in a custom component onto the form in place of a regular field. This is great but it came with one major issue.

## The problem

Almost every Service Portal developer has come across this issue in one way or another. You have many forms to display and they all require this *one* new bit of functionality, but it is slightly different per form. This is an issue because it means you either have to duplicate the functionality for each form, or you find a way to make it generic and change it per configuration on the variable.

For example, lets say you are required to make a table variable (pre-madrid where they brought in a new type for tables!) for multiple forms but you are required to have different columns for each. There is so much common functionality that it would be a waste to have three different version of the same copied code. In an effort to keep things DRY we need to find a way to make this reusable.

## The Solution

Turns out you have access to the both the field the widget is on and the g_form through variables on the $scope. These are found at:

{% highlight javascript %}
c.g_form = $scope.page.g_form;
c.field = $scope.page.field;
{% endhighlight %}

With access to these two bits of information we now have a way to start creating some configurable options. All we need now is the macro field and a hidden field that can save the value (Macro fields do not have values).

### Creating the Options

In order to have a configurable format for our tables we will need to decide on an agreed schema for defining tables. There are various ways we can do this, from creating a new table to store the columns to using the default value. For this example we will use the default value of our variable with the following schema.

{% highlight json %}
{
  "label":"<Display Label>",
  "columns":[{
    "code":"<Variable Name for the Column>",
    "label":"<Column Display Name>",
    "type":"<HTML5 Type>"
  }],
  "field":"<Hidden Field to Store Value>"
}
{% endhighlight %}

Lets say we have a table with three columns. A string field called Name; A number field called Qty; and a checkbox called Confirmed. This is what we can put in the default value for the macro.

{% highlight json %}
{
  "label":"Order Form",
  "columns":[{
    "code":"name",
    "label":"Name",
    "type":"text"
  },{
    "code":"quantity",
    "label":"Qty",
    "type":"number"
  },{
    "code":"confirmed",
    "label":"Confirmed",
    "type":"checkbox"
  }],
  "field":"my_hidden_field"
}
{% endhighlight %}

We have used inbuilt HTML 5 types in this schema to simplify the implementation but you are also able to create your own more complex implementation if required. Just change the "type" key and handle the case in the widget HTML.

### Rendering the Form

So now we have the required configurable schema we need to make our widget handle it. First we want to make sure we grab the schema of the variable on load of our widget. This is simply using the g_form with our variable name.

{% highlight javascript %}
/* Get our schema as a javascript object */
c.schema = JSON.parse(c.g_form.getValue(c.field.name));
{% endhighlight %}

So we have our schema but we have not told the widget how to actually render it out yet. Lets do that now.

{% highlight html %}
<div class="panel">
  <div class="panel-heading">{{c.g_form.getLabel(c.field.name)}}</div>
  <table class="table">
    <thead>
      <tr>
        <!-- Display all the headers -->
        <th ng-repeat="column in c.schema.columns">
          {{column.label}}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td ng-repeat="column in c.schema.columns">
          <input type="{{column.type}}" class="form-control"/>
        </td>
      </tr>
    </tbody>
  </table>
</div>
{% endhighlight %}

Success! We now have ourtable rendering with the correct fields displaying, and I even threw in so bootstrap classes to help get the style in line with the other fields on the page.

There is only one thing left to do now. Lets get the table adding and removing values. To do this we will add a place to store the data in our schema.

{% highlight json %}
{
  "rows":[]
}
{% endhighlight %}

Now we have a place to store this data, lets allow it to have values saved there. I will change the HTML as follows:

{% highlight html %}
<!-- Table Head updates -->
<thead>
  <tr>
    <!-- Add a column at the start to remove rows -->
    <th></th>

    <!-- what we had before goes here... -->
  </tr>
</thead>


<!-- Table Body Updates -->
<tbody>
  <!-- Render the rows that exist -->
  <tr ng-repeat="row in c.schema.rows track by $index">
    <!-- remove rows -->
    <td><span class="glyphicon glyphicon-trash" ng-click="removeRow($index)"></span></td>

    <td ng-repeat="column in c.schema.columns">
      <!-- Set each row to have a model for the columns -->
      <input type="{{column.type}}" class="form-control" ng-model="row[column.code]" ng-change="save()"/>
    </td>
  </tr>
  <!-- A default row for adding new rows -->
  <tr>
    <td><button class="btn btn-primary" ng-click="addRow()">Add Row</button></td>

    <!-- dummy the extra columns -->
    <td ng-repeat="column in c.schema.columns"></td>
  </tr>
</tbody>
{% endhighlight %}

The final thing to do is implement the functions defined in this update.

{% highlight javascript %}
$scope.addRow = function() {
  c.schema.rows.push({});
}

$scope.removeRow = function(index) {
  c.schema.rows.splice(index, 1);
}

$scope.save = function() {
  c.g_form.setValue(c.schema.field, JSON.stringify(c.schema));
}
{% endhighlight %}

### Additional Features

Now that we have this base code we can pretty much add anything we want. Some good ideas are:

1. Validation
2. Extra Field Types
3. In-form Modifications to the Table
4. Move the schema from the default value to a new record type

## The Warning

Please note that these are not currently documented is the documentation (Up to Madrid) so the variables may change and have done so previously. There isn't anything we can do about it so hopefully ServiceNow bring out an official way of doing this soon!
