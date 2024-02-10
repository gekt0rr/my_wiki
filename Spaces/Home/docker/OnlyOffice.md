# OnlyOffice
OnlyOffice — это пакет офисных приложений, который является бесплатным и открытым исходным кодом. Он включает в себя текстовый процессор, таблицу, презентацию и редактор PDF. OnlyOffice доступен для Windows, macOS, Linux, Android и iOS.
## Установка
[Статья установки OnlyOffice Document Server](https://helpcenter.onlyoffice.com/ru/installation/docs-community-install-docker.aspx)
### Создание структуры каталогов для использования одинакового сертификата с Nextcloud (НЕОБЯЗАТЕЛЬНО)
Создаем каталоги
```
sudo mkdir /app /app/onlyoffice /app/onlyoffice/DocumentServer && cd /app/o*/D* && sudo mkdir logs data data/certs lib db
```
копируем сертификаты
~~~
cd /var/snap/nextcloud/<34073>/certs/live
cp cert.pem /app/onlyoffice/DocumentServer/data/certs/onlyoffice.crt
cp privkey.pem /app/onlyoffice/DocumentServer/data/certs/onlyoffice.key
~~~
### Команда для создания контейнера
```
sudo docker run -i -t -d -p 4443:443 --restart=always \
-v /app/onlyoffice/DocumentServer/logs:/var/log/onlyoffice \
-v /app/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data \
-v /app/onlyoffice/DocumentServer/lib:/var/lib/onlyoffice \
-v /app/onlyoffice/DocumentServer/db:/var/lib/postgresql \
-e JWT_ENABLED=false \
onlyoffice/documentserver
```
для включения JWT и задания ему пароля нужно заменить `-e JWT_ENABLED=false` на `-e JWT_SECRET=password`
### Тюнинг
Если используется nextcloud то необходимо необходимо добавить строку в его конфигурационный файл
```
'onlyoffice' => array ('verify_peer_off' => TRUE,),
```
Далее внутри самого контейнера `nano /etc/onlyoffice/documentserver/default.json` необходимо заменить значение  `"rejectUnauthorized": true` на `"rejectUnauthorized": false`