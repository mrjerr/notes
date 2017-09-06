bash, sh
=

```
for f in *.docx; do pandoc -f docx -t markdown -o "${f%.docx}".md $f; done
```
конвертируем все файлы **.docx**  с помощью утилиты **pandoc**  в текущей дериктории в формат **.md**, меняя расширение файла.