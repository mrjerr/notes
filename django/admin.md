Django Admin
=

## Reversing admin URLs in Django

|Page	| URL name	|Parameters|
| -| -| -|
|Index	|index	|
|Logout	|logout	|
|Password change	|password_change	|
|Password change done	|password_change_done	|
|Application index page|	app_list	|app_label
|Redirect to object’s page|	view_on_site	|content_type_id, object_id

|Page	| URL name	|Parameters|
| -| -| -|
|Changelist	|{{ app_label }}_{{ model_name }}_changelist	|
|Add	|{{ app_label }}_{{ model_name }}_add	|
|History	|{{ app_label }}_{{ model_name }}_history	|object_id
|Delete	|{{ app_label }}_{{ model_name }}_delete	|object_id
|Change	|{{ app_label }}_{{ model_name }}_change	| object_id


```python
{% url 'admin:store_category_changelist'%}

{% url 'admin:store_filtername_change' filter.id %}
```
