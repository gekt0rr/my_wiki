# Полезные команды Linux
## Отключение спящего режима при закрытии крышки ноутбука

Вы можете отключить спящий режим при закрытии крышки ноутбука, изменив настройки файла `logind.conf`. Для этого выполните следующие команды:

```
nano /etc/systemd/logind.conf
```

В файле `logind.conf` найдите строку `#HandleLidSwitch=suspend` и замените ее на `HandleLidSwitch=ignore`.

Сохраните файл и перезапустите службу `systemd-logind`:

```
sudo service systemd-logind restart
```

## Настройка sudo

Для добавления вашего пользователя в список пользователей, которые могут выполнять команды от имени суперпользователя, вы можете использовать команду `visudo`. Например, чтобы добавить пользователя `basov`, вы можете выполнить следующие шаги:

```
sudo visudo
```

Добавьте следующую строку в файл:

```
basov   ALL=(ALL:ALL) NOPASSWD:ALL
```

Сохраните файл и закройте редактор. Теперь пользователь `basov` может выполнять любые команды от имени суперпользователя без ввода пароля.

## ip_forward
```
echo 1 > /proc/sys/net/ipv4/ip_forward
```
## хрень
```
sudo apt update
sudo apt install gparted rsync
sudo gparted
lsblk
sudo mkdir /sda_6 /sda_7
sudo mount /dev/sda6 /sda_6
sudo mount /dev/sda7 /sda_7
sudo rsync -a /sda_7/* /sda_6/home/
sudo nano /sda_6/etc/fstab
```

Шаг 1. Загрузка с Live CD.

1. Загрузитесь с помощью Live CD и запустите командную оболочку (например, bash).

Шаг 2. Установка необходимых пакетов.

1. Установите пакеты gparted и rsync с помощью команды:

```
sudo apt update
sudo apt install gparted rsync
```

Шаг 3. Изменение разделов с помощью GParted.

1. Запустите GParted, введите команду:

```
sudo gparted
```

2. Уменьшите размер раздела /dev/sda6 до минимально допустимого.
    
3. Увеличьте размер раздела /dev/sda7 до максимально допустимого.
    
4. Нажмите на кнопку "Применить" (зеленая галочка).
    

Шаг 4. Копирование папки /home и изменение файла fstab.

1. Создайте папки для монтирования разделов:

```
sudo mkdir /mnt/sda6 /mnt/sda7
```

2. Смонтируйте разделы /dev/sda6 и /dev/sda7 в соответствующие папки:

```
sudo mount /dev/sda6 /mnt/sda6
sudo mount /dev/sda7 /mnt/sda7
```

3. Скопируйте содержимое папки /home с раздела /dev/sda7 на раздел /dev/sda6 с помощью rsync:

```
sudo rsync -a /mnt/sda7/home/ /mnt/sda6/home/
```

4. Отредактируйте файл /mnt/sda6/etc/fstab с помощью текстового редактора (например, nano) и измените точку монтирования корневого раздела на /dev/sda7:

```
sudo nano /mnt/sda6/etc/fstab
```

Замените строку:

basic

```
/dev/sda6 /               ext4    errors=remount-ro 0       1
```

на строку:

basic

```
/dev/sda7 /               ext4    errors=remount-ro 0       1
```

5. Сохраните и закройте файл fstab.

Шаг 5. Перезагрузка системы.

1. Отмонтируйте разделы /dev/sda6 и /dev/sda7:

```
sudo umount /mnt/sda6
sudo umount /mnt/sda7
```

2. Перезагрузите систему с помощью команды:

```
sudo reboot
```