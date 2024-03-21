---
title: Speaking Engagements
comments: false
author: Nico Vermeir
sitemap: false
permalink: /speaking.html
---

I am passionate about sharing knowledge and engaging with the tech community. Here are some of the conferences and communities I've had the pleasure of speaking at recently:

{% for conference in site.data.conferences %}
### {{ conference.name }}
- **Location:** {{ conference.location }}
- **Date:** {{ conference.date }}
- **Topic:** {{ conference.topic }}
{% endfor %}