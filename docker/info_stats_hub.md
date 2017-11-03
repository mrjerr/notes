# Docker Info

**docker ps -sa** - вывод инф о котнейнерах включая занимаемое место

**docker system events** - вывод событий в реальном времени. Создание контейнера, запуск-остановка, подключение к контейнеру или удаление.

**docker system df** - вывод инф о размере занимаемом котнейнерами/обазами/томами
```
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              22                  2                   4.45GB              4.182GB (93%)
Containers          2                   0                   1.569GB             1.569GB (100%)
Local Volumes       5                   1                   39.07MB             1.149kB (0%)
```

**docker history 5309278ace62** - вывод инф о слоях из которых состоит образ и команд создания
```
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
5309278ace62        17 minutes ago      /bin/sh -c #(nop)  CMD ["ipython"]              0B
11f7244e796f        40 minutes ago      /bin/sh -c apk add --no-cache gcc musl-dev...   207MB
f9d3bdf9fc6c        About an hour ago   /bin/sh -c #(nop)  LABEL maintainer=Eugene...   0B
ddfd487077b4        7 days ago          /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
<missing>           7 days ago          /bin/sh -c #(nop) ADD file:92bfed3f8dfbee0...   3.99MB
```
**docker stats** - вывод инф о работающих контейнерах
```
CONTAINER           CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
0bd0ac3c5616        0.00%               0B / 0B             0.00%               1.51kB / 0B         0B / 102kB          0
```
**docker inspect <id, name, tag>** - выводит информацию в формате json об образе или контейнере, можно увидеть какой ip получил контейнер, работает ли он, с какими командами запущен, какие переменные окружения заданы и т.д.
_____
# Dockerhub
 1) Ресгитрация на сайте https://hub.docker.com
 2) Создаем на сайте репозиторий, заполняем **name** и **short description** - 
 **username/name_of_my_image** - пример имени репозитория
 3) Создаем и заполняем Dockerfile и создаем образ - 
 **docker build -t username/name_of_my_image .**
 4) **docker login** - выполняем комманду, вводим логин и пароль с которым регистрировались на сайте hub.docker.com
 5) выгружаем свой образ на DockerHub - 
 **docker push username/name_of_my_image**
_____
## Version & Info
```
> docker version
Client:
 Version:      17.06.0-ce
 API version:  1.30
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:20:04 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.06.0-ce
 API version:  1.30 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:18:59 2017
 OS/Arch:      linux/amd64
 Experimental: false
```
_____
```
> docker info
Containers: 2
 Running: 0
 Paused: 0
 Stopped: 2
Images: 49
Server Version: 17.06.0-ce
Storage Driver: aufs
 Root Dir: /home/d0cker/docker/aufs
 Backing Filesystem: extfs
 Dirs: 99
 Dirperm1 Supported: true
Logging Driver: json-file
...
Kernel Version: 3.16.0-4-amd64
Operating System: Debian GNU/Linux 8 (jessie)
OSType: linux
Architecture: x86_64
CPUs: 12
Total Memory: 19.6GiB
```
