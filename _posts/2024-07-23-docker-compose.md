---
title: Docker-compose란?
date: 2024-07-23 02:54:00 +09:00
categories: [docker,docker-compose]
tags:
  [
    docker,
    docker-compose,
  ]
---
목차
- [docker-compose란?](#docker-compose란)
  - [docker-compose의 기본 개념](#docker-compose의-기본-개념)
  - [docker-compose의 주요 기능](#docker-compose의-주요-기능)
  - [docker-compose의 기본 명령어](#docker-compose의-기본-명령어)
  - [docker-compose.yml파일 작성 방법](#docker-composeyml파일-작성-방법)
    - [1. 기본 구조](#1-기본-구조)
    - [2. 서비스 정의](#2-서비스-정의)
  - [docker-compose 설치와 다중 컨테이너 관리/베포](#docker-compose-설치와-다중-컨테이너-관리베포)
  - [dockerfile과 docker-compose의 차이점](#dockerfile과-docker-compose의-차이점)


<br/>

들어가기에 앞서, 이 글은 Docker에 대한 기본 개념과 Dockerfile에 대한 이해를 바탕으로 작성되었습니다.   
만약  Dockerfile에 대한 기초 지식이 필요하다면, 먼저 [dockerfile 작성법 ](https://2swo.github.io/posts/docker/#docker-%EC%82%AC%EC%9A%A9%EB%B2%95) 이라는 게시물을 읽어보시길 권장합니다. 


## docker-compose란?

### docker-compose의 기본 개념

* **docker Compose**는 여러 개의 Docker 컨테이너를 정의하고 관리하기 위한 도구입니다.    
docker-compose를 사용하면 애플리케이션의 서비스를<span style="color:#ffd33d">  docker-compose.yml 파일</span>에 정의하고 단일 명령으로 이러한 서비스를 시작할 수 있습니다.


### docker-compose의 주요 기능

- **다중 컨테이너 애플리케이션 정의** : `docker-compose.yml` 파일을 통해 애플리케이션의 여러 서비스를 하나의 파일에 정의할 수 있습니다.

- **서비스 연결** : 서로 다른 컨테이너 간의 네트워크 연결을 쉽게 설정할 수 있습니다.

- **볼륨 관리** : 컨테이너의 데이터 볼륨을 정의하고 관리할 수 있습니다.

- **환경 변수 설정** : 환경 변수를 통해 애플리케이션의 설정을 관리할 수 있습니다.

- **확장성** : 여러 인스턴스를 쉽게 확장할 수 있습니다.

- **일관된 개발 환경** : 동일한 설정 파일을 사용하여 로컬 개발 환경과 프로덕션 환경을 일관되게 유지할 수 있습니다.

<br>

> 개인적인 생각으로, doocker-compose의 주요 기능은 위와 같이  많은 기능들이 있지만, 그중에서도 가장 좋다고 느낀 점은 확장성과, 볼륨 관리인 것 같습니다.


<br>

---

### docker-compose의 기본 명령어

```bash
# docker-compose.yml 파일에 정의된 서비스를 빌드하고 시작합니다.
docker-compose up

# 실행 중인 서비스를 중지하고 네트워크 및 볼륨을 삭제합니다.
docker-compose down

# 실행 중인 서비스의 로그를 출력합니다.
docker-compose logs

# 실행중인 컨테이너 내에서 명령을 실행합니다.
docker-compose exec [service] [command]
```

docker 명령어와 중복되는 것이 많습니다.  옵션을 사용하거나, 더 많은 명령어가 궁금하다면?<br/>
docker-compose 명령어 자세히 알아보기 : https://docs.docker.com/compose/compose-file/

---

### docker-compose.yml파일 작성 방법

`docker-compose.yml` 파일은  다중 컨테이너 애플리케이션을 정의하는 데 사용되는 YAML 형식의 설정 파일입니다.   


#### 1. 기본 구조

`docker-compose.yml` 파일의 기본 구조는 다음과 같습니다!

```yaml
version: '3'  # 사용한 docker-compose 파일의 버전

services:  # 서비스 정의
  service_name:  # 서비스 이름
    image: image_name  # 사용할 Docker 이미지
    build:  # 이미지를 빌드할 때 사용할 설정 (dockerfile내부 설정으로 대체가능!)
      dockerfile: Dockerfile  # 사용할 Dockerfile
    ports:  # 포트 매핑
      - "host_port:container_port"
    volumes:  # 볼륨 마운트
      - host_path:container_path
    environment:  # 환경 변수 설정
      - ENV_VAR_NAME=value
    networks:  # 네트워크 설정
      - network_name

networks:  # 네트워크 정의
  network_name:

volumes:  # 볼륨 정의
  volume_name:

```

#### 2. 서비스 정의

서비스는 컨테이너의 실행 단위를 정의합니다. 각 서비스는 컨테이너의 이미지, 빌드 설정, 포트 매핑, 볼륨, 환경 변수 등을 포함하고 있습니다.

각 서비스를 정의하게 되면, 애플리케이션의 각 구성 요소를 독립적으로 관리하고, 설정하며, 실행할 수 있습니다. 이는 애플리케이션의 배포, 확장 및 유지보수를 더욱 용이하게 합니다.

```yaml
services:
  code:
    image: mycode:latest  # 내 코드 이미지를 사용
    ports:
      - "3000:3000"  # 포트 설정
    env_file:
      - .env  # 환경 변수 설정

  redis:
    image: myredis:latest # 내 Redis 이미지를 사용
    volumes: 
      - redis_data:/usr/local/etc/redis # 볼륨 마운트
    env_file:
      - .env.redis # 환경 변수 설정
    ports:
      - "6379:6379" # 포트 설정

volumes:
  redis_data:

# code 서비스는 애플리케이션 코드를 실행하며, 포트 3000을 사용하고, 
#.env 파일을 통해 환경 변수를 설정합니다.

# redis 서비스는 Redis 데이터베이스를 실행하며, 포트 6379를 사용하고, 
#redis_data 볼륨을 /usr/local/etc/redis 경로에 마운트합니다. 또한, .env.redis 파일을 통해 환경 변수를 설정합니다.
```

<br>

>예시로, 위와 같이 code와 redis라는 각 서비스를 정의하게 되면, 각 구성 요소를 독립적으로 관리하고 
>설정하며, 실행할 수 있습니다. 이는 애플리케이션의 배포와 확장 및 유지보수를 더욱 용이하게 합니다.

<br>

### docker-compose 설치와 다중 컨테이너 관리/베포

1. docker-compose 설치 

> 먼저, `docker-compose`를 다운로드 해 보겠습니다. 제 설치 환경은 `ubuntu 22-04`입니다.
   
```bash
# Docker Compose 설치
$ sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Docker Compose 실행 권한 부여
$ sudo chmod +x /usr/local/bin/docker-compose

# 심볼릭 링크 연결
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 설치된 Docker Compose 버전 확인
$ docker-compose --version

```

2. ubuntu에 docker-compose.yml 작성하기 (또는 옮겨주기)

> 저는 `Cyberduck`이라는 프로그램을 이용하여 로컬에 있는 docker-compose.yml을 옮겨주었습니다.

```yml
version: '3'
services:
  ma6-main:
    image: 2swo/ma6-menbosha-back
    ports:
      - '3000:3000'
    env_file:
      - .env.development

  redis:
    image: 2swo/redis
    volumes:
      - redis_data:/usr/local/etc/redis
    command: sh -c "redis-server /usr/local/etc/redis/redis.conf"
    env_file:
      - .env.redis
    ports:
      - '6379:6379'

  api-nginx-certbot:
    image: 2swo/api-back-nginx-certbot
    ports:
      - '80:80'
      - '443:443'
    env_file:
      - .env.nginx-certbot

volumes:
  redis_data:

```


3. 작성한 docker-compose에 맞는 이미지 다운로드

> docker login을 하신 후, 내 dockerhub에 있는 이미지를 다운받아 주시면 됩니다.

<img width="" alt="스크린샷 2024-07-23 오후 9 14 10" src="https://github.com/user-attachments/assets/dd76e483-c740-4cec-ac15-a7f19a2ca255">

<br/>

4. docker-compose up -d 옵션으로 시작하기.

> -d 옵션을 붙일 경우, 서비스가 백그라운드에서 실행되며, 터미널을 계속해서 사용할 수 있습니다.

<img width="" alt="스크린샷 2024-07-23 오후 9 23 58" src="https://github.com/user-attachments/assets/15e06d8e-f747-488d-b88a-52858e3c859c">


<br/>

추가적으로, compose를 통해 실행된 이미지들 또한 `docker ps` 옵션을 사용해서 잘 실행되고 있는지 확인할 수 있습니다.


---

### dockerfile과 docker-compose의 차이점

dockerfile과 docker-compose는 둘 다 docker를 사용하여 애플리케이션을 베포하고 관리하는데 사용됩니다. 하지만 그 목적과 기능에는 살짝 차이가 있습니다.

**dockerfile**   
- 목적: Dockerfile은 <span style="color:#ffd33d"> 개별 docker 이미지를 빌드</span>하기 위해 사용됩니다.  

- 구조: Dockerfile은 일련의 명령어로 구성되며, 각 명령어는 이미지를 빌드하는 과정의 단계를 나타냅니다.


**docker-compose**   

- 목적:목적: Docker Compose는 <span style="color:#ffd33d"> 여러 Docker 컨테이너를 정의하고 실행</span> 하기 위해 사용됩니다.

- 구조: `docker-compose.yml` 파일을 사용하여 여러 컨테이너 서비스를 정의합니다.

<br>


>요약하자면, dockerfile은 개별 애플리케이션 또는 서비스의 docker images를 빌드하는데 사용됩니다.  
>docker-compose는 여러 docker 컨테이너를 정의하고 함께 조율하는데 사용됩니다.










---

<br>
<span style="color:orange"> 포스팅에 대한 피드백은 언제나 환영입니다! </span> <br/>
필요한 정보나 궁금하신 것을 남기시면 빠른 시일 내로 답변드리겠습니다.