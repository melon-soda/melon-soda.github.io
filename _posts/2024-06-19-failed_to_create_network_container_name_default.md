---
title: failed to create network <container_name>_default
date: 2024-06-19 18:45:00 +0900
categories: [Troubleshoot]
tags: [docker, docker-compose]
---

## Description

docker-compose를 사용하여 컨테이너를 생성하는 과정에서 다음 오류와 함께 컨테이너 생성에 실패하였다.
```bash
➜  mysql docker compose -f ./docker-compose.yml up
[+] Running 1/0
 ✘ Network mysql-test_default  Error
failed to create network mysql-test_default: Error response from daemon: Failed to Setup IP tables: Unable to enable SKIP DNAT rule:  (iptables failed: iptables --wait -t nat -I DOCKER -i br-a748cfc8a0b6 -j RETURN: iptables: No chain/target/match by that name.
 (exit status 1))
```

```yml
name: mysql-test

services:
  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: pass
    ports:
      - "3306:3306"
    expose:
      - "3306"
    volumes:
      - mysql:/var/lib/mysql
    networks:
      - bridge

volumes:
  mysql:
```

## Solution

docker 재시작
```bash
➜  mysql sudo systemctl daemon-reload
➜  mysql sudo systemctl restart docker
```

### Possible Solution

```bash
iptables -t filter -N DOCKER
```

### Failed Solution

These solutions has been tried and has failed.

## Reason

`DOCKER` 네트워크가 사라지는 것이 원인으로 추정.

## Referenced Links

- https://github.com/wodby/docker4drupal/issues/211
