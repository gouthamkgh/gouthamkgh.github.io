---
layout: default
title:  About
date:   2016-11-11 17:50:00
categories: main
---

<!-- <section id="archive">
  <h2>Archived posts</h2>
  <ul class="posts">
    {%for post in site.posts %}
      {% unless post.next %}
        <ul class="post">
      {% else %}
        {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
        {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
        {% if year != nyear %}
          </ul>
          <h3>{{ post.date | date: '%Y' }}</h3>
          <ul class="posts">
        {% endif %}
      {% endunless %}
        <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
    </ul>
  </ul>
</section> -->

<h2>Archived posts</h2>

<ul class="posts">
    {% for post in site.posts %}
      <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>