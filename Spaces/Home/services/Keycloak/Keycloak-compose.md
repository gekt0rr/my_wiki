# Keycloak
```yaml
version: '3'

volumes:
  postgres_data:
      driver: local

services:
  postgres:
      image: postgres
      volumes:
        - postgres_data:/opt/docker-compose/ceycloak/postgresql/data
      environment:
        POSTGRES_DB: keycloak
        POSTGRES_USER: keycloak
        POSTGRES_PASSWORD: password
  keycloak:
      image: quay.io/keycloak/keycloak:legacy
      environment:
        DB_VENDOR: POSTGRES
        DB_ADDR: postgres
        DB_DATABASE: keycloak
        DB_USER: keycloak
        DB_SCHEMA: public
        DB_PASSWORD: password
        KEYCLOAK_USER: admin
        KEYCLOAK_PASSWORD: Qwerty@123
        KEYCLOAK_FRONTEND_URL: https://auth.305-lab.duckdns.org/auth/
        # Uncomment the line below if you want to specify JDBC parameters. The parameter below is just an example, and it shouldn't be used in production without knowledge. It is highly recommended that you read the PostgreSQL JDBC d>
        #JDBC_PARAMS: "ssl=true"
      ports:
        - 7214:8080
#        - 9990:9990
      depends_on:
        - postgres

```