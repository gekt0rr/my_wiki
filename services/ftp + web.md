# ftp + web
## Установка FTP-сервера и web-сервера

Для установки FTP-сервера vsftpd и web-сервера Apache вы можете выполнить следующие команды:

```
sudo apt-get install vsftpd
sudo nano /etc/vsftpd.conf
```

В файле `/etc/vsftpd.conf` необходимо раскомментировать строку `anonymous_enable=YES`, чтобы разрешить анонимный доступ к FTP-серверу.

Затем необходимо перезапустить службу vsftpd:

```
sudo systemctl restart vsftpd.service
```

Для установки web-сервера Apache вы можете выполнить команду:

```
sudo apt-get install apache2
```

## Создание директории для загрузки файлов

После установки web-сервера Apache вам необходимо создать директорию, в которой будут храниться файлы, доступные для загрузки. Вы можете использовать каталог `/var/www/html/` для этой цели, как указано в вашей заметке. Вы можете перейти в этот каталог с помощью команды:

```
cd /var/www/html/
```

Затем вы можете загрузить файл kickstart на FTP-сервер и разместить его в директории `/var/www/html/`. После этого файл будет доступен по URL-адресу `http://<IP-адрес сервера>/ks.cfg`, где `<IP-адрес сервера>` - это IP-адрес вашего веб-сервера.

## Compose
```yaml
version: '3.9'
services:
  apache:
    image: webdevops/apache:latest
    container_name: my-apache-app
    ports:
    - '20080:80'
    volumes:
      - ./html:/var/www/
      - ./conf_apache:/usr/local/apache2/
    networks: 
      - nginx-proxy-manager_default

networks:
  nginx-proxy-manager_default:
    external: true
```
> [!warning]
> Если сайт не поддерживает кирилицу, то необходимо добавить `AddDefaultCharset utf-8` сразу после строки `<VirtualHost *:80>` в файле директории `sites-enabled`