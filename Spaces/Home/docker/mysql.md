С помощью следующей конфигурации docker-compose можно быстро развернуть контейнер с MYSQL и использовать созданную базу данных для локальной разработки и не только.

```xml
version: '3.1'

services:
  mysql_db:
    image: mysql:8
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: stage
      MYSQL_USER: example
      MYSQL_PASSWORD: secret2
    volumes:
      - ./dbdata:/var/lib/mysql/
```

Сохраняем данный код в файле **docker-compose.yml** и запускаем командой **docker-compose up -d**.

После запуска наша база данных из переменной MYSQL_DATABASE будет доступна по адресу: 127.0.0.1:3306 и мы сможем подключаться к ней с помощью любого клиента указав данные для подключения из переменных MYSQL_USER, MYSQL_PASSWORD.

- **MYSQL_ROOT_PASSWORD** - пароль для пользователя root;
- **MYSQL_DATABASE** - база данных которая будет создана при создании образа;
- **MYSQL_USER** - пользователь который будет создан при создании образа. Данный пользователь будет иметь полный доступ к базе данной MYSQL_DATABASE;
- **MYSQL_PASSWORD** - пароль пользователя MYSQL_USER;

Чтобы остановить контейнер без его удаления используем **docker-compose stop**. Если требуется удалить контейнер и его сеть используем **docker-compose down**.