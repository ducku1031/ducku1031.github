---
layout: post
title: "Docker"
description: "Docker"
categories: [Linux]
tags: [Docker]
redirect_from:
  - /2020/08/19/
---

* Kramdown table of contents
{:toc .toc}


# Docker 
> Docker는 Linux 기반의 Container Runtime 오픈소스로 Virtual Machine과 상당히 유사한 기능을 가지면서 Virtual Machine보다 훨씬 가벼운 형태로 배포가 가능하다.   

<br>

## VM vs Docker

![3](https://user-images.githubusercontent.com/69279022/90702522-6ee31800-e2c6-11ea-99c5-3e8892e736ab.jpg)

<br>

Virtual Machine
> - 편하지만 성능이 좋지 못한 단점.   
> - CPU에 가상화를 위한 기능들이 많이 추가되었지만 아직도 Virtual Machine은 Real Machine에 비해 상대적으로 속도가 느리다.   

<br>

Docker
> - Guest OS를 설치하지 않는다.
> - Docker Image에 Server 운영을 위한 프로그램과 라이브러리만 격리해서 설치할 수 있다.
> - OS Resource(시스템 콜)은 Host OS와 공유한다.
> - Docker는 하드웨어를 가상화하는 계층이 없기 때문에 VM에 비해 월등히 빠르다.
> - Real Machine과 성능차이가 거의 나지 않는다. 

## Repository

![d1](https://user-images.githubusercontent.com/69279022/90853677-c90ed680-e3b5-11ea-89a9-d5ac81f5985a.png)

<br>

- Container Images를 중앙의 Repository에 저장하였다가 다른 환경에서 가져다가 사용할 수 있다. 즉, Git 또는 VCS(Version Control System)과 같은 개념으로 Application들을 Container로 Packaging하여 다른 환경으로 쉽게 옮길 수 있다   

<br>

## Docker 설치
~~~
$ sudo wget -qO- https://get.docker.com/ | sh       # 자동 설치 스크립트
$ sudo apt-get install docker.io                    # Ubuntu
$ sudo yum install docker-io                        # CentOS
~~~

## Docker에 권한 부여하기
> 일반 유저가 사용할 경우 권한을 부여하여 Sudo 명령을 사용하지 않고 사용할 수 있다.   

` 방법 1 `
~~~
$ sudo usermod -aG docker $USER
~~~
- 현재 유저에 docker Group을 보조그룹으로 추가   

` 방법 2 `
~~~
$ sudo setfacl -m user:$USER:rw /var/run/docker.sock
~~~
- docker.sock 파일에 ACL을 설정하여 권한을 추가

<br>

# Docker 명령어

## Docker Hub에서 Iamge 받기

` 명령어 형식 `
~~~
$ docker pull [ Images Name ]:[ Tag ]
~~~

` 예시 `
~~~
$ docker pull ubunut
~~~
- docker hub에서 이름이 ubuntu라는 Image의 최신 Version을 가져온다.   
- 뒤에 Tag 정보를 입력하지 않으면 자동으로 최신 Version(latest)를 받아온다.

<br>

## Docker Image List 확인하기

` 명령어 형식 `
~~~
$ docker images
~~~

<br>

## Docker Image를 사용하여 Container 생성, 실행

` 명령어 형식 `
~~~
$ docker run [ Option ] [ Images Name ] [ 실행할 파일 ]
~~~

` 명령어 예시 1 `
~~~
$ docker run -i -t --name Hello ubuntu /bin/bash
~~~
- ubuntu Image를 Container로 생성한 뒤 ubuntu Image 안의 /bin/bash를 실행한다.   
- i 옵션(interactive), t 옵션(Pseudo-tty) 를 사용하여 실행된 Bash Shell에서 입력 및 출력을 할 수 있게 한다.   
- --name 옵션을 사용하여 생성한 Container의 Name을 지정한다.

` 명령어 예시 2 `
~~~
$ docker run --name -d -p 8080:80 -v /root/data:/data nginx
~~~
- "--name test" : Container의 이름을 test로 설정   
- "-d" : background에서 해당 Container를 실행   
- "-p 8080:80": [ Host IP]:8080으로 접속하면 [ 해당 Container IP]:80으로 접속된다.   
- "-v /root/data:/data" :  Cotainer의 /data Directory를 Host의 /root/data Directorty로 Mount 한다.  ( /root/data Dircetory에 파일을 넣으면 Container에서 해당 파일을 읽을 수 있다. )   

<span style="color:red">TIP !!</span>

run 명령어 ERROR 발생시
~~~
unable to remount sys readonly: unable to mount sys as readonly max retries reached
~~~
vi /etc/syscofig/docker
~~~
$ vi /etc/syscofig/docker
other_args="--selinux-enabled --exec-driver=lxc"     # 추가로 작성
$ sudo systemctl restart docker
~~~

<br>

## Cotainer List 확인

` 명령어 형식 `
~~~
$ docker ps                   # 실행중인 Container List를 출력
$ docker ps -a                # 모든 Container List를 출력
~~~

<br>

## 정치(STOP) 상태인 Container를 Background로 시작

` 명령어 형식 `
~~~
$ docker run [ Container Name ] 
~~~

` 명령어 예시 `
~~~
$ docker run Hello
~~~
- 정지 상태인 이름이 Hello인 Container Background로 시작한다.

<br>

## Container 재시작하기 

` 명령어 형식 `
~~~
$ docker restart [ Container Name ]
~~~

` 명령어 예시 `
~~~
$ docker restart Hello
~~~
- 이름이 Hello인 Container를 재시작한다.

<br>

## 실행중인 Container 접속하기

` 명령어 형식 `
~~~
$ docker attach [ Container Name ]
~~~

` 명령어 예시 `
~~~
$ docker attach Hello
~~~
- 이름이 Hello인 Container로 접속한다.

<br>

## Container 외부에서 Container 내부에 명령 실행하기 

` 명령어 형식 `
~~~
$ docker exec [ Container Name ] [ Command ]
~~~

` 명령어 예시 `
~~~
$ docker exec Hello ls
~~~
- 이름이 Hello인 Container에서 ls 명령어의 실행 값을 출력해준다.

<br>

## 실행(START)중인 Container를 정지

` 명령어 형식 `
~~~
$ docker stop [ Container Name ]
~~~

` 명령어 예시 `
~~~
$ docker stop Hello
~~~

<br>

## docker Container 삭제  

` 명령어 형식 `
~~~
$ docker rm [ Cotainer Name ]
~~~

` 명령어 예시 `
~~~
$ docker rm Hello
~~~

<br>

## docker Image 삭제   

` 명령어 형식 `
~~~
$ docker rmi [ Images Name ]:{ Tag }
~~~

` 명령어 예시`
~~~
$ docker rmi ubuntu:latest
~~~


<br>

## Cotainer안의 파일을 Host에 복사

` 명령어 형식 `
~~~
$ docker cp [ Container Name ]:[ 컨테이너안 복사할 파일 경로 ] [ 호스트에 복사할 위치 ]
~~~

` 명령어 예시 `
~~~
$ docker cp Hello:/var/log ~
~~~
- Hello Container의 /var/log 파일을 Host의 홈디텍터리에 복사한다

<br>

## Modify가 발생한 Container를 Images로 생성하기 

` 명령어 형식 `
~~~
$ docker commit [ 옵션 ] [ 컨테이너 이름 ] [ 이미지 이름 ]:[태그]
~~~

` 명령어 예시 `
~~~
$ docker commit -a"test <test@co.kr>" -m "hello" Hello test-image:0.2
~~~
- "-a "test <test@co.kr> " : Commit을 한 사용자를 남김   
- "-m "hello"  : Hello라 Log Message를 남긴다.   
- "test-container" : commit하여 이미지로 생성할 컨테이너 지정한다.   
- "test2:0.2" : test2 라는 이름으로 Image를 생성한다 Tag는 0.2로 지정한다.   

<br>

## Container에서 변경된 파일 확인하기

` 명령어 형식 `
~~~
$ docker diif [ 컨테이너 이름 ]
~~~

` 명령어 예시`
~~~
$ docker diff Hello
A /data
C /run
A /run/nginx.pid
C /var/lib/nginx
A /var/lib/nginx/body
A /var/lib/nginx/fastcgi
A /var/lib/nginx/proxy
A /var/lib/nginx/scgi
A /var/lib/nginx/uwsgi
C /etc/nginx
A /etc/nginx/site-enabled
C /dev
C /dev/kmsg
~~~
- **A** : 추가된 파일을 의미한다.
- **C** : 변경된 파일을 의미한다.
- **D** : 삭제된 파일을 의미한다


<br>

## Container의 세부 정보를 확인

 ` 명령어 형식 `
 ~~~
 $ docker inspect [ 컨테이너 이름 ]
 ~~~

 ` 명령어 예시 `
~~~
$ docker inspect hello-nginx
[{
    "Args": [],
    "Config": {
        "AttachStderr": false,
        "AttachStdin": false,
        "AttachStdout": false,
        "Cmd": [
            "nginx"
        ],
        "CpuShares": 0,
        "Cpuset": "",
        "Domainname": "",
        "Entrypoint": null,
        "Env": [
            "HOME=/",
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        ],
        "ExposedPorts": {
            "443/tcp": {},
            "80/tcp": {}
        },
        "Hostname": "d4369f661b0a",
        "Image": "hello:0.1",
        ... 생략 ...
~~~

<br>


# Dockerfile 작성하기

1. Dockerfile을 생성한다   

~~~
$ vi Dockerfile
~~~

<br>

2. Dockerfile을 작성한다      

~~~
FROM ubuntu:14.04
MAINTAINER Foo Bar <foo@bar.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx

VOLUME ["/data", "/etc/nginx/site-enabled", "/var/log/nginx"]

WORKDIR /etc/nginx

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
~~~
- **FROM** : 어떠한 Images를 기반으로 할지 설정한다.   

~~~
FROM [ Image Name ]:[ Tag ]     # 형식
~~~
- **MAINTAINER** : 관리자의 정보를 입력한다.   
- **RUN**: Shell Script 또는 Command를 실행한다. (Images 생성 중에는 사용자의 입력을 받을 수 없음으로 -y 옵션을 사용한다. )
- **VOLUME** : Host와 공유할 Directory List이다. (docker run 명령어 실행시 -v 옵션으로 여기에 설정한 공유할 Directory를 지정할 수 있다. )  
- **CMD** : Container가 시작되었을 때 실행할  실행 파일 또는 쉘 스크립트   
- **WORDIR** : CMD에서 설정한 실행 파일이 실행될 Directory   
- **EXPOSE** : Host와 연결할 Port Number   

<br>

## Dockerfile으로 Docker Image를 생성

` 명령어 형식 `
~~~
$ docker build [ Option ] [ Dockerfile PATH ]
~~~

` 명령어 예시 `
~~~
$ docker build --tag test .
~~~
- **" . "** 현재 위치에 있는 Dockerfile을 사용하여 Image를 생성한다.    
- --tag 옵션을 사용하여 Images의 이름과 버전을 지정할 수 잇다. (미지정시 latest)   


<br>

# Docker Private Repository 구축

1. Docker 공식 저장소인 Docker Hub에서 Registry 가져오기   

~~~
$ docker pull registy
~~~

2. Registry Image 실행   

~~~
$ docker run -d -p 5000:5000 --name repo --restart=always registry
~~~
- registry의 기본 Port 5000번이다.   
- "--restart=always" : Bootung 시 자동으로 시작되게 설정    

<br>


## Private Repo에 넣을 Image를 가져온다.   

` 명령어 형식 `
~~~
$ docker pull [ 이미지 이름 ]
~~~
` 명령어 예시 `
~~~
$ docker pull Hello-world
~~~

## 가져온 Images의 정보를 변경하여 복사   

` 명령어 형식 `
~~~
$ docker tag [ 다운받은 이미지 이름] localhost:5000/[ 사용자 정의 이름]
~~~
` 명령어 예시 `
$ docker tag Hello-world localhost:5000/test
- "localhost:5000" : 위에서 다운받은 Registry가 Localhost에 위치하고 5000번 포트로 동작하기 때문에 이렇게 지정한다. (필수로 설정)   

` Image 확인 `
~~~
$docker images
REPOSITORY          TAG                 IMAGE ID            CREATED                  VIRTUAL SIZE
localhost:5000/test latest              bf756fb1ae65        7 Months ago              13.3KB
Hello-world         latest              bf756fb1ae65        7 Months ago              13.3KB
~~~

## Privaet Repo에 Image 업로드

` 명령어 형식 `
~~~
$ docker push [ 자신의 로컬 IP]:5000/[Images 이름]:[Tag]
~~~

` 명령어 예시 `
~~~
$ docker push 10.10.1.100:5000/test:latest
~~~
- Repo IP 10.10.1.100:5000으로 이름이 test인 Image의 최신버전을 Pull 한다.


## Privaet Repo에 Image 다운로드

` 명령어 형식 `
~~~
$ docker pull [ IP ]:5000/[Image 이름]:[Tag]
~~~
` 명령어 예시 `
~~~
$ docker pull 10.10.1.100:5000/test:latest
~~~
