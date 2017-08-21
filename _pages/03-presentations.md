---
layout: page
title: Presentations
permalink: /presentations/
navigation_weight: 1
---

{% for page in site.data.presentations-index %}
  <div class="boxed_page">
    <div class = "index_item_left">
      <img src="{{ page.image }}" style="margin: 0px 10px" width="54" height="54" align="left"/>
    </div>
    <div clas = "index_item_right">
      <p style="text-align:left;"><blogheader><a href="{{ page.url | prepend: site.baseurl }}">{{ page.title }}</a></blogheader><time>{% if page.author %}&nbsp;•&nbsp;{{ page.author}}{% endif %}</time></p>
      {{ page.description }}
      <br>
    </div>
  </div>
{% endfor %}
<br><br>
