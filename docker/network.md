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
 * **host**: этот драйвер дает контейнеру доступ к собственному пространству хоста (контейнер будет видеть и использовать тот же интерфейс, что и хост).
 * **macvlan**: этот драйвер дает контейнерам прямой доступ к интерфейсу и суб-интерфейсу (vlan) хоста. Также он разрешает транкинг.

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
На хосте появляется новый сетевой интерфейс, br-7408efa958a3, где 7408efa958a3 часть идентификатора созданной сети.
Контейнеры которые подключены к общей сети могу взаимодействовать друг с другом по имени
```
docker network rm 7408efa958a3 # удалить указанный network
```

**man docker-network-create** - выводит страницу man с пояснениями и примерами

## Взаимодействие контейнеров в созданной сети
```
# создаем новую сеть
> docker network create --subnet=10.1.0.0/16 --gateway=10.1.0.1 --ip-range=10.1.4.0/24 --driver=bridge --label=host4net bridge04
cbb69143f76adc31619c2339a6714470dbe4704b23d005a6b931d563a5ebed68
```
После создания новой сети мы получаем ее идентификатор.
Так же в системе создается интерфейс **br-cbb69143f76a**, br-12первых символов идентификатора.

Запустим два контейнера в созданной сети
```
> docker run -d --rm --name=c1 --net=bridge04 nginx
de9c5fb6afba766d336bd124f90b2a0b63d2aed250970034f1ad3f295bb6c035
> docker run -d --rm --name=c2 --net=bridge04 nginx 
885fe14ee0b19b191aa60065dbf71415f05b202649b1bc618bd37136e287bf6a
# так же можно вручную задать ip адрес, например:
> docker run -it --rm --net=bridge04 --ip 10.1.4.100 debian
```
Для каждого контейнера на хосте создается **veth** интерфейс, который подсоединяется к своему интерфейсу **br**. br-интерфейс выступает в роли L2-коммутатора. Посмотреть эту информацию можно с помощью утилиты brctl.
```
>>> /sbin/ifconfig|grep veth
veth45fd540 Link encap:Ethernet  HWaddr 32:aa:d5:a5:9d:b6
veth65c5e5a Link encap:Ethernet  HWaddr 7e:e8:d7:ae:a2:a5

>>> sudo brctl show br-cbb69143f76a
bridge name     		bridge id               STP enabled     interfaces
br-cbb69143f76a         8000.02421c0a08e1       no              veth45fd540
                        		                                veth65c5e5a
```

После выключения контейнера интерфейсы veth удаляются.
___
Так же, контейнеры подключенные к одной сети, могу взаимодействовать между собой по имени, занаданному при запуске опцией --name.
```
>>> docker exec -it c1 ping c2
PING c2 (10.1.4.1) 56(84) bytes of data.
64 bytes from c2.bridge04 (10.1.4.1): icmp_seq=1 ttl=64 time=0.147 ms
```
Можно использовать полное (fully-qualified) имя, которое складывается из названий контейнера и сети: c2.bridge04, как мы видим в ответе.

Docker предоставляет для контейнеров внутренний DNS сервер, который слушает локальный адрес 127.0.0.11, что автоматически прописывается в /etc/resolv.conf внутри контейнеров:
```
>>> docker exec c1 cat /etc/resolv.conf
nameserver 127.0.0.11
options ndots:0
```
