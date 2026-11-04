# Linux · Docker · Git/GitHub 실습

Linux 기본 명령어부터 Docker 컨테이너 운영, 웹 서버 실행, 데이터 저장, Git/GitHub 활용까지 실습한 내용을 기록한 문서입니다.

## 1. 프로젝트 개요

### 학습 목표

- Linux 터미널의 기본 명령어와 파일 권한 이해
- Docker 설치 및 정상 작동 여부 확인
- Docker 이미지와 컨테이너의 생성·실행·관리
- Dockerfile을 이용한 웹 서버 이미지 빌드
- 포트 매핑, 바인드 마운트 및 데이터 영속성 확인
- Git 설정 및 GitHub 저장소 업로드

### 실행 환경

| 구분 | 환경 및 버전 |
| --- | --- |
| OS | Windows 11 |
| Linux 환경 | WSL (Windows Subsystem for Linux) |
| Docker | 29.9.2 |
| Git | 2.53.0 |

### 수행 항목 체크리스트

- [x] 터미널 기본 조작 (`pwd`, `cd`, `mkdir`, `touch`, `cat`, `rm`, `mv`)
- [x] 권한 변경 실습
- [x] Docker 설치 및 점검, `hello-world` 실행
- [x] Docker 컨테이너 진입 및 명령 수행
- [x] 컨테이너 종료·유지 차이 확인 (`attach` vs `exec`)
- [x] Dockerfile 작성 및 이미지 빌드·실행
- [x] 포트 매핑 및 웹 서버 접속
- [x] 바인드 마운트
- [x] Docker 데이터 영속성 검증
- [x] Git 설정 및 GitHub 업로드

---

## 2. Docker 설치 및 기본 점검

### 2.1 Docker 버전 및 실행 환경 확인

`docker --version`으로 설치된 Docker의 버전을 확인하고, `docker info`로 클라이언트와 서버의 설정 및 실행 환경을 점검했습니다.

**실행 기록**

```text
$ docker --version
Docker version 29.9.2, build a82e528

$ docker info
Client:
 Version:    29.9.2
 Context:    default
 Debug Mode: false
 Plugins:
  agent: Docker AI Agent Runner (Docker Inc.)
    Version:  v1.152.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-agent
  ai: Docker AI Agent - Ask Gordon (Docker Inc.)
    Version:  v1.33.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-ai
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.38.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.6.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-compose
  debug: Get a shell into any image or container (Docker Inc.)
    Version:  0.49.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-debug
  desktop: Docker Desktop commands (Docker Inc.)
    Version:  4.97.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-desktop
  dhi: CLI for managing Docker Hardened Images (Docker Inc.)
    Version:  v0.0.9
    Path:     /usr/local/lib/docker/cli-plugins/docker-dhi
  extension: Manages Docker extensions (Docker Inc.)
    Version:  v0.2.32
    Path:     /usr/local/lib/docker/cli-plugins/docker-extension
  init: Creates Docker-related starter files for your project (Docker Inc.)
    Version:  v1.4.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-init
  mcp: Docker MCP Plugin (Docker Inc.)
    Version:  v0.44.2
    Path:     /usr/local/lib/docker/cli-plugins/docker-mcp
  model: Docker Model Runner (Docker Inc.)
    Version:  v1.2.9
    Path:     /usr/local/lib/docker/cli-plugins/docker-model
  offload: Docker Offload (Docker Inc.)
    Version:  v0.7.21
    Path:     /usr/local/lib/docker/cli-plugins/docker-offload
  pass: Docker Pass Secrets Manager Plugin (beta) (Docker Inc.)
    Version:  v0.3.2
    Path:     /usr/local/lib/docker/cli-plugins/docker-pass
  sandbox: "docker sandbox" is deprecated, use Docker Sandboxes instead (Docker Inc.)
    Version:  v0.13.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-sandbox
  scout: Docker Scout (Docker Inc.)
    Version:  v1.27.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-scout

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 3
 Server Version: 29.9.2
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: local
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Discovered Devices:
  cdi: docker.com/gpu=webgpu
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 nvidia runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: b7a25261e029d68978bbf290df0a62fc819b3c51
 runc version: v1.5.2-0-g29dd3dc2
 init version: de40ad0
 Security Options:
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 24
 Total Memory: 15.25GiB
 Name: docker-desktop
 ID: 95d38f69-9981-4d2e-b054-b4193b4c2482
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 HTTP Proxy: http.docker.internal:3128
 HTTPS Proxy: http.docker.internal:3128
 No Proxy: hubproxy.docker.internal
 Labels:
  com.docker.desktop.address=unix:///var/run/docker-cli.sock
 Experimental: false
 Insecure Registries:
  hubproxy.docker.internal:5555
  127.0.0.0/8
  ::1/128
 Live Restore Enabled: false
 Firewall Backend: iptables
```

