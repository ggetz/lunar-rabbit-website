---
layout: post
title: "New Game!"
date: 2016-04-16 8:38:24
categories: news
tags: [Heartline]
author: Laura Mo
---
<!-- _includes/base.html -->
{% assign base = '' %}
{% assign depth = page.url | split: '/' | size | minus: 1 %}
{% if    depth == 1 %}{% assign base = '.' %}
{% elsif depth == 2 %}{% assign base = '..' %}
{% elsif depth == 3 %}{% assign base = '../..' %}
{% elsif depth == 4 %}{% assign base = '../../..' %}{% endif %}

[titleLogo]: {{base}}/img/posts/HeartlineAnnouncement/HeartlineLogo.png
[heartlineArt]: {{base}}/img/posts/HeartlineAnnouncement/HeartlineSplash.jpg


This has been an announcement long overdue, but we're excited to announce Lunar Rabbit's second project!

![alt text] [titleLogo]

Set in a world where the unsetting sun has given life for as long as anyone could remember, Heartline will be a unique blend of semi-procedurally generated sidescrollers and choose your own adventure games.

The Crows are born into the world with both a physical body and a ghostly doppleganger attached to them by a heartline. As they live, their ghosts slowly burn away the line until the only thing left to burn is the Crow's life. But with the light from the sun, their lines grew long and so did their lives.  Now for the first time, the sun has begun to set, leading many to believe that it is the end of the world.

Play as the Traveller and both control their physical body and their doppleganger. Send ripples through the line to collect sundrops. Make decisions with consequences. Explore a world in its twilight days.

Keep checking here for progress updates and insights on how we're making this world come to life!
 
![alt text] [heartlineArt]