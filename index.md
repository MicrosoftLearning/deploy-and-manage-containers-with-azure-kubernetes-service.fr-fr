---
title: Instructions hébergées en ligne
permalink: index.html
layout: home
---

# Répertoire de contenu

Les liens hypertexte vers chaque exercice et démonstration de labo sont répertoriés ci-dessous.

## Laboratoires

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Module| Exercice |
| --- | --- | 
{% for activity in Exercise  %} | {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

