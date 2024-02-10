# Keycloak — больно не будет
полезные ссылки:
[Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki](https://habr.com/ru/companies/nixys/articles/752994)

## oAuth Keycloak + DokuWiki + FreeIPA

> OAuth — открытый протокол авторизации, который позволяет предоставить третьей стороне ограниченный доступ к защищённым ресурсам пользователя без необходимости передавать ей логин и пароль.

### Настройка User federation с FreeIPA в Keycloak

В вэб-админке Keycloak создаем новую область (realm), например **myrealm**, и переходим в раздел User federation. Там добавляем LDAP-провайдера

Вкладка Settings:

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 2](https://itdraft.ru/wp-content/uploads/2023/02/key-ldap1-1024x899.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 2")](https://itdraft.ru/wp-content/uploads/2023/02/key-ldap1.png)

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 3](https://itdraft.ru/wp-content/uploads/2023/02/key-ldap2-1024x832.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 3")](https://itdraft.ru/wp-content/uploads/2023/02/key-ldap2.png)
```
Вкладка Settings:
Console display name: freeipa.itdraft.ru
Vendor: Red Hat Directory Server
Connection URL: ldap://freeipa.itdraft.ru

# Во FreeIPA должен быть заведен пользователей keycloak с паролем mypass
Bind DN: uid=keycloak,cn=users,cn=accounts,dc=itdraft,dc=ru
Bind credentials: mypass
Edit mode: READ_ONLY
Users DN: cn=users,cn=accounts,dc=itdraft,dc=ru
Username LDAP attribute: uid
RDN LDAP attribute: uid
UUID LDAP attribute: uid
User object classes: inetOrgPerson, organizationalPerson

# [разрешаем доступ для группы gr-keycloak
User LDAP filter: (&(objectclass=person)(uid=*)(memberOf=cn=gr-keycloak,cn=groups,cn=accounts,dc=itdraft,dc=ru))
```
Проверяем подключение, сохраняем

Вкладка Mappers > first name

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 4](https://itdraft.ru/wp-content/uploads/2023/02/image-1024x684.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 4")](https://itdraft.ru/wp-content/uploads/2023/02/image.png)

```
LDAP Attribute: givenname
```

Сохраняем

Создаем клиента для dokuwiki: переходим в раздел Clients и создаем нового клиента (Create client)

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 5](https://itdraft.ru/wp-content/uploads/2023/02/doku-key-create-1-1-1024x480.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 5")](https://itdraft.ru/wp-content/uploads/2023/02/doku-key-create-1-1.png)

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 6](https://itdraft.ru/wp-content/uploads/2023/02/doku-key-create-2-1024x510.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 6")](https://itdraft.ru/wp-content/uploads/2023/02/doku-key-create-2.png)

```
General Settings
Client type: OpenID Connect
Client ID: dokuwiki #Понадобится для настройки плагина в dokuwiki

Capability config
Client authentication: On
Authentication flow: All
```

Сохраняем, переходим в настройки клиента dokuwiki

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 7](https://itdraft.ru/wp-content/uploads/2023/02/image-1-1024x724.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 7")](https://itdraft.ru/wp-content/uploads/2023/02/image-1.png)

[![Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 8](https://itdraft.ru/wp-content/uploads/2023/02/image-2-1024x706.png "Настройка oAuth авторизации через Keycloak+FreeIPA в DokuWiki 8")](https://itdraft.ru/wp-content/uploads/2023/02/image-2.png)

```
Вкладка Settings
Valid redirect URIs: https://dokuwiki.itdraft.ru/*

Вкладка Credentials
Client Authenticator: Client Id and Secret
Client secret: ... #Понадобится для настройки плагина в dokuwiki
```

Настройка Keycloak завершена