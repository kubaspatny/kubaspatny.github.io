---
layout: page
title: Archives
---

<ul class="archive">
  {% for post in site.posts %}

    {% unless post.next %}
      <h3>{{ post.date | date: '%Y' }}</h3>	 
    {% else %}
      {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
      {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
      {% if year != nyear %}
	   <hr class="post-divider">	
        <h3>{{ post.date | date: '%Y' }}</h3>
      {% endif %}
    {% endunless %}

    <p>{{ post.date | date: "%b %d" }} <a href="{{ post.url }}">{{ post.title }}</a></p>
  {% endfor %}
</ul>
