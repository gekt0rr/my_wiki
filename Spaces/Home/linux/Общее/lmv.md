# Как увеличить размер раздела LVM в Linux

Опубликовано: 2 января 2022 г. by [Pradeep Antil](https://www.linuxbuzz.com/author/pradeep/ "Просмотреть все сообщения Прадипа Антила")

Привет, технические специалисты! Одним из преимуществ раздела LVM является то, что его размер можно изменить онлайн без каких-либо простоев. Здесь изменение размера означает увеличение и уменьшение размера раздела lvm. Однако не рекомендуется уменьшать раздел lvm, так как это может привести к повреждению данных.

В этом посте мы покажем, как расширить или увеличить размер раздела LVM в Linux с помощью команды lvextend . Для демонстрации у нас есть раздел /dev/vg01-lv01 размером 10 ГБ, смонтированный в /data папке



```
$ df -Th /data/
Filesystem            Type  Size  Used Avail Use% Mounted on
/dev/mapper/vg01-lv01 ext4  9.8G   37M  9.3G   1% /data
$
```

Предположим, мы хотим увеличить размер с 10 ГБ до 15 ГБ. Кроме того, у нас нет свободного места в группе томов vg01 .

```
$ sudo vgs vg01
  VG   #PV #LV #SN Attr   VSize   VFree
  vg01   1   1   0 wz--n- <10.00g 4.00m
$
```

Давайте углубимся в шаги,

## Шаг 1) Подключите новый диск к системе Linux.

Подключите диск объемом 5 ГБ к вашей Linux-системе. После подключения убедитесь, что он доступен на уровне операционной системы.

Запустите команду lsblk и ' fdisk -l ', чтобы проверить,

```
$ lsblk | grep -i sd
```

[![lsblk-новый-диск-Linux](https://www.linuxbuzz.com/wp-content/uploads/2022/01/lsblk-new-disk-linux.png "lsblk новый диск")](https://www.linuxbuzz.com/wp-content/uploads/2022/01/lsblk-new-disk-linux.png)

```
$ sudo fdisk -l
```

[![fdisk-l-command-linux](https://www.linuxbuzz.com/wp-content/uploads/2022/01/fdisk-l-command-linux-1024x602.png "Команда fdisk l в Linux")](https://www.linuxbuzz.com/wp-content/uploads/2022/01/fdisk-l-command-linux.png)

Вышеприведенные данные подтверждают, что новый диск /dev/sdc размером 5 ГБ обнаружен на уровне ОС.

## Шаг 2) Создайте физический том (PV)

Создайте PV на диске /dev/sdc с помощью команды pvcreate,

```
$ sudo pvcreate /dev/sdc
  Physical volume "/dev/sdc" successfully created.
$ sudo pvs /dev/sdc
  PV         VG Fmt  Attr PSize PFree
  /dev/sdc      lvm2 ---  5.00g 5.00g
$
```

## Шаг 3) Расширьте группу томов

Чтобы расширить группу томов, добавьте новый pv (/dev/sdc) в группу томов (vg01) с помощью vgextend

```
$ sudo vgextend vg01 /dev/sdc
  Volume group "vg01" successfully extended
$
```

После расширения группы томов проверьте ее размер с помощью команды vgs:

```
$ sudo vgs
  VG   #PV #LV #SN Attr   VSize  VFree
  vg01   2   1   0 wz--n- 14.99g 5.00g
$
```

Вывод выше подтверждает, что теперь у нас есть 5 ГБ свободного места в группе томов.

## Шаг 4) Расширьте логический том (LV)

Определите LV с помощью команды lvs,

```
$ sudo lvs
  LV   VG   Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv01 vg01 -wi-ao---- 9.99g
$
```

В случае файловой системы Ext4 LVM выполните следующие команды, чтобы увеличить размер в режиме онлайн.

```
$ sudo lvextend -L +4.99G /dev/vg01/lv01
$ sudo resize2fs /dev/vg01/lv01
$ df -Th /data
```

[![Расширить-LVM-раздел с помощью команды lvextend](https://www.linuxbuzz.com/wp-content/uploads/2022/01/Extend-LVM-Partition-with-lvextend-command-1024x277.png "Расширьте раздел LVM с помощью команды lvextend")](https://www.linuxbuzz.com/wp-content/uploads/2022/01/Extend-LVM-Partition-with-lvextend-command.png)

Отлично, приведенный выше вывод ясно подтверждает, что размер файловой системы был увеличен с 10 ГБ до 15 ГБ.

В случае файловой системы XFS LVM выполните следующую команду:

```
$ sudo lvextend -L +4.99G /dev/vg01/lv01 -r
```

Приведенная выше команда расширит и изменит размер файловой системы за один раз.

Вот и все из этого поста, надеюсь, вы нашли его информативным. Пожалуйста, поделитесь своими отзывами и вопросами в разделе комментариев ниже.