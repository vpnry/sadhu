---
layout: page
title: Search
permalink: /search.html
showtoc: "no"
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/lunr.js/2.3.9/lunr.min.js" integrity="sha512-4xUl/d6D6THrAnXAwGajXkoWaeMNwEKK4iNfq5DotEbLPAfk6FSxSP3ydNxqDgCw1c/0Z1Jg6L8h2j+++9BZmg==" crossorigin="anonymous"></script>
<script> window.lunr || document.write('<script src="{{ "/assets/js/lunr.min.js" | prepend: site.baseurl  }}"><\/script>')</script>

<!-- This search page is adapted from https://github.com/CloudCannon/bakery-store-jekyll-template/tree/lunrjs -->

<br>
<div style="align-items: center;">
<h3 style="font-style: bold;">Search this site</h3>
<form action="{{ site.baseurl }}/search.html" method="get">
  <!-- <label for="search-box">Search</label> -->
  <input type="text" id="search-box" name="query" placeholder="enter keywords...">
  <input type="submit" value="search">
</form>

<ul id="lunr-search-results"></ul>

</div>
<script>
  window.store = {
    {% for post in site.posts %}
      "{{ post.url | slugify }}": {
        "title": "{{ post.title | xml_escape }}",
        "author": "{{ post.author | xml_escape }}",
        "category": "{{ post.category | xml_escape }}",
        "content": {{ post.content | strip_html | strip_newlines | jsonify }},
        "url": "{{ post.url | xml_escape }}"
      }
      {% unless forloop.last %},{% endunless %}
    {% endfor %}
  };
</script>
<script src="assets/js/search.min.js"></script>
