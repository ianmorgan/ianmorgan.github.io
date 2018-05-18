---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

# Introduction 

Dig around to view links to tech things I've cared about, 
worried about, would like to more about, or just been really 
annoyed by. 


* [Mircoserices link 1](./content/mircoservices)
* [Mircoserices link 2](content/microservices)
* [Mircoserices link 3](/content/microservices)

# Blogs

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>