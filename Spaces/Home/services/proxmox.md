# Proxmox
## Установка/настройка
### Подготовка сервера для установки Proxmox

Перед установкой Proxmox необходимо обновить существующие пакеты и настроить файл `/etc/hosts`. Вы можете выполнить следующие команды:

```
sudo apt update
sudo apt upgrade
sudo nano /etc/hosts
```

В файле `/etc/hosts` вставьте следующую строку:

```
192.168.7.117   proxmox.zenchaten.local proxmox
```

Сохраните файл и закройте редактор.

### Установка Proxmox

Для установки Proxmox вы можете выполнить следующие команды:

pgsql

```
sudo su

echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list

wget https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg

apt update && apt full-upgrade 
apt install pve-kernel-5.15
reboot
```

Команды выше добавляют репозиторий Proxmox, обновляют список пакетов, устанавливают ядро Proxmox, выполняют полное обновление и перезагружают сервер.

Затем вы можете выполнить следующие команды для установки Proxmox:

basic

```
sudo su
apt install proxmox-ve postfix open-iscsi 
apt remove os-prober

passwd root
```

Команда `apt install` устанавливает пакеты `proxmox-ve`, `postfix` и `open-iscsi`, которые необходимы для работы Proxmox. Команда `apt remove` удаляет пакет `os-prober`, который может вызывать проблемы при загрузке.

Затем необходимо установить пароль для пользователя `root`.

### Настройка сети

Подробнее про настройку сетевых интерфейсов можно почитать [[Debian, как настроить сеть в текстовом файле]]. В данном случае мы будем настраивать мост. Для начала установим необходимые пакеты.
```
sudo apt install vlan bridge-utils ifenslave
```
Для настройки сетевых интерфейсов в Proxmox вы можете использовать файл `/etc/network/interfaces`. Вот содержимое этого файла, основанное на вашей конфигурации:

```
auto lo
iface lo inet loopback

auto enp2s0f0
iface enp2s0f0 inet manual
iface enp1s0f0 inet manual
iface enp1s0f1 inet manual
iface enp2s0f1 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.7.117/24
        gateway 192.168.7.1
        bridge-ports enp2s0f0
        bridge-stp off
        bridge-fd 0
```

В файле `/etc/network/interfaces` создается мост `vmbr0` на базе интерфейса `enp2s0f0`. Мост настраивается для использования статического IP-адреса `192.168.7.117/24` с шлюзом по умолчанию `192.168.7.1`.

### Настройка DNS

Для настройки DNS в Proxmox вы можете использовать файл `/etc/resolv.conf`. Вот содержимое этого файла, основанное на вашей конфигурации:

```
nameserver 62.148.128.1
nameserver 8.8.8.8
```

В файле `/etc/resolv.conf` задаются DNS-серверы `62.148.128.1` и `8.8.8.8`.

### Завершение установки

После установки Proxmox вы можете получить доступ к веб-интерфейсу Proxmox по адресу `https://192.168.7.117:8006`.

## Полезные команды
восстановить виртуалку в proxmox из архива
```
qmrestore /tmp/vzdump-qemu-100-2023_02_04-18_33_17.vma.gz 100 -storage local
```
другое