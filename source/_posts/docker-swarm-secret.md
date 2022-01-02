---
title: Manage sensitive data with Docker secrets
date: 2022-01-02 15:23:51
tags:
    - docker
    - docker-swarm
---

[docker](https://docs.docker.com/engine/swarm/secrets/#example-rotate-a-secret)

### Tl;dr
- `Sensitive Data`はどう管理すればいいのか、良い方法を知りたい
    - Usernames and passwords
    - TLS certificates and keys
    - SSH keys
    - Other important data such as the name of a database or internal server
    - Generic strings or binary content (up to 500 kb in size)
- `Docker secret`を使って極秘データを一元に管理できる、利用のコンテナだけに安全で渡せる
- `Docker secrets` are only available to `swarm services`, not to standalone containers

---

Docker containerを立ち上げる時に秘密な情報などをコンテナにどう渡せばよいかについて調べましたのでメモです。
[`Docker Secret`](https://docs.docker.com/engine/swarm/secrets/#about-secrets)があります。

- Docker Secretを利用しないDocker compose:
```sh
  mongo:
    image: mongodb
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_DATABASE=mydb
      - MONGO_INITDB_ROOT_USERNAME=rootuser
      - MONGO_INITDB_ROOT_PASSWORD=rootpassword
    volumes:
      - mongo-db:/data/db
```
- Docker Secretを利用するDocker compose:
```
services:
  mongo:
    image: mongodb
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_DATABASE=mydb
      - MONGO_INITDB_ROOT_USERNAME_FILE=rootuser
      - MONGO_INITDB_ROOT_PASSWORD_FILE=/run/secrets/db_root_password
    volumes:
      - mongo-db:/data/db
secrets:
   db_root_password:
     file: db_password.txt
```
secretsで `db_root_password`の秘密データを定義されます。
サービス開始するとき、Dockerは`/run/secrets/<key-name>`にマウントします。