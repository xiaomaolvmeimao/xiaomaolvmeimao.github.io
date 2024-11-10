---
layout: page
title: "About"
description: "Who am I?"
header-img: "img/home-bg.jpg"
---
<!--
![Jexus](../img/Jexus1.jpg)# Luke  
Data Scientist / Analyst / Engineer

Hey there! 👋 I'm Luke, a data enthusiast with a deep-rooted background in some of the most exciting tech ecosystems in the world—I've had the privilege to work with Alibaba, Baidu, and ByteDance, diving into the data-driven heartbeat of these companies. My journey in data spans several years, where I've been lucky enough to tackle complex challenges, build data solutions, and help shape strategies that drive real impact.

Now, I'm focused on exploring the fascinating world of AI, where I'm diving into the latest advancements and figuring out how we can use these technologies to create even more innovative, impactful solutions.

Thanks for stopping by, and feel free to connect if you want to chat data, AI, or anything tech!

-->
<hr>

{% for member in site.data.members %} {% if member.visible == true %}

<img src="{{ member.img }}" style="margin-top:0px; margin-bottom:5px; margin-right:10px; float:left; width:150px !important">

<h1>{{ member.name }}</h1>

<h2>{{ member.role }}</h2>

{% if member.github %} 
<span class="social-share-googleplus"><a href="https://github.com/{{ member.github }}" title="Github"><img src="{{ "/img/icons/github-icon.png" | prepend: site.baseurl }}" style="height:20px">Github</a></span> 
{% endif %}
{% if member.gplus %}
<span class="social-share-googleplus"><a href="{{ member.gplus }}" title="Google Plus"><img src="{{ "/img/icons/gplus-icon.png" | prepend: site.baseurl }}" style="height:20px">Google Plus</a></span>
{% endif %}
{% if member.url %}
<span class="social-share-googleplus"><a href="{{ member.url }}" title="Google Plus"><img src="{{ "/img/icons/url-icon.png"| prepend: site.baseurl }}" style="height:20px">Website</a></span>
{% endif %}

{% if member.bio %} 
{{ member.bio }}
{% endif %}

<hr>

{% endif %}{% endfor %}
	