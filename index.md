---
layout: default
title: 首页
---

# 文档索引


<ul>
{% assign files = site.pages | where_exp: "page", "page.path contains '我的坚果云/note/'" %}
{% assign grouped = files | group_by_exp: "page", "page.path | split: '/' | last | split: '--' | first" %}

{% for category in grouped %}
  <li>
    <strong>{{ category.name | capitalize }}</strong>
    <ul>
      {% for item in category.items %}
        <li>
          <a href="{{ item.url }}">
            {{ item.path | split: '/' | last | split: '--' | last
              | replace: '.md', ''
              | replace: '-', ' '
              | capitalize }}
          </a>
        </li>
      {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul>

