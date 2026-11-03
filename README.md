### 프로젝트 개요
  리눅스 명령어, docker 설치와 운영, Git/Github 실습

### 실행환경

```jsx
- OS: Window 11
	WSL (Windows Subsystem for Linux)
- Docker: 29.9.2
- Git: 2.53.0
```

### 수행 항목 체크리스트
[x] 터미널 기본 조작 (pwd/cd/mkdir/touch/cat/rm/mv)
[x] 권한 변경 실습
[x] Docker - 설치, 점검 / hello-world 실행
[x] Docker 컨테이너 진입 및 명령 수행
[x] 컨테이너 종료/유지(attach vs exec) 정리
[x] Dockerfile 작성 및 이미지 빌드/실행
[x] 포트 매핑 및 접속
[x] 바인드 마운트
[x] Docker 볼륨 영속성 검증
[x] Git 설정 및 GitHub 업로드

---

#### 1. Docker 설치 및 기본 점검

- version, info 확인

```jsx
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

- docker 기본 운영 명령 수행

```jsx
$ docker images                                                                                                  i Info →   U  In Use
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

- 컨테이너 실행 실습 (hello-world)

```jsx
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

- 컨테이너 실행하고 내부 진입 후 명령 수행
- attach/exec 차이 관찰

```jsx
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

| **구분** | **`docker attach`** | **`docker exec`** |
| --- | --- | --- |
| **접속 대상** | 컨테이너의 **메인 프로세스(PID 1)** | 컨테이너 내부에 **새로운 프로세스** 생성 |
| **`exit` 입력 결과** | **컨테이너 종료됨** | **컨테이너 유지됨** (쉘만 종료) |
| **권장 사용법** | 로그 모니터링 등 단순 접속 | 디버깅 및 명령어 실행 (표준 방식) |

- 권한 실습

```jsx
root@27dec3d095c9:~# touch test.txt

root@27dec3d095c9:~# ls
test.txt

root@27dec3d095c9:~# ls -l test.txt
-rw-r--r-- 1 root root 0 Nov  3 00:46 test.txt

root@27dec3d095c9:~# chmod 700 test.txt

root@27dec3d095c9:~# ls -l test.txt
-rwx------ 1 root root 0 Nov  3 00:46 test.txt
```

| **숫자** | **권한** |
| --- | --- |
| 4 | r (읽기) |
| 2 | w (쓰기) |
| 1 | x (실행) |
- `644`: 소유자는 읽기+쓰기(6), 그룹/기타는 읽기만(4) — 일반 파일 기본값
- `755`: 소유자는 전부(7), 그룹/기타는 읽기+실행(5) — 디렉토리·실행 파일 기본값
- `700`: 소유자만 전부(7), 그룹/기타는 아무 권한 없음(0) — 완전 비공개

- Dockerfile 기반 웹 서버 컨테이너
  - nginx alpine을 베이스로 사용하여 app/ 경로에 생성된 html을 복사
  - 이미지 빌드 후 컨테이너 실행

```jsx
FROM nginx:stable-alpine3.24-perl
COPY app/ /usr/share/nginx/html
```

```jsx
$ docker build -t web-server .

codyssey@GNIA-C401-C01:~$ docker run -d -p 80:80 --name my-server web-server
```


- 포트 매핑
  - host의 80번 포트와 container의 80번 포트 매핑
    

```jsx
$ docker run --name server -d -p 80:80 nginx
```


- 바인드 마운트
  - 호스트의 특정 파일이나 디렉토리 경로를 도커 컨테이너 내부의 경로와 직접 연결
  - 호스트 디렉토리 절대경로[/home/codyssey/bind/html]:컨테이너 디렉토리 경로[/usr/share/nginx/html] 연결
  - index.html 내용 업데이트 >> 연결 확인 (*하단 스크린샷 첨부)
    

```jsx
codyssey@GNIA-C401-C01:~$ docker run --name bind-nginx -d -p 80:80 -v /home/codyssey/bind/html:/usr/share/nginx/html:ro
-d nginx

codyssey@GNIA-C401-C01:~$ vi bind/html/index.html
```


- 볼륨 영속성
  - 컨테이너가 삭제되거나 종료되어도 데이터가 유지됨
    

```jsx
codyssey@GNIA-C401-C01:~$ mkdir data

codyssey@GNIA-C401-C01:~$ ls
bind  data

codyssey@GNIA-C401-C01:~$ cd data

codyssey@GNIA-C401-C01:~/data$ echo "hello" > test.txt

codyssey@GNIA-C401-C01:~/data$ cat test.txt
hello

codyssey@GNIA-C401-C01:~$ docker run --name vol-ubuntu -it -v ./data:/home/data ubuntu bash

root@ed34a309baab:/# cd home

root@ed34a309baab:/home# ls
data  ubuntu

root@ed34a309baab:/home# cd data/

root@ed34a309baab:/home/data# ls
test.txt

root@ed34a309baab:/home/data# cat test.txt
hello
```

- 화면 보기
  [Screenshot](https://app.notion.com/p/E1-1-40d34c21c2668095a42dec1e5bfba238?source=copy_link)
