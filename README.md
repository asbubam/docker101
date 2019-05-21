# 2019.0522 Docker101

# Docker for Mac 다운로드 및 설치
```bash
$ wget https://download.docker.com/mac/stable/Docker.dmg
```

# docker 설치 확인
```bash
$ docker -v
Docker version 18.09.2, build 6247962

# 실행 중인 컨테이너 리스트 출력
$ docker ps -a
```

# Jenkins 컨테이너 실행하기
```bash
$ docker run -p 8083:8080 jenkins/jenkins:latest
$ open http://localhost:8083
```

# Jenkins home volume 마운트해서 컨테이너 실행하기
```bash
$ docker run -d -p 8083:8080 -v ~/jenkins:/var/jenkins_home jenkins/jenkins:latest
$ open http://localhost:8083
```

# Jenkins 컨테이너 조회
> container id는 전체를 입력하지 않아도 된다.
> ex) $ docker stop e78

```bash
# 실행 중인 도커 컨테이너를 조회 $ docker ps -a 
# 컨테이너 로그 조회 $ docker logs (-f) {container id} 
 # 특정 컨테이너를 stop
$ docker stop {container id}

# stop된 컨테이너를 다시 시작 $ docker start {container id}

# 특정 컨테이너를 삭제
$ docker rm {container id}
```

# 로컬에 pull 받은 이미지 조회
```bash
$ docker images
```

# MySQL 컨테이너 실행
```bash
# MySQL 5.7 버전 컨테이너를 mysql 이란 이름으로 3306 포트를 포팅해서 -d 옵션으로 실행 
$ docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --name mysql \
  mysql:5.7

# 호스트에서 컨테이너 내부에 명령 실행
$ docker exec mysql cat /etc/issue

# 호스트에서 컨테이너 내부에 명령 실행 -it (interactive tty)
$ docker exec -it mysql /bin/bash
$ mysql -h localhost -u root
$ show databases;
$ \q
$ exit

# 더이상 로컬에 MySQL client를 설치하지 않아도 된다. (버전별로 실행도 가능!!!)
$ docker exec -it mysql mysql -h localhost -uroot

# 동일한 방법으로 MacOS에서 Docker를 사용해서 Linux 커널/라이브러리를 사용해 빌드할 수도 있다.
```

# docker-compose로 워드프레스 띄우기
```bash
$ vi docker-compose.yml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data: {}

$ docker-compose up -d
$ open http://localhost:8000
$ docker-compose down
```

# Nginx docker image 만들기
```bash
$ vi Dockerfile
FROM alpine:3.1

RUN apk add --update nginx && mkdir /tmp/nginx && rm -rf /var/cache/apk/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

$ docker build -t asbubam/my_nginx:0.1 .
$ docker run -d -p 80:80 --name nginx asbubam/my_nginx:0.1 $ open http://localhost:80

# dockerhub 내 계정 public repo에 push 한다면...
$ docker push asbubam/my_nginx:0.1
```

# docker image 조회 삭제
```bash
$ docker images
$ docker rmi
```
