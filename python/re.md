regular expression examples
=

### Online ресурсы
- https://regex101.com/
- https://pythex.org/

**Функции**
```
re.match - проверяет входит ли шаблон в строку если нет возвращает None
re.search - находит первую подстроку которая подходит под шаблон
re.findall - находит все подстроки которые подходят под шаблон
re.sub -  заменит все подстроки которые подходят под шаблон
re.split - разделить строку по указанному шаблону
```

```
# re.IGNORECASE
re.findall('test', 'test TEST tESt', re.IGNORECASE) # ['test', 'TEST', 'tESt']

```

### Примеры
```
pt = r'a[abc]c' ; s = 'abc, acc, aac'

re.match(pt, s) # <_sre.SRE_Match object; span=(0, 3), match='abc'>
re.search(pt, s) # <_sre.SRE_Match object; span=(0, 3), match='abc'>
re.findall(pt,s) # ['abc', 'acc', 'aac']
re.sub(pt, 'abc', s,) # 'abc, abc, abc'

```


## Метасимволы
**. ^ $ * + ? { } [ ] \ | ( )**
```
[abcd] = [a-d] - любой **один** символ из последовательности a,b,c,d
[^abcd] = [^a-d] - любой символ кроме указанных
\d = [0-9] - любая цифра
\D = [^0-9] - любой символ кроме цифр
\s = [ \t\n\r\f\v] - любой пробельный символ
\S = [^ \t\n\r\f\v] - любой не пробельный символ
\w = [a-zA-Z-0-9_] - любой символ из списка букв, цифр или _
\W = [a-zA-Z-0-9_] - любой символ кроме букв, цифр или _
. - один любой символ
\b - начало или конце слова
```
```
* - указывает на любое количество символа которой стоит перед *
b* - b, bb, bbbb,bbbbbb, или отсутсвие b
ab*c - ac, abc, abbbc, abbbbbbbc..

+ - тоже что и * но миниму 1 символ
ab+c -abc, abbbc, abbbbbbbc..

? - 0 или 1 символ указанный перед ?
ab?c - abc или ac

{n} - n символов указанных перед {n}
ab{2}c - abbc
{n,m} - любое количество символов от n до m
ab{2,4}c - abbc, abbbc, abbbbc
```

## Жадные совпадения
```
pt = r'a[ab]+a'
s = 'abaaba'
re.match(pt, s) # <_sre.SRE_Match object; span=(0, 6), match='abaaba'>
re.findall(pt, s) # ['abaaba']
# максимально возможное совпадение
```
## Нежадные совпадения
```
pt = r'a[ab]+?a'
s = 'abaaba'
re.findall(pt, s) # ['aba', 'aba']
```

## Группы
```
(text) - выбрать из строки подстроку
(text|test) - выбрать text или test

pt = r'(test|text)'
s = 'test text asdasa'
re.findall(pt, s) # ['test', 'text']

#Использование найденой группы
pt = r'(test)-\1' # \1 будет подставлено (test)
s = 'test-test asdasa'
re.match(pt, s) # <_sre.SRE_Match object; span=(0, 9), match='test-test'>
# заменя test-test на test 
re.sub(pt, r'\1', s,) # 'test asdasa'
#finadll возвращает только найденные группы
re.findall(r'((test)-\2)', s) # [('test-test', 'test')]
# первая группа это содержимое первых скобок
# вторая группа это совпадение вторых скобок
# вторая группа может состоять из строки 'test'
# первая группа может состоять из двух первых групп через дефис
```
**Ищем подстроку которая повторяется**
```
pt = r'.*(cat).*\1{1,}'
Паттерн используется для match
ищет подстроку cat которая повторяется 2 и более раз
(cat) группа 1, 
\1 еще раз группа 1, и {1,} - повторение группы 1 от одного раза и больше
Для двух совпадений можно не писать {1,} - 
r'.*(cat).*\1'
r"(.*cat.*){2,}"
Аналог для search
re.search(r"cat.*cat",'acat andcat cat')
```
**Поменять местами две первых буквы в каждом слове, состоящем хотя бы из двух букв**
```
line = 'There’ll be no more "Aaaaa"'
re.sub(r'\b(\w)(\w)', r'\2\1', line) #  'hTere’ll eb on omre "aAaa"'
r'\b(\w)(\w)' - ищем начало слова в котором есть две буквы, это две буквы попадают в группы 1 и 2
r'\2\1' - заменяем на конструкцию которая состоит сначала из группы 2 а потом из группы 1
```

**В строке заменить повторяющиеся буквы на одну**
```
line = 'There’ll be no more "Aaaaa"'
re.sub(r'(\w)\1+', r'\1', line) # 'There’l be no more "Aa"'

```
