---
layout: page
title: Tutorials
permalink: /tutorials/
navigation_weight: 2
---

{% for page in site.data.tutorial-index %}
  <div class="boxed_page">
    <div class = "index_item_left">
      <a href="{{ page.url }}"><img src="{{ page.image }}" style="margin: 5px 10px" width="54" height="54" align="left"/></a>
    </div>
    <div clas = "index_item_right">
      <a href="{{ page.url }}">{{ page.title }}</a><time>{% if page.author %}&nbsp;•&nbsp;{{ page.author}}{% endif %}</time><br>
      <p class="spacer">{{ page.description }}</p>
    </div>
  </div>
{% endfor %}
<br><br>
