Общее
=
```
VBoxManage list    vms|runningvms|ostypes|hostdvds|hostfloppies|
                    intnets|bridgedifs|hostonlyifs|natnets|dhcpservers|
                    hostinfo|hostcpuids|hddbackends|hdds|dvds|floppies|
                    usbhost|usbfilters|systemproperties|extpacks|
                    groups|webcams|screenshotformats
vboxmanage list ostypes - Список поддерживаемых типов виртуалок 
```

**Просмотр информации о виртуальной машине**

> vboxmanage showvminfo vm

**Изменить директорию где храняться вирутальные машины**
> vboxmanage setproperty machinefolder /path/to/directory/

#### Запуск виртуальной машины с именем _vm_ без GUI 

>vboxmanage startvm --type headless vm

#### Выключение виртуальной машины (2 спосбоба)

> VBoxManage controlvm vm acpipowerbutton
> VBoxManage controlvm vm poweroff

**Разблокировать виртуальную машину для изменения конфигурации при ошибке**

> vboxmanage startvm vm --type emergencystop

**Подключить VBoxGuestAdditions образ**
```
скачиваем для своей версии
wget http://download.virtualbox.org/virtualbox/5.1.24/VBoxGuestAdditions_5.1.24.iso
и монтируем
vboxmanage storageattach Win3 --storagectl ide-controller --port 0 --device 1 --type dvddrive --medium /usr/home/user/VirtualBox\ VMs/VBoxGuestAdditions_5.1.24.iso
```

***

### Управление ВМ -> VBoxManage controlvm "vm" ... 

**Отключить сетевой кабель на интерфейсе 1**
> VBoxManage controlvm "vm" setlinkstate1 off

**Отправить CTRL-ALT-DEL**
>VBoxManage controlvm testMachine keyboardputscancode 1d 38 53
***


Сеть
=
**Пробросить порт 3389 с виртуальной машины на 127.0.0.1:33890 хостовой**

> VBoxManage modifyvm "vm" --natpf1 "guestrdp,tcp,127.0.0.1,33890,,3389"

**Удалить это же правило по имени - guestrdp**

> VBoxManage modifyvm xp --natpf1 delete guestrdp

**Подключить nat интерфейс**

> VBoxManage modifyvm "guest" --nic1 nat

**Подключить hostonly адептер и связать его с хостовым**

> VBoxManage modifyvm "vm" --nic1 hostonly
> VBoxManage modifyvm "vm" --hostonlyadapter1 'vboxnet1'

**Отключить сетевой интерфейс №1**

> VBoxManage modifyvm "guest" --nic1 none

#### NAT

**Список NAT-сетей**

>vboxmanage natnetwork list

**Создание новой сети:**

> vboxmanage natnetwork add --netname UbuntuNat --network 10.0.4.0/24 --enable --dhcp on --ipv6 off

**Редактирование сети:**

>vboxmanage natnetwork modify --netname UbuntuNat --ipv6 on

**Удаление сети:**

>vboxmanage natnetwork remove --netname UbuntuNat

**Подключение виртуалок к NAT-сети:**

>vboxmanage modifyvm ubuntu1 --nic2 natnetwork --nat-network2 UbuntuNat
>vboxmanage modifyvm ubuntu2 --nic2 natnetwork --nat-network2 UbuntuNat

#### Sharedfolder

**Подключить директорию из хоста в виртуалку**

>vboxmanage sharedfolder add vm --name vmail --hostpath /var/vmail/

**Отключить директорию из хоста в виртуалку**

>vboxmanage sharedfolder remove vm --name vmail 

***

### VNC

**Включаем доступ к ВМ по VNC**

> vboxmanage modifyvm ubuntu1604 --vrde on
> vboxmanage modifyvm ubuntu1604 --vrdeaddress 127.0.0.1 
> vboxmanage modifyvm ubuntu1604 --vrdeport 3001
> vboxmanage modifyvm ubuntu1604 --vrdeproperty VNCPassword="secret"

**Подключаемся**

>xvncviewer 127.0.0.1:3001

***

### Экспорт, Импорт, Клонирование, Удаление

**Переименовать ВМ (при этом переименовывается и ее каталог):**

>vboxmanage modifyvm ubuntu1604 --name ubuntu1

**Создать полный клон ВМ:**

> vboxmanage clonevm ubuntu1 --name ubuntu2 --register

**Удалить виртуалку и все ассоциированные с ней файлы, в том числе и диски:**

>vboxmanage unregistervm ubuntu2 --delete

**Экспорт:**

>vboxmanage export ubuntu2 --output ubuntu2.ova

**Импорт:**

>vboxmanage import ubuntu2.ova

#### импорт с переопределением имени:

>vboxmanage import ubuntu2.ova --vsys 0 --vmname ubuntu2

***

Примеры
=
### Создание новой виртуалки с Ubuntu
```
vboxmanage createvm --name ubuntu1604 --ostype Ubuntu_64 --register

vboxmanage modifyvm ubuntu1604 --cpus 1 --memory 512 --audio none \
  --usb off --acpi on --boot1 dvd --nic1 nat

vboxmanage createhd \
  --filename /home/eax/virtualbox/ubuntu1604/ubuntu1604.vdi \
  --size 10000

vboxmanage storagectl ubuntu1604 --name ide-controller --add ide

vboxmanage storageattach ubuntu1604 --storagectl ide-controller \
  --port 0 --device 0 --type hdd \
  --medium /home/eax/virtualbox/ubuntu1604/ubuntu1604.vdi

vboxmanage storageattach ubuntu1604 --storagectl ide-controller \
  --port 0 --device 1 --type dvddrive \
  --medium /home/eax/data/iso/ubuntu-16.04.1-server-amd64.iso
```


### Создание Windows 2003 server x64
```
VBoxManage createvm --name Win3 --ostype Windows2003_64 --register

VBoxManage modifyvm Win3 --cpus 2 --memory 4096 --floppy disabled --audio none  \
	--nic1 nat --vram 4 --accelerate3d off --boot1 dvd --acpi on --cableconnected1 on

vboxmanage createhd --filename /home/user/VirtualBox\ VMs/Win3/Win3_hdd0.vdi --size 20000

vboxmanage storagectl Win3 --name ide-controller --add ide

vboxmanage storageattach Win3 --storagectl ide-controller --port 0 \
	--device 0 --type hdd --medium /home/user/VirtualBox\ VMs/Win3/Win3_hdd0.vdi

vboxmanage storageattach Win3 --storagectl ide-controller --port 0 \
	 --device 1 --type dvddrive --medium /usr/home/user/MS.2003.R2.Ent.x64/zenx642a.iso

\Настраиваем VNC
\Запускам, устанавливаем ОС
\Потом подключаем второй CD диск

vboxmanage storageattach Win3 --storagectl ide-controller --port 0 --device 1 \
	--type dvddrive --medium /usr/home/user/MS.2003.R2.Ent.x64/zenx642b.iso

\прокидываем порт RDP

```





































