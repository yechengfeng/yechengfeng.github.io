---
layout: default
title: 首页
---

# 📚 文档索引


<style>
  details { margin: 8px 0; }
  summary { font-weight: bold; font-size: 1.1em; cursor: pointer; }
  summary:hover { color: #2c7be5; }
  .subcategory { margin-left: 1.5em; }
  .file-list { margin-left: 3em; list-style: disc; }
  a { text-decoration: none; color: #333; }
  a:hover { color: #2c7be5; }
</style>

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
    <details open>
      <summary>{{ cat | capitalize }}</summary>
      <ul class="file-list">
        {% for file in files %}
          {% assign fname = file.path | split: '/' | last | replace: '.md','' %}
          {% assign parts = fname | split: '--' %}
          {% assign depth = parts.size %}

          {% if depth == 1 and cat == "其他" %}
            <li>📄 <a href="{{ file.url }}">{{ parts[0] | replace: '-', ' ' | capitalize }}</a></li>

          {% elsif depth == 2 and parts[0] == cat %}
            <li>📄 <a href="{{ file.url }}">{{ parts[1] | replace: '-', ' ' | capitalize }}</a></li>

          {% elsif depth > 2 and parts[0] == cat %}
            <li>
              <details class="subcategory">
                <summary>📂 {{ parts[1] | replace: '-', ' ' | capitalize }}</summary>
                <ul class="file-list">
                  <li>📄 <a href="{{ file.url }}">{{ parts | last | replace: '-', ' ' | capitalize }}</a></li>
                </ul>
              </details>
            </li>

          {% endif %}
        {% endfor %}
      </ul>
    </details>
  </li>
{% endfor %}
</ul>

