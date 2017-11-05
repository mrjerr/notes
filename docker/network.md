## NETWORK

 * **docker network ls** - выводит список существующих сетей
```
NETWORK ID          NAME                DRIVER              SCOPE
7a2bd3d32464        bridge              bridge              local
0fa068ce1c13        host                host                local
4ebed2e496fe        none                null                local
```
В Docker по умолчанию есть 4 сетевых драйвера (bridge, host, macvlan, overlay)
 * **bridge**: по умолчанию контейнеры запускаются в сети такого типа. На хосте появляется интерфейс docker0 через который возможно взаимодействовать с контейнерами
 * **overlay**: этот тип драйвера позволяет реализовать взаимодействие между контейнерами, которые выполняются на разных хостах

### bridge

docker inspect 7a2bd3d32464
```
        ..."Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
...
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0"
...       
```
Можно увидеть подсеть и шлюз, а так же параметры которые указывают bringe сеть по умолчанию. Здесь указан интерфейс docker0
___
Создаем еще одну bridge-сеть с именем my-net и подсетью 192.168.1.0/24
```
docker network create --driver bridge --subnet 192.168.1.0/24 --ip-range 192.168.1.0/24 my-net
```
на хосте появляется новый сетевой интерфейс, br-7408efa958a3, где 7408efa958a3 часть идентификатора созданной сети
```
docker network rm 7408efa958a3 # удалить указанный network
```

**man docker-network-create** - выводит страницу man с пояснениями и примерами

## Подключение контейнера в созданную сеть
```
# create network
docker network create --subnet=10.1.0.0/16 --gateway=10.1.0.1 --ip-range=10.1.4.0/24 --driver=bridge --label=host4net bridge04
# run and connect container to network bridge04
docker run -it --rm --net=bridge04 debian
# assing static ip address 
docker run -it --rm --net=bridge04 --ip 10.1.4.100 debian

```
