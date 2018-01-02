HttpResponse
=
```
from django.http import HttpResponse

# создание ответа
response = HttpResponse("<html>Hello world</html>")

# установка заголовков
response['Age'] = 120

# установка всех параметров
response = HttpResponse(
              content = '<html><h1>Ничего</h1></html>',
              content_type = 'text/html',
              status = 404,
            )
```


## Установка HTTP заголовков
```
response = HttpResponse(my_data,
              content_type='application/vnd.ms-excel')
response['Content-Disposition'] = 'attachment; filename="foo.xls"'
```


## Установка Cookie
```
response = HttpResponse(html)
response.set_cookie('visited', '1')
```
