** Просмотр sql запросов при получении объектов **
```
In [19]: import logging                                 
In [20]: l = logging.getLogger('django.db.backends')    
In [21]: l.setLevel(logging.DEBUG)                      
In [22]: l.addHandler(logging.StreamHandler())      
In [23]: User.objects.all().order_by('-id')[:10]          
(0.000) SELECT "auth_user"."id", "auth_user"."username", "auth_user"."first_name", "auth_user"."last_name", "auth_user"."email", "auth_user"."password", "auth_user"."is_staff", "auth_user"."is_active", "auth_user"."is_superuser", "auth_user"."last_login", "auth_user"."date_joined" FROM "auth_user" ORDER BY "auth_user"."id" DESC LIMIT 10; args=()
Out[23]: [<User: hamdi>]
```

