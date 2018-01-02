HttpRequest
=

- request.method - метод запроса
- request.GET - словарь с GET параметрами
- request.POST - словарь с POST параметрами
- request.COOKIES - словарь c Cookie
- request.FILES - загруженныe файлы
- request.META - CGI-like переменные
- request.session - словарь-сессия
- request.user - текущий пользователь


## Получение GET и POST параметров
```
order = request.GET['sort'] # опасно!
if order == 'rating':
    queryset = queryset.order_by('rating')
page = request.GET.get('page') or 1
try:
  page = int(page)
except ValueError:
  return HttpResponseBadRequest()
```

## GET и POST - объекты QueryDict
/path/?id=3&id=4&id=5

#### Получение множественных значений
```
id = request.GET.get('id') # id = 5
id = request.GET.getlist('id') # id = [3,4,5]
```
#### Сериализация
```
qs = request.GET.urlencode()
# qs = 'id=3&id=4&id=5'
```

## Получение HTTP заголовков
```
user_agent = request.META.get('HTTP_USER_AGENT')
user_ip = request.META.get('HTTP_X_REAL_IP')
if user_ip in None:
    user_ip = request.META.get('REMOTE_ADDR')
```

## Получение Cookie
```
is_visited = request.COOKIES.get('visited')
```