**확인 결과:** Docker 클라이언트와 서버 모두 `29.9.2`로 표시되었으며, Docker Desktop의 Linux 컨테이너 환경이 확인되었습니다.

### 2.2 Docker 기본 운영 명령어

Nginx 이미지를 사용해 컨테이너를 실행하고, 이미지 목록·실행 상태·자원 사용량·로그를 확인했습니다.

| 명령어 | 확인할 내용 |
| --- | --- |
| `docker images` | 로컬에 저장된 이미지 목록 |
| `docker run` | 이미지를 이용해 컨테이너 생성 및 실행 |
| `docker ps` | 실행 중인 컨테이너 목록 |
| `docker stats` | CPU 및 메모리 등 자원 사용 현황 |
| `docker logs` | 컨테이너의 실행 로그 |

**실행 기록**

```text
$ docker images                                                                                                          i Info →   U  In Use
IMAGE                          ID             DISK USAGE   CONTENT SIZE   EXTRA
nginx:stable-alpine3.24-perl   2cf02ded67dc        151MB         38.2MB    U

$ docker run --name server -d -p 80:80 nginx:stable-alpine3.24-perl

$ docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED         STATUS         PORTS                                 NAMES
4022b4225e68   nginx:stable-alpine3.24-perl   "/docker-entrypoint.…"   9 minutes ago   Up 9 minutes   0.0.0.0:80->80/tcp, [::]:80->80/tcp   server

$ docker stats
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O         PIDS
4022b4225e68   server    0.00%     23.11MiB / 15.25GiB   0.15%     1.7kB / 126B   10.6MB / 8.19kB   25

$ docker logs 402
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/11/03 00:08:04 [notice] 1#1: using the "epoll" event method
2026/11/03 00:08:04 [notice] 1#1: nginx/1.30.6
2026/11/03 00:08:04 [notice] 1#1: built by gcc 15.2.0 (Alpine 15.2.0)
2026/11/03 00:08:04 [notice] 1#1: OS: Linux 6.18.54.1-microsoft-standard-WSL2
2026/11/03 00:08:04 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/11/03 00:08:04 [notice] 1#1: start worker processes
2026/11/03 00:08:04 [notice] 1#1: start worker process 29
2026/11/03 00:08:04 [notice] 1#1: start worker process 30
2026/11/03 00:08:04 [notice] 1#1: start worker process 31
2026/11/03 00:08:04 [notice] 1#1: start worker process 32
2026/11/03 00:08:04 [notice] 1#1: start worker process 33
2026/11/03 00:08:04 [notice] 1#1: start worker process 34
2026/11/03 00:08:04 [notice] 1#1: start worker process 35
2026/11/03 00:08:04 [notice] 1#1: start worker process 36
2026/11/03 00:08:04 [notice] 1#1: start worker process 37
2026/11/03 00:08:04 [notice] 1#1: start worker process 38
2026/11/03 00:08:04 [notice] 1#1: start worker process 39
2026/11/03 00:08:04 [notice] 1#1: start worker process 40
2026/11/03 00:08:04 [notice] 1#1: start worker process 41
2026/11/03 00:08:04 [notice] 1#1: start worker process 42
2026/11/03 00:08:04 [notice] 1#1: start worker process 43
2026/11/03 00:08:04 [notice] 1#1: start worker process 44
2026/11/03 00:08:04 [notice] 1#1: start worker process 45
2026/11/03 00:08:04 [notice] 1#1: start worker process 46
2026/11/03 00:08:04 [notice] 1#1: start worker process 47
2026/11/03 00:08:04 [notice] 1#1: start worker process 48
2026/11/03 00:08:04 [notice] 1#1: start worker process 49
2026/11/03 00:08:04 [notice] 1#1: start worker process 50
2026/11/03 00:08:04 [notice] 1#1: start worker process 51
2026/11/03 00:08:04 [notice] 1#1: start worker process 52
172.17.0.1 - - [03/Nov/2026:00:11:08 +0000] "GET / HTTP/1.1" 200 896 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/156.0.0.0 Safari/537.36" "-"
172.17.0.1 - - [03/Nov/2026:00:11:08 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/156.0.0.0 Safari/537.36" "-"
2026/11/03 00:11:08 [error] 30#30: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost", referrer: "http://localhost/"
```

