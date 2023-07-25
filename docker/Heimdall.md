# Heimdall
Как следует из названия, [Heimdall Application Dashboard](https://hub.docker.com/r/linuxserver/heimdall/) — это панель инструментов для всех ваших веб-приложений. Однако это не обязательно должно быть ограничено приложениями, вы можете добавлять ссылки на все, что вам нравится.

Heimdall — элегантное решение для организации всех ваших веб-приложений. Он предназначен для этой цели, поэтому вы не потеряете свои ссылки в море закладок.

Почему бы не использовать его в качестве стартовой страницы браузера? У него даже есть возможность включить панель поиска с помощью Google, Bing или DuckDuckGo.

[![Демонстрационная анимация Хеймдалля](https://camo.githubusercontent.com/bcfd4f74c93b25bea7b14eacbafd649206bf754a3d4b596329968f0ee569cf3c/68747470733a2f2f692e696d6775722e636f6d2f4d72433451704e2e676966)](https://camo.githubusercontent.com/bcfd4f74c93b25bea7b14eacbafd649206bf754a3d4b596329968f0ee569cf3c/68747470733a2f2f692e696d6775722e636f6d2f4d72433451704e2e676966)
## Compose
```yaml
---
version: "2.1"
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdall
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /path/to/appdata/config:/config
    ports:
      - 5080:80
      - 5443:443
    restart: unless-stopped
```