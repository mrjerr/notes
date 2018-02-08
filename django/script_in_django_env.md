Запуск скрипта в окружении django

```
import os
import sys
import django

def main():
  from PROJECT.models import MODEL_NAME


if __name__=="__main__":
    sys.path.append('/path/to/project/dir')
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "PROJECT.settings")
    django.setup()
    main()

```

Если нужно предварительно активировать окружение, sh скрипт

```
#! /bin/bash                                                                                                                                               
source /path/to/bin/activate
python /path/to/script.py
```

универсальный sh скрипт который активирует окружение и запускает скрипт

```
#!/bin/bash
cd `dirname $0`
source ../bin/activate
python "${@:1}"

```
