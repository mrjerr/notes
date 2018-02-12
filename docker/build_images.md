# Build and create images

## Сборка образа из Dockerfile
```
docker build -t mrjerr/grab-ipython:2.0 .
```
Создаем образ из Dockerfile в текущей директории, присваиваем указанные теги

```
docker build img_name -f /path/to/Dockerfile
```
опция -f позволяет указать Dockerfile

## Загрузка образа на Docker Hub
 - регистрация на Docker Hub
 - авторизация в консоли
```
$ docker login
username and password

$ cd ~/.docker
$ cat config.json

{
        "auths": {
                "https://index.docker.io/v1/": {
                "auth": "bXJqZXJyOjXXXXXXXXXXeA=="
                }   
        }   
}%

$ docker logout
```
 - выгрузка образа в репозиторий
 ```
 $ docker push username/image_name:tag_name
 ```
 



## Сборка образа из контейнера
```
$ docker commit container_name image_name:tag
```

## Экспорт и импорт образа через файл
```
# Export
docker save image_name > image.tar
docker save -o image.tar image_name
docker save --output image.tar image_name
# compress
gzip image.tar
# image.tar.gz
```

```
#Import
docker load < image.tar
docker load --input image.tar my_image_name
docker load --input image.tar.gz
```

### Пример создания образа

- Создаем Dockerfile
```
FROM node:alpine

LABEL maintainer="Eugene Bardosh <mr.jerr@gmail.com>"

RUN npm -g install browser-sync \
    && mv /etc/profile.d/color_prompt /etc/profile.d/color_prompt.sh

ENV ENV="/etc/profile"
ADD aliases.sh /etc/profile.d/aliases.sh

EXPOSE 3000 
EXPOSE 3001

WORKDIR /src

ENTRYPOINT ["/bin/sh"]

```
- собираем образ 
```
docker build -t mrjerr/bsync .
```
- запускаем
```
docker run -it --rm --name bsync -P -v ${PWD}:/src mrjerr/bsync

```

Для того что бы запускался файл /etc/profile, нужно установить переменную окружения
ENV ENV="/etc/profile".

Так же добавляем в файл с алиасами, для быстрого запуска комманд. 

если запускать команду при старте контейнера:
```
docker run -it --rm --name bsync -P -v ${PWD}:/src mrjerr/bsync browser-sync start --server --files "." --no-open
```
невозможно остановить контейнер с помощью Ctrl+C.
Потому запускаем оболочку и уже там запускаем и останавливаем

При запуске пробрасываем локальный каталог в /src, куда сразу попадаем при запуске контейнера
