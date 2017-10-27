Настройка **spf** и **dkim** для почтового сервера
=
## Проверка на спам и наличие нужных настроек
http://www.mail-tester.com/ 

## SPF
##### Для настройки необходимо создать запись типа **TXT**
```
v=spf1 ip4:111.111.111.11/32  -all

```
здесь указано:
 * версия spf1
 * ip сервера или подсети который может отправлять от имени домена
 * -all запрет отправки всем остальным
```
v=spf1 +a +mx -all
```
+a - разрешает прием писем от хоста, IP-адрес которого указан в A-записи
+mx -  разрешает прием писем, если отправляющий хост указан в одной из MX-записей
```
v=spf1 ip4:124.31.25.75 mx/24 a:test.com.ua/24 +a:smtp.mail.ru include:gmail.com ~all
```
mx/24 - в список разрешенных отправителей входят все IP-адреса, находящихся в тех же сетях класса С, что и MX-ы домена
a:test.com.ua/24 - в список разрешенных отправителей входят все IP-адреса, находящихся в тех же сетях класса С, что и А-записи домена test.com.ua;
+a:smtp.mail.ru - то же, что и a:smtp.mail.ru. Принимать от smtp.mail.ru;
include:gmail.com - принимать письма с серверов, разрешенных SPF-записями gmail.com;
~all - принимать письма со всех остальных серверов, но помечать их как СПАМ
##### Проверка записи
```
dig txt domain.com |grep spf
--
domain.com.           36908   IN      TXT     "v=spf1 ip4:111.111.111.111/32  -all"
```

## DKIM
##### Настройка
**1. Генерируем приватный(секретный ключ)**
```
openssl genrsa -out /usr/local/etc/exim/example.com.key 2048
```
получаем текстовый файл с секретным ключем, который остается на сервере.
Для этого ключа, получим публичный ключ, который необходимо будет прописать в настройках домена.
**2.  Получаем публичный ключ из приватного**
```
openssl rsa -in /usr/local/etc/exim/example.com.key -pubout
```
Текст ключа будет выведен в консоль, откуда его можно будет скопировать
**3. Настройка записей DNS**
необходимо создать две **TXT** записи для cуб домена:
- mail._domainkey - где **mail** произвольное имя, которое будет прописано в настройках почтового сервера, т.н. dkim селектор
- _adsp._domainkey 

**запись типа _TXT_ для субдомена mail._domainkey.domain.com**
```
v=DKIM1; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxweK5+F42YFdkRQqZI922yCQc68pdgWUhYr7CSeCxcQ5P10oKi2mXtYxOOKmeA7NExY6U5jrGEJ6gyrBfsJPUp25PwApbpGZ+cIRB2/N3KcevEfiOVyyO7f7WduM+jmz69nyLozWO7QM2QjCCxWx6aKZm
```
p= Сюда вставляется публичный ключ который мы получили ранее.

**запись типа _TXT_ для субдомена _adsp._domainkey.domain.com**
```
dkim=all
```
all — Все письма должны быть подписаны

**4 Настройка почтового сервера EXIM**
В файл configure добавить следующие опции:
```
DKIM_DOMAIN = ${lc:${domain:$h_from:}}
DKIM_KEY_FILE = /etc/exim4/dkim/DKIM_DOMAIN.key
DKIM_PRIVATE_KEY = ${if exists{DKIM_KEY_FILE}{DKIM_KEY_FILE}{0}}
DKIM_SELECTOR = email
```
DKIM_KEY_FILE - может быть для каждого домена свой или указать какой то конректный
DKIM_SELECTOR = mail - это имя субдомена **mail._domainkey**, если выбрали другой то необходимо указать его

**Добавляем эти настройки к smtp транспорту**
находим секцию **remote_smtp**
редактируем:
``` 
remote_smtp:
                driver 			= smtp
                dkim_domain		= DKIM_DOMAIN
                dkim_selector		= DKIM_SELECTOR
                dkim_private_key	= DKIM_PRIVATE_KEY
```
Важно что бы объявленные выше переменные совпадали.
Сохраняем конфиг и перезагружаем сервис почтового сервера
```
service exim restart
```

Пробуем отправить тестовое письмо, любому внешнему почтовику и проверим есть ли сделанные настройки  в заголовках. 
```
DKIM-Signature: v=1; a=rsa-sha256; q=dns/txt; c=relaxed/relaxed; d=domain.com; s=email; h=Message-ID:Subject:To:From:Date:Content-Transfer-Encoding:Content-Type:MIME-Version; bh=trocwoB/zQMB0Mc33eYqusUcnwFHX8CcbEOWqUo8y8Q=; b=Ujbh+lAxazODnuxge2kQ20UikahIu1h3jOynHXBVQ+S2gx9S
```