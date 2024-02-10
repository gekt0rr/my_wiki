# Postgres
PostgreSQL - популярная открытая реляционная база данных, широко используемая в веб-приложениях. Для удобства разработки часто бывает нужно развернуть PostgreSQL локально вместе с инструментами администрирования.

Docker Compose позволяет легко запустить PostgreSQL и pgAdmin для управления базой данных в отдельных контейнерах, используя готовые образы.
## Compose
```yaml
version: '3.8'
services:
  db:
    container_name: pg_container
    image: postgres
    restart: always
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
      POSTGRES_DB: test_db
    ports:
      - "5432:5432"
  pgadmin:
    container_name: pgadmin4_container
    image: dpage/pgadmin4
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: root
    ports:
      - "5050:80"
```