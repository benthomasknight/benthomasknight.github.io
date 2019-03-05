{% if include.ordered %}
<ol>
{% else %}
<ul>
{% endif %}

{% assign sorted = include.category | reverse %}
{% for post in sorted %}
  <li>
    {% if page.url != post.url %}
      <a href="{{ post.url }}">
    {% endif %}

    {{post.title}}

    {% if page.url != post.url %}
      </a>
    {% endif %}
  </li>
{% endfor %}

{% if include.ordered %}
</ol>
{% else %}
</ul>
{% endif %}
