{% assign rows = site.posts.size | divided_by: 2.0 | ceil %}
{% for i in (1..rows) %}
  {% assign offset = forloop.index0 | times: 2 %}
  <div>
    {% for post in site.posts limit:2 offset:offset %}
      <a href="{{ post.url }}">{{ post.title }}</a>
    {% endfor %}
  </div>
{% endfor %}


{% assign rows = site.data.snippet-index.size | divided_by: 2.0 | ceil %}
{% for i in (1..rows) %}
  {% assign offset = forloop.index0 | times: 2 %}
  <div>
    {% for page in site.data.snippet-index limit:2 offset:offset %}
      <div class="boxed_page">
        <div class="index_item_left">
          <img src="{{ page.image }}" alt="Image text" style="margin: 0px 10px" width="54" height="54" align="left"/>
        </div>
        <div class="index_item_right">
          <a href="{{ page.url }}">{{ page.title }}</a><br>
          {{ page.description }}
          <br>
        </div>
      </div>
    {% endfor %}
  </div>
{% endfor %}