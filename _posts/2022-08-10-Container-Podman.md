---
title: "Container/Podman 기술정리"
last_modified_at: 2022-08-10T17:30:00-00:00
categories:
  - Container
tags:
  - Podman
  - Virtualization
  - CL180
---

# 개요
이번에는 컨테이너의 동작원리와 예시에 대해 정리를 해보고자 한다.  

# 컨테이너
## 인프라 비교
**가상화 vs 컨테이너**  
![가상화 vs 컨테이너](https://www.redhat.com/cms/managed-files/virtualization-vs-containers.png)  

## 사용되는 기술
컨테이너를 실행에 사용되는 Linux 기능
- Namespace
  - 네임스페이스는 특정 시스템 리소스를 격리한다.
  - 격리대상으로는 네트워크, 프로세스 ID, 마운트포인트, IPC리소스, 시스템 정보, 시스템 리소스를 포함한다.
- cGroups
  - 컨트롤 그룹은 시스템 리소스를 관리하고 제한한다.
- Seccomp
  - 프로세스에서 시스템 호출을 제한한다.
  - 프로세스에 대한 보안 프로필을 정의한다.
- SELinux
  - Linux 커널에서 사용되는 프로세스에 대한 액세스 제어 시스템이다.


## 기술 종류
- lxc
- Docker
- Podman
  - Docker과 호환성이 있고 아키텍쳐를 리패킹하여 보안적 결함과 문제점을 개선했다.

## 컨테이너 런타임
"[여기](https://www.samsungsds.com/kr/insights/docker.html)" 참조

# 실습정리
## 컨테이너 생성
```
[user@host ~]$ podman run registry.redhat.io/rhel8/httpd-24
Trying to pull registry.redhat.io/rhel8/httpd-24...
Getting image source signatures
Copying blob sha256:23113...b0be82
72.21 MB / 72.21 MB [======================================================] 7s
...output omitted...AH00094: Command line: 'httpd -D FOREGROUND'
```

```
[user@host ~]$ podman ps
CONTAINER ID    IMAGE                 COMMAND                ... NAMES
47c9aad60491
   registry.redhat.io/rhel8/httpd-24  "/usr/bin/run-http..."  ... focused_fermat2
```

**컨테이너 이름지정해서 실행하기**  
```
[user@host ~]$ podman run --name my-httpd-container \
> registry.redhat.io/rhel8/httpd-24
...output omitted...AH00094: Command line: 'httpd -D FOREGROUND'
```

**대화형 쉘/프로세스로 실행**  
```
[user@host ~]$ podman run -it registry.redhat.io/rhel8/httpd-24 /bin/bash
 bash-4.4#
```

기존에 실행중인 컨테이너는 podman run을 podman exec로 바꾸면된다.  

## 컨테이너 관리
**실행중인 컨테이너**  
```
[user@host ~]$ podman ps
CONTAINER ID   IMAGE         COMMAND      CREATED  STATUS   PORTS    NAMES
77d4b7b8ed1f  registry.redhat.io/rhel8/httpd-24  "/usr/bin/run-http..."  ...ago  Up...   my-htt...
```

**실행중지된 컨테이너 포함**  
```
[user@host ~]$ podman ps -a
CONTAINER ID  IMAGE        COMMAND     CREATED  STATUS        PORTS  NAMES
4829d82fbbff  registry.redhat.io/rhel8/httpd-24  "/usr/bin/run-http..."  ...ago   Exited (0)...        my-httpd...
```

**컨테이너 실행중지**  
```
[user@host ~]$ podman stop my-httpd-container
77d4b7b8ed1fd57449163bcb0b78d205e70d2314273263ab941c0c371ad56412
```

**컨테이너 재시작**  
```
[user@host ~]$ podman restart my-httpd-container
77d4b7b8ed1fd57449163bcb0b78d205e70d2314273263ab941c0c371ad56412
```

**컨테이너 삭제**  
```
[user@host ~]$ podman rm my-httpd-container
77d4b7b8ed1fd57449163bcb0b78d205e70d2314273263ab941c0c371ad56412
```

**모든 컨테이너 삭제/종료**
```
[user@host ~]$ podman stop -a
5fd8e98ec7eab567eabe84943fe82e99fdfc91d12c65d99ec760d5a55b8470d6
716fd687f65b0957edac73b84b3253760e915166d3bc620c4aec8e5f4eadfe8e
86162c906b44f4cb63ba2e3386554030dcb6abedbcee9e9fcad60aa9f8b2d5d4
```  

```
[user@host ~]$ podman rm -a
5fd8e98ec7eab567eabe84943fe82e99fdfc91d12c65d99ec760d5a55b8470d6
716fd687f65b0957edac73b84b3253760e915166d3bc620c4aec8e5f4eadfe8e
86162c906b44f4cb63ba2e3386554030dcb6abedbcee9e9fcad60aa9f8b2d5d4
```

**파일복사**  
```
[student@workstation ~]$ podman cp \
> /home/filepath container-name:/dest
```

**스토리지 연결**  
1.디렉토리 생성


```
[student@workstation ~]$ mkdir -p /home/student/local/vol
```

2.디렉토리 권한변경

```
[student@workstation ~]$ podman unshare chown 27:27 /home/student/local/vol
```

chown 명령어로도 설정 가능하다.  
컨테이너에 볼륨 연결후 볼륨 디렉토리에 ls -la 했을때 uid:gid가 나오는데  
그것과 맞춰주면 된다.  

```
[student@workstation ~]$ ls -la /home/student/local/vol
drwxr-x---. 2 100026 100026 6 Apr  8 07:31 /home/student/local/vol/item... <- uid:gid 에 주목
```


3.SELinux 컨테이너 정책 추가

**영구 등록시**  
```
[student@workstation ~]$ sudo semanage fcontext -a \
> -t container_file_t '/home/student/local/vol(/.*)?'
```

semanage fcontext -l 로 등록된 정책 확인 가능  

**일시 등록시**  
```
[student@workstation ~]$ sudo chcon -t container_file_t /home/student/local/vol
```

4.SELinux 정책 적용

```
[student@workstation ~]$ sudo restorecon -R /home/student/local/vol
```

5.정책 확인

```
[student@workstation ~]$ ls -ldZ /home/student/local/vol
drwxrwxr-x. 2 student student unconfined_u:object_r:container_file_t:s0 6 May 26 14:33 /home/student/local/vol
```

6.볼륨 연결

```
[student@workstation ~]$ podman run ... -v /home/student/local/vol:/myvol
6e0ef134315b510042ca757faf869f2ba19df27790c601f95ec2fd9d3c44b95d
```

**네트워크 포트 매핑**
```
[user@host ~]$ podman run -d --name apache1 -p 8080:8080 \
> registry.redhat.io/rhel8/httpd-24
```

**환경변수지정**
```
[student@workstation ~]$ podman run --name mysql-2 \
...
> -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 \
> -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 \
> registry.redhat.io/rhel8/mysql-80:1
281c0e2790e54cd5a0b8e2a8cb6e3969981b85cde8ac611bf7ea98ff78bdffbb
```

## 레지스트리 관리
**공개 레지스트리**
- Quay.io
- RedHat Container Catalog

**프라이빗 레지스트리**
```
vi /etc/containers/registries.conf
...
[registries.search]
registries = ["registry.access.redhat.com", "quay.io"] <-공개 레지스트리 설정

[registries.insecure]
registries = ['localhost:5000'] <-프라이빗 레지스트리 설정
```

## 이미지 관리
**이미지 가져오기**  
```
[user@host ~]$ podman pull quay.io/bitnami/nginx
```

**이미지 게시하기**  
```
[user@host ~]$ podman push quay.io/bitnami/nginx
```

**이미지 저장하기**  
```
[user@host ~]$ podman save \
> -o mysql.tar registry.redhat.io/rhel8/mysql-80
```

**이미지 로드하기**  
```
[user@host ~]$ podman load -i mysql.tar
```

**이미지 삭제하기**  
```
[user@host ~]$ podman rmi registry.redhat.io/rhel8/mysql-80
```

**이미지 변경비교**  
```
[user@host ~]$ podman diff mysql-basic
C /run
C /run/mysqld
A /run/mysqld/mysqld.pid
A /run/mysqld/mysqld.sock
A /run/mysqld/mysqld.sock.lock
A /run/secrets
```

**이미지 커밋하기**  
```
[user@host ~]$ podman commit mysql-basic  mysql-custom
```

커밋은 이미지를 제작하며 쌓아온 레이어를 하나로 합친다.  

**이미지 태그지정**   
```
[user@host ~]$ podman tag mysql-custom devops/mysql
```

태깅은 현재까지의 작업내용을 지정한 태그로 레이어 쌓는다고 보면된다.  

## 컨테이너 이미지 설계
**예시**  
```
# This is a comment line 1
FROM ubi8/ubi:8.5 2
LABEL description="This is a custom httpd container image" 3
MAINTAINER John Doe <jdoe@xyz.com> 4
RUN yum install -y httpd 5
EXPOSE 80 6
ENV LogLevel "info" 7
ADD http://someserver.com/filename.pdf /var/www/html 8
COPY ./src/ /var/www/html/ 9
USER apache 10
ENTRYPOINT ["/usr/sbin/httpd"] 11
CMD ["-D", "FOREGROUND"] 
```
- FROM ... 기본 이미지 지정
- LABEL ... 일반 메타데이터 지정
- MAINTAINER ... Author 메타데이터 지정
- RUN ... 이미지 제작시 실행할 명령어, RUN 명령어 여러줄로 나누면 명령어 별 레이어 계층화가된다.
- EXPOSE ... 네트워크 포트 노출
- ENV ... 환경변수
- ADD ... 파일 복사 지정
- COPY ... 파일 복사 지정, 폴더지정지 폴더 복사가 아닌 하위 파일을 재귀적으로 가져옴, bzip2,gzip2 압축파일을 지정 위치에 압축해제 상태로 가져옴
- USER ... 실행 유저권한
- ENTRYPOINT ... 컨테이너 실행시 기본값으로 실행되는 스크립트 혹은 프로그램 지정, 디렉토리 지정도 가능
- CMD ... ENTRYPOINT에서 실행하는 스크립트, 프로그램의 기본 옵션. podman exec -it ... <명령어> << 명령어 지정이 없을때의 기본값.

**이미지 빌드**  
```
[student@workstation dockerfile-create]$ podman build --layers=false \
> -t do180/apache .
STEP 1: FROM ubi8/ubi:8.5
Getting image source signatures 1
Copying blob sha256:...output omitted...
71.46 MB / 71.46 MB [=====================================================] 18s
...output omitted...
Storing signatures
STEP 2: MAINTAINER Your Name <youremail>
STEP 3: LABEL description="A custom Apache container based on UBI 8"
STEP 4: RUN yum install -y httpd &&     yum clean all
Loaded plugins: ovl, product-id, search-disabled-repos, subscription-manager
...output omitted...
STEP 5: RUN echo "Hello from Containerfile" > /var/www/html/index.html
STEP 6: EXPOSE 80
STEP 7: CMD ["httpd", "-D", "FOREGROUND"]
STEP 8: COMMIT ...output omitted... localhost/do180/apache:latest
Getting image source signatures
...output omitted...
Storing signatures
b49375fa8ee1e549dc1b72742532f01c13e0ad5b4a82bb088e5befbe59377bcf
```
