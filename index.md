---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

# Introduction 

Dig around to view links to tech things I've cared about, 
worried about, would like to more about, or just been really 
annoyed by. 

* [Graph Store](https://ianmorgan.github.io/graph-store/) - trying things out with Kotlin and GraphQL to unify  some 
common concepts in a single service. There is also a [running service](https://grapstore.app) as a simple demo 
* [Useful Stuff](https://ianmorgan.github.io/useful-stuff) - a little micro-site where I keep track of the useful 
little articles, blogs links and so on that I come across.  
* [Event Store](https://ianmorgan.github.io/event-store/) - a bare bones event store to support basic event sourcing concepts 
I'm hoping to find time to write variants using different backends, including MySQL and Kafka 

My [CV](pdfs/IanMorganCVJune2018.pdf) and [LinkedIn](https://www.linkedin.com/in/ianjmorgan/)

# Blogs

Ok, so I don't blog very often :(

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>