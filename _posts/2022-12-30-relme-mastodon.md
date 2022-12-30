---
title: Rel=me in Jekyll for mastodon
date: 2022-12-30 15:00:00 +/-TTTT
categories: [Social]
tags: [jekyll]     # TAG names should always be lowercase
---
Mastodon, the federated social media network that has been getting lots of attention lately has a section in the user profile called profile metadata were you can for instance add a link to a webpage. If the page you link to has a rel="me" attribute it will display as verified on the profile page. I added a link to this blog on my page at [https://twit.social/@jost80](https://twit.social/@jost80) but Jekyll with the Chirpy theme did not add rel="me" by default. 

Since I didn't find any documented solution I decided to try and hack it in myself. 

My solution is:

In contact.yml I added a me attribute for mastodon:

```
  type: mastodon
  icon: 'fab fa-mastodon'   # icons powered by <https://fontawesome.com/>
  url:  'https://twit.social/@jost80'                  # Fill with your mastodon account page
  me: true
```

And then in _includes/sidebar.html (This file was missing for me in chirpy starter but could be added from the chirpy repo) I modified the if url section.

Original code

```
{% raw %}
<a href="{{ url }}" aria-label="{{ entry.type }}"
    {% unless entry.noblank %}target="_blank" rel="noopener" {% endunless %}>
    <i class="{{ entry.icon }}"></i>
</a>
```
{% endraw %}


Quick and dirty fix
```
{% raw %}
<a href="{{ url }}" aria-label="{{ entry.type }}"
    {% unless entry.noblank %}target="_blank"{% unless entry.noblank %} rel="{% if entry.me %}me {% endif %}noopener"{% endunless %}{% endunless %}>
    <i class="{{ entry.icon }}"></i>
</a>
```
{% endraw %}


Since I am not a web developer I assume there is better ways to do this and I would not recommend copying this solution without some more investigation.