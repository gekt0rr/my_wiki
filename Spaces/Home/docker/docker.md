---
sticker: emoji//1f433
_filters: []
_contexts: []
_links: []
_sort:
  field: rank
  asc: false
  group: false
---
# Docker

Docker - это платформа, которая позволяет разработчикам создавать, тестировать и развертывать приложения в изолированных контейнерах. Контейнеры - это небольшие, самодостаточные единицы программного обеспечения, которые могут быть запущены на любой платформе, которая поддерживает Docker. Это делает их идеальным решением для разработки и развертывания приложений, которые должны быть масштабируемыми и переносимыми.

В этом разделе wiki вы найдете различные сценарии развертывания контейнеров Docker. Эти сценарии помогут вам быстро и легко развернуть приложения в контейнерах Docker. Вы также найдете в этом разделе различные ресурсы, которые помогут вам узнать больше о Docker.

## Установка Docker на хост

**1. Скачайте необходимые пакеты**
```
apt install vim net-tools tree ncdu bash-completion curl dnsutils htop iftop pwgen screen sudo wget nmon git
```
**2. Установите Docker**
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo apt install python3-pip -y
sudo apt install docker-compose
```
**3. Добавьте вашего пользователя в группу docker**
```
sudo gpasswd -a $USER docker
```
После выполнения этих шагов Docker будет установлен на вашем хост. Вы можете проверить это, выполнив следующую команду:
```
docker run hello-world
```
Если вы видите сообщение "Hello from Docker!", то Docker установлен и работает должным образом.

**4. Так же будет полезным установить portainer для удобства управления docker-инфраструктурой**

Сначала создайте том, который Portainer Server будет использовать для хранения своей базы данных:

```
docker volume create portainer_data
```
Затем загрузите и установите контейнер Portainer Server:

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```


## Различные полезные контейнеры
[[Semaphore]]