**확인 결과:** `server` 컨테이너가 실행 중임을 확인했고, 로그에서 `/` 요청에 대한 HTTP `200` 응답을 확인했습니다.

### 2.3 `hello-world` 컨테이너 실행

Docker가 이미지를 다운로드하고 컨테이너를 실행할 수 있는지 확인하기 위해 공식 `hello-world` 이미지를 실행했습니다.

**실행 기록**

```text
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete
d5e71e642bf5: Download complete
Digest: sha256:c567b231f65614688711a75f31518a064258a51d442117a3e771a3a8fdba78ef
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

**확인 결과:** `Hello from Docker!` 메시지가 출력되어 Docker 클라이언트와 데몬의 통신, 이미지 다운로드 및 컨테이너 실행이 정상적으로 진행되었음을 확인했습니다.

---

## 3. Linux 명령어 및 컨테이너 관리

### 3.1 Ubuntu 컨테이너 접속과 기본 명령어

Ubuntu 컨테이너 안에서 현재 위치 확인(`pwd`), 파일 목록 조회(`ls`), 경로 이동(`cd`), 문자열 출력(`echo`)을 실습했습니다. 이후 컨테이너를 재시작하고 `docker exec`로 다시 접속하여 셸을 종료했을 때 컨테이너가 유지되는지 확인했습니다.

**실행 기록**

```text
 $ docker run -it ubuntu bash

 root@27dec3d095c9:/# pwd
 /
 root@27dec3d095c9:/# ls
 bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
 root@27dec3d095c9:/# cd ~
 root@27dec3d095c9:~# pwd
 /root 
 root@27dec3d095c9:~# echo "hello"
 hello
 root@27dec3d095c9:~# exit
 exit

codyssey@GNIA-C401-C01:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

codyssey@GNIA-C401-C01:~$ docker ps -a
CONTAINER ID   IMAGE                          COMMAND                  CREATED          STATUS                      PORTS     NAMES
27dec3d095c9   ubuntu                         "bash"                   5 minutes ago    Exited (0) 5 seconds ago              cool_franklin
66daef906692   hello-world                    "/hello"                 6 minutes ago    Exited (0) 6 minutes ago              musing_mirzakhani
4022b4225e68   nginx:stable-alpine3.24-perl   "/docker-entrypoint.…"   32 minutes ago   Exited (0) 18 minutes ago             server

codyssey@GNIA-C401-C01:~$ docker start 27d
27d

codyssey@GNIA-C401-C01:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS         PORTS     NAMES
27dec3d095c9   ubuntu    "bash"    6 minutes ago   Up 2 seconds             cool_franklin

codyssey@GNIA-C401-C01:~$ docker exec -it 27d bash
root@27dec3d095c9:/#

root@27dec3d095c9:/# exit
exit

codyssey@GNIA-C401-C01:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS              PORTS     NAMES
27dec3d095c9   ubuntu    "bash"    7 minutes ago   Up About a minute             cool_franklin
```

### 3.2 `docker attach`와 `docker exec` 차이

컨테이너에 접속하는 두 명령어는 **접속 방식과 셸 종료 시 결과**가 다릅니다.

| 구분 | `docker attach` | `docker exec` |
| --- | --- | --- |
| 접속 대상 | 컨테이너의 메인 프로세스(PID 1) | 컨테이너 내부에 새로운 프로세스 생성 |
| `exit` 입력 결과 | 메인 프로세스가 종료되면 컨테이너도 종료 | 실행한 셸만 종료되며 메인 프로세스는 유지 |
| 주된 용도 | 실행 중인 메인 프로세스 입출력 연결 | 실행 중인 컨테이너 내부 점검·명령 실행 |

**확인 결과:** `docker exec -it 27d bash`로 접속한 뒤 `exit`를 입력해도 Ubuntu 컨테이너가 실행 중(`Up`)인 상태로 유지되었습니다.

### 3.3 파일 권한 변경 (`chmod`)

Ubuntu 컨테이너에서 `test.txt` 파일을 생성하고 `chmod 700`을 적용한 전후의 파일 권한을 비교했습니다.

**실행 기록**

```text
root@27dec3d095c9:~# touch test.txt

root@27dec3d095c9:~# ls
test.txt

root@27dec3d095c9:~# ls -l test.txt
-rw-r--r-- 1 root root 0 Nov  3 00:46 test.txt

root@27dec3d095c9:~# chmod 700 test.txt

root@27dec3d095c9:~# ls -l test.txt
-rwx------ 1 root root 0 Nov  3 00:46 test.txt
```

파일 권한은 읽기(`r`), 쓰기(`w`), 실행(`x`)을 숫자로 합산하여 표현합니다.

| 숫자 | 권한 |
| --- | --- |
| 4 | `r` (읽기) |
| 2 | `w` (쓰기) |
| 1 | `x` (실행) |

- `644`: 소유자 읽기·쓰기, 그룹과 기타 사용자 읽기
- `755`: 소유자 모든 권한, 그룹과 기타 사용자 읽기·실행
- `700`: 소유자만 모든 권한, 그룹과 기타 사용자 접근 권한 없음

**확인 결과:** 권한이 `-rw-r--r--`에서 `-rwx------`로 바뀌어, 소유자만 읽기·쓰기·실행 권한을 갖게 되었습니다.

---

## 4. Dockerfile을 이용한 웹 서버 실행

### 4.1 Dockerfile 작성 및 이미지 빌드

Nginx Alpine 기반 이미지를 사용하고 프로젝트의 `app/` 디렉터리에 있는 웹 파일을 컨테이너의 Nginx 기본 문서 경로로 복사하도록 Dockerfile을 작성했습니다.

**Dockerfile 원문**

```text
FROM nginx:stable-alpine3.24-perl
COPY app/ /usr/share/nginx/html
```

**빌드 및 실행 기록**

```text
$ docker build -t web-server .

codyssey@GNIA-C401-C01:~$ docker run -d -p 80:80 --name my-server web-server
```

`docker build`로 `web-server` 이미지를 생성하고 이를 기반으로 `my-server` 컨테이너를 백그라운드에서 실행하도록 명령했습니다.

### 4.2 포트 매핑

컨테이너의 웹 서버에 호스트 컴퓨터에서 접근하기 위해 **호스트 80번 포트와 컨테이너 80번 포트**를 연결했습니다.

**실행 기록**

```text
$ docker run --name server -d -p 80:80 nginx
```

`-p 80:80`은 `호스트 포트:컨테이너 포트` 형식이며, 해당 컨테이너가 정상 실행 중일 때 호스트에서 `http://localhost`로 접근할 수 있습니다.

---

## 5. 컨테이너와 데이터 저장

### 5.1 바인드 마운트 (Bind Mount)

**바인드 마운트**는 호스트의 특정 파일 또는 디렉터리를 컨테이너 내부 경로와 직접 연결하는 방식입니다.

이번 실습에서는 호스트의 `/home/codyssey/bind/html`과 컨테이너의 `/usr/share/nginx/html`을 연결한 뒤, 호스트의 `index.html` 파일을 수정하여 연결 여부를 확인하고자 했습니다.

**실행 기록**

```text
codyssey@GNIA-C401-C01:~$ docker run --name bind-nginx -d -p 80:80 -v /home/codyssey/bind/html:/usr/share/nginx/html
-d nginx

codyssey@GNIA-C401-C01:~$ vi bind/html/index.html
```

- `/home/codyssey/bind/html`: 호스트 디렉터리
- `/usr/share/nginx/html`: 컨테이너 내부 디렉터리

**확인 사항:** 호스트에서 `index.html`을 변경했을 때 웹 서버가 변경된 파일을 제공하는지 확인하는 실습입니다.

### 5.2 볼륨 영속성

docker가 관리하는 볼륨을 생성한 다음 컨테이너를 제거하고 새로운 컨테이너와 기존 볼륨을 연결하여 파일을 읽을 수 있는지 확인했습니다.

**실행 기록**

```text
codyssey@GNIA-C401-C01:~$ docker volume create vol-test
vol-test

codyssey@GNIA-C401-C01:~$ docker volume ls
DRIVER    VOLUME NAME
local     vol-test

codyssey@GNIA-C401-C01:~$ docker run --name vol-ubuntu -it -v vol-test:/home/data ubuntu bash

root@c38e53ef4641:/home/data# echo "hello" > test.txt

root@c38e53ef4641:/home/data# cat test.txt
hello

codyssey@GNIA-C401-C01:~$ docker rm vol-ubuntu
vol-ubuntu

codyssey@GNIA-C401-C01:~$ docker run --name vol-ubuntu2 -it -v vol-test:/home/data ubuntu bash

root@8947dc83ad57:/# cd home/data/

root@8947dc83ad57:/home/data# ls
test.txt

root@8947dc83ad57:/home/data# cat test.txt
hello
```

**확인 결과:** 새로운 컨테이너에서도 `test.txt`에서 `hello`가 출력되어 데이터가 유지됨을 확인했습니다.



---

## 6. 실습 결과 화면

실행 결과를 확인할 수 있는 스크린샷 입니다.   
→ [Screenshot](https://app.notion.com/p/E1-1-40d34c21c2668095a42dec1e5bfba238?source=copy_link)

## 7. 트러블 슈팅
### 7-1) -v ./data:/home/data에서 docker volume create
바인드 마운트를 사용한 영속성 검증과 docker가 관리하는 named volmue을 구분하지 못하고 볼륨 영속성에서 호스트 경로를 지정했습니다.
체크리스트를 확인하며 문제 상황을 인식하고 vol-test라는 볼륨을 생성하고 영속성 검증 진행했습니다.

### 7-2) port 22 connection timed out
개인 노트북으로 작업 내용을 git clone 진행했을 때 WSL Ubuntu에서 `git clone` 실행 시 SSH(22번 포트)와 HTTPS(443번 포트) 연결이 모두 타임아웃되었습니다.
```jsx
wsl --shutdown
```

wsl 재시작 이후에도 같은 문제 발생하였고 ssh(22) https(443) 포트가 모두 `Connection timed out`으로 실패했으므로 공통 네트워크 경로의 문제라고 생각하게 되었습니다.

```jsx
# 관리자 권한 PowerShell
netsh int ip reset
netsh winsock reset
```

Windows의 TCP/IP 관련 설정을 초기화하고, Winsock 카탈로그를 초기화했습니다.

> Winsock(Windows Sockets)은 Windows 애플리케이션에서 네트워크 소켓 통신을 수행할 때 사용하는 API 계층

문제는 해결되었지만 초기화 전후의 네트워크 로그가 없으므로 원인이 TCP/IP 설정인지 Winsock 카탈로그인지 WSL 네트워크 상태인지 단정할 수 없습니다.
앞으로 비슷한 문제가 발생한다면 네트워크 전체를 초기화하기보다 문제 범위를 먼저 파악하겠습니다.

## 8. 실습 정리

이번 실습을 통해 Docker의 설치 상태를 확인하고 이미지 기반 컨테이너 실행 및 관리 방법을 학습했습니다. Ubuntu 컨테이너에서 Linux 기본 명령과 파일 권한 변경을 수행했고 Nginx 기반 Dockerfile 작성과 포트 매핑을 연습했습니다. 또한 호스트 디렉터리를 컨테이너에 연결하여 컨테이너 삭제 후에도 파일이 유지되는 것을 확인했습니다.
