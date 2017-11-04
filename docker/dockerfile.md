# Dockerfile

## FROM - Базовый образ
Примеры:
```
FROM debian:stable
FROM centos:latest 
FROM alpine:3.5
```

## MAINTAINER, LABEL
Варианты, одно из
```
MAINTAINER Eugene Bardosh <mr.jerr@gmail.com>
LABEL authors=SeleniumHQ
LABEL maintainer="Eugene Bardosh <mr.jerr@gmail.com>"
```

## USER
Определяется пользователь под которым будут выполняться комманды в запущеном контейнере. Пользователь должен быть создан заранее.
Например:
```
RUN useradd -ms /bin/bash user1
USER user1
```
#### Все команды RUN обывленые после USER выполняться под указанным пользоватем. У него не будет прав root
При подключении к контейнеру выполниться вход под указанным пользователем.
Для того что бы подключиться к контейнеру под **root** используем опцию **-u 0**:
```
docker exec -u 0 -it <container_id> /bin/bash
```
## RUN
Содержит набор комманд, которые выполняются только на этапе сборки**(build)** образа
```
RUN set -x \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends ca-certificates wget \
	&& rm -rf /var/lib/apt/lists/* 
```
Выполнение каждой директивы RUN упаковывается в отдельный слой, потому используют группировку команд

## ENV
Используется что бы задавать переменные окружения, нужные для запуска приложения или сборки, установки зависимостей и т.п..
```
ENV MYSQL_VERSION 8.0.3-rc-1debian8
ENV MY_VAR some
```
Dokerfile может быть большой, при изменении версии, необходимо будет исправить только переменную, а не искать в коде прописанные версии.

```
RUN apt-get install -y mysql-community-client-core="${MYSQL_VERSION}" mysql-community-server-core="${MYSQL_VERSION}"
```

## CMD
Содержит команду и аргументы, которые выполняются при запуске контейнера
```
CMD ["nginx", "-g", "daemon off;"] 
```
Команда выполняется, если при запуске на задана другая.
**docker run <image_name> /bin/mycommand** - определение в CMD будет проигнорировано и запуститься указанная **/bin/mycommand**

## ENTRYPOINT
Так же как и CMD содержит команду и аргументы, которые выполняются при запуске контейнера.
При запуске контейнера и задании команды она будет проигнорирована и выполняться указанные в ENTRYPOINT
ENTRYPOINT ["nginx", "-g", "daemon off;"]

## ENTRYPOINT and CMD
Имеют два режима выполнения(и CMD и ENTRYPOINT):
 * Режим exec
**ENTRYPOINT ["/path/executable", "param1", "param2"]** - **/path/executable param1 param2**
 * Режим shell - переданная команда запускается с помощью **/bin/sh -c**
**ENTRYPOINT command param1 param2** - **/bin/sh -c** _command param1 param2_

**Режим exec** - предпочтительнее, по скольку сигналы отправляемые контейнеру перенаправляются процессу с PID 1, это тот процесс который запускается для указанных комманд.
В режиме exec не доступны переменные среды оболочки, например такие как $PATH
**Необходимо указывать путь польностью**
```
ENTRYPOINT ["ping", "www.google.com"] # CTRL+C завершит работу команды
ENTRYPOINT ping www.google.com # CTRL+C не завершит работу команды, по скольку /bin/sh -c не передаст сигнал для ping
ENTRYPOINT exec ping www.google.com # CTRL+C завершит работу команды
```
**Переопределение при запуске контейнера**
```
docker run --entrypoint [my_entrypoint] <image_name>
docker run <image_name> [command 1] [arg1] [arg2] 
```
Если заданы ENTRYPOINT и CMD - возможны различные варианты в зависимости от режима:

| 	|No ENTRYPOINT|	ENTRYPOINT execE pE	| ENTRYPOINT [“execE”, “pE”] |
| - | -|--| ---- |
|No CMD|	error, not allowed |	/bin/sh -c execE pE	| execE pE |
|CMD [“execC”, “pC”]|	execC pC|	/bin/sh -c execE pE|	exeE pE exeC pC
|CMD [“pC”, “p2C”]|	p1C p2C|	/bin/sh -c execE pE|	execE pE p1C p2C
|CMD execC pC|	/bin/sh -c execC pC|/bin/sh -c execE pE|	execE pE /bin/sh -c execC pC

Более подробно можно посмотреть - https://habrahabr.ru/company/southbridge/blog/329138/

## EXPOSE

Задаем номера портов, открытых на контейнере. Зная ip контейнера в локальной сети Docker можно обращаться напрямую к открытым портам или пробросить порт на любой порт хоста
```
EXPOSE 80
EXPOSE 443
```
Подразумевается что в контейнере существует процесс который случает указанные порты
пробрасываются открытые порты на хост при запуске с использованием параметра -p

 * **docker run -d -P nginx** - пробрасывает все открытые порты контейнера на случайные свободные порты хоста на всех интерфейсах
 * **docker run -d -p 127.0.0.1:8181:80 nginx** - пробрасывает 80-й порт контейнера на порт 8181 на интерфейсе 127.0.0.1 хоста
 * **docker port <container_id>** - выводит информацию куда какой порт проброшен.

**опция -p 127.0.0.1:8181:80** пробрасывает 80 порт котнейнера даже если он не открыт с помощью -  **EXPOSE 80**

## VOLUME
Нужны организации общих папок для нескольких контейнеров или для хранения данных отдельно от контейнера. VOLUME не удаляются при удалении контейнера

**docker run -v /opt/project my_image** или  в Dockerfile **VOLUME ["/opt/project"]** - на хосте создается volume, папка с уникальным идентификатором, в контейнере она доступна в /opt/project 
```
docker volume list # выводит созданные на хосте volume
docker volume inspect <volume_id> # выведет информацию о конректном volume. "Mountpoint" где на хосте расположена папка
```
**docker run -v /opt/project:/my_project my_image** - не создает VOLUME, а монтирует директорию хоста /opt/project в /my_project контейнера
