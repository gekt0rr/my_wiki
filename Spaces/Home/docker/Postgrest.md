# Postgrest
  
[PostgREST](https://postgrest.org/en/stable/) - это веб-сервер, который **превращает базу данных PostgreSQL в RESTful API**. Это означает, что вы можете использовать любой клиент REST, такой как curl или Postman, для доступа к данным в вашей базе данных PostgreSQL.

PostgREST также поддерживает различные функции, такие как генерация представлений, фильтрация, сортировка и pagination. Это делает его очень мощным инструментом для создания RESTful API для ваших данных PostgreSQL.
## Compose
```yaml
version: '3.8'
services:
  postgrest:
    container_name: postgrest_container
    image: postgrest/postgrest
    restart: always
    ports:
      - "3000:3000"
    environment:
      PGRST_DB_URI: "postgres://root:root@172.22.0.3:5432/test_db"
      PGRST_DB_SCHEMA: "public"
      PGRST_DB_ANON_ROLE: "root"
      PGRST_DB_POOL: 10
      PGRST_SERVER_HOST: "0.0.0.0"
      PGRST_SERVER_PORT: 3000
    networks:
      - postgres_default

networks:
  postgres_default:
    external: true
```
