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