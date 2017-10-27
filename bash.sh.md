bash, sh
=

```
for f in *.docx; do pandoc -f docx -t markdown -o "${f%.docx}".md $f; done
```
конвертируем все файлы **.docx**  с помощью утилиты **pandoc**  в текущей дериктории в формат **.md**, меняя расширение файла.

```
$ sudo apt-get install ghostscript
$ gs -dNOPAUSE -sDEVICE=jpeg -dFirstPage=1 -dLastPage=5 -sOutputFile=output%d.jpg -dJPEGQ=100 -r200 -q intput.pdf -c quit
```
сделать из pdf файла jpg картинки
