Декораторы в Django
=

```
from django.views.decorators.http import require_POST

@require_POST
def like(request):
  pass
```

- @require_GET – только GET запросы
- @require_POST – только POST запросы
- @login_required(login_url='/login/')
- @csrf_exempt – отключить проверку CSRF
