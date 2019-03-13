---
title:  "Global SysID Search"
date:   2019-02-19 00:00:00
---
Every now and then I run in to the problem of having a sys_id without any identifiers as to what it points to. This is often a problem with System Properties, where a developer has removed the hard-coding of an id and it isn't easy to find in the sys_metadata table.

Here is a script to help find what you need.

{% highlight javascript %}
gs.log(findAnywhere('8bcd5703dbd8a300715833f43a9619df'));

function findAnywhere(asysid, html) {
  if (html !== true && html !== 'true') {
    html = false;
  }
  var check;
  var tableName;
  var url = gs.getProperty('glide.servlet.uri');
  var table = new GlideRecord('sys_db_object');
  //Make sure we're not looking at a ts (text search) table.
  table.addEncodedQuery('sys_update_nameISNOTEMPTY^nameISNOTEMPTY^nameNOT LIKEts_');
  table.query();
  while (table.next()) {
    tableName = table.getValue('name');
    check = new GlideRecord(tableName);
    gs.log(tableName);
    try {
      if (check && check.get(asysid) && check.getValue('sys_id') == asysid) {
        url += tableName + '.do?sys_id=' + asysid;
        if (html) {
          var htmlUrl = '<a href="' + url + '">' + url + '</a>';
          return url;
        } else {
          return url;
        }
      }
    } catch (e) {
      // Catch all errors (seems to happen when looking at the db indexes table)
      gs.log('**************************ERROR****************************');
      gs.log(e);
    }
  }
}
{% endhighlight %}
