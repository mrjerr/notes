Django Template
=

```
from django.shortcuts import render, render_to_response

return render_to_response('blog/post_details.html', {
            'post': post,
            'comments': comments,
        })

return render(request, 'blog/post_details.html', {
                  'post': post,
                  'comments': comments,
              })
```

## Syntax

- {% for item in list %}{% endfor %}
- {% if var %}{% endif %}  
- {% include "tpl.html" %} - включение подшаблона
- {{ var }} - значение переменной var
- {{ var|truncatechars:9 }} - применение фильтра
- {# comment #} , {% comment %}{% endcomment %} - комментарии

#### Доступ к свойствам и методам

- {{ object.content }} - доступ к атрибуту
- {{ name.strip }} -  вызов метода strip
- {{ info.avatar }} -  доступ к ключу словаря
- {{ post_list.0 }} - доступ к 0 элементу списка

## Наследование шаблонов
### Базовый шаблон
```
<!DOCTYPE HTML>
<html>
  <head>
    <title>{% block title %}Q&A{% endblock %}</title>
    {% block extrahead %}{% endblock %}
  </head>
  <body>
    <h1>Вопросы и ответы</h1>
    {% block content %}{% endblock %}
  </body>
</html>
```
### Шаблон страницы
```
{% extends "base.html" %}
{% block title %}
  {{ block.super }} – главная
{% endblock %}
{% block content %}
  {% for obj in post_list %}
    <div class="question">
      <a href="{{ obj.build_url }}">{{ obj }}</a>
      {{ obj.created_date|date:"d.m.Y" }}
    </div>
  {% endfor %}
{% endblock %}
```
