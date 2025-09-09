---
layout: default
title: 首页
---

# 文档索引

以下为自动生成的文档分类及链接：

<ul>
{% assign files = site.pages | where_exp: "page", "page.path contains '我的坚果云/note/'" %}
{% assign grouped = "" | split: "" %}

<!-- 先处理一级分类（第一个 -- 或 “其他”） -->
{% for file in files %}
  {% assign fname = file.path | split: '/' | last | replace: '.md','' %}
  {% assign parts = fname | split: '--' %}
  {% if parts.size > 1 %}
    {% assign first_cat = parts[0] %}
  {% else %}
    {% assign first_cat = "其他" %}
  {% endif %}
  {% assign grouped = grouped | push: first_cat %}
{% endfor %}

{% assign unique_cats = grouped | uniq %}

{% for cat in unique_cats %}
  <li>
    <strong>{{ cat | capitalize }}</strong>
    <ul>
      {% for file in files %}
        {% assign fname = file.path | split: '/' | last | replace: '.md','' %}
        {% assign parts = fname | split: '--' %}
        {% assign depth = parts.size %}

        {% if depth == 1 and cat == "其他" %}
          <li>
            <a href="{{ file.url }}">{{ parts[0] | replace: '-', ' ' | capitalize }}</a>
          </li>
        {% elsif depth == 2 and parts[0] == cat %}
          <li>
            <a href="{{ file.url }}">{{ parts[1] | replace: '-', ' ' | capitalize }}</a>
          </li>
        {% elsif depth > 2 and parts[0] == cat %}
          <li>
            <strong>{{ parts[1] | replace: '-', ' ' | capitalize }}</strong>
            <ul>
              <li>
                <a href="{{ file.url }}">{{ parts | last | replace: '-', ' ' | capitalize }}</a>
              </li>
            </ul>
          </li>
        {% endif %}
      {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul>

