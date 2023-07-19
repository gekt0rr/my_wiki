# Ngnix Proxy Manager 
Nginx Proxy Manager (NPM) — это бесплатная и открытая платформа управления обратным прокси, балансировщиком нагрузки и менеджером HTTP-кеша. Это веб-интерфейс управления Nginx, который упрощает настройку и управление серверами Nginx.
## Настройка Lets Encrypt с использованием wildcard
WildCard сертификат — это тип цифрового сертификата, который позволяет защитить несколько доменов или поддоменов одним сертификатом. Это может быть полезно, если вы управляете несколькими веб-сайтами или веб-приложениями, которые используют один и тот же сервер.

Для примера будем использовать [duckdns](https://www.duckdns.org/). 
1. Заходим на сайт и авторизовываемся.
2. Делаем запись на локальный адрес.
    **Например**: blabla.duckdns.org -> 192.168.100.100
3. Заходим в веб панель NPM (81 порт, admin@example.com и пароль `changeme`) во вкладку "SSL Certificates"
4. Создаем запрос на сертификат с указание домена `blabla.duckdns.org` и wildcard `*.blabla.duckdns.org`
5. После получения Lets Encrypt сертификата можем смело создавать ресурсы за нашим NPM. Для включения режима tls во вкладке "SSL" выбираем полученный сертификат, включаем `Force SSL` и `HTTP/2 Support`
> [!warning]
> Некоторые сервисы, например такие как portainer, требую включения протокола `websocket`. Включается в первой вкладке при добавлении `Proxy hosts`

## docker compose
~~~yaml
version: '3.8'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      # - '21:21' # FTP

    # Uncomment the next line if you uncomment anything in the section
    # environment:
      # Uncomment this if you want to change the location of
      # the SQLite DB file within the container
      # DB_SQLITE_FILE: "/data/database.sqlite"

      # Uncomment this if IPv6 is not enabled on your host
      # DISABLE_IPV6: 'true'

    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt


~~~
