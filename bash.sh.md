bash, sh
=

```
for f in *.docx; do pandoc -f docx -t markdown -o "${f%.docx}".md $f; done
```
конвертируем все файлы **.docx**  с помощью утилиты **pandoc**  в текущей дериктории в формат **.md**, меняя расширение файла.
___
```
pandoc -s -S --latex-engine=xelatex --filter=pandoc-citeproc -o '$file_base_name.pdf' '$file_name'
```
MD to PDF
___
```
$ sudo apt-get install ghostscript
$ gs -dNOPAUSE -sDEVICE=jpeg -dFirstPage=1 -dLastPage=5 -sOutputFile=output%d.jpg -dJPEGQ=100 -r200 -q intput.pdf -c quit
```
сделать из pdf файла jpg картинки
___
```
rsync -rvz -e 'ssh -p 2222' --progress --remove-sent-files ./dir user@host:/path
```
rsync через ssh на произвольном порту
