---
title: Docker Tutorial
date: 2024-06-21 14:03:41 +0800
categories: [CS, Docker]
tags: [docker, tutorial]    # TAG names should always be **lowercase**
---

## Docker

官方网址：[docker官网](https://www.docker.com/)

官方镜像网址：[docker images](https://hub.docker.com/)

### 零碎知识

镜像（image）的完整名称是：`镜像名:标签（版本）` 

- 常用操作：

![img](https://kv56iiakc2.feishu.cn/space/api/box/stream/download/asynccode/?code=NDU4YjM5MjYxNjdmMTE3Mzg1YzMyZmYwNzM3ZmQ1ZDFfOFZFMDlTbUV5TVBmanRzWEZiOHVUYUxVYjhoQkkySzNfVG9rZW46QmVlWmJFUGNqbzF3QjR4cGN3T2Nsb2s5bkF6XzE3NDQ0NjM2MjU6MTc0NDQ2NzIyNV9WNA)

### Install

在开发者-文档-manuals-docker engine-install中有安装教程

![img](https://kv56iiakc2.feishu.cn/space/api/box/stream/download/asynccode/?code=NWIzMzNlYzcxMWQxMDJhNGI3ODBlZjU0YmEwNmMwYzVfMTNaQVVuU0pKblpDVHFtTXVZU1UyRFNwS0Vub2Z1UnpfVG9rZW46TG1uYWIzQ0pWb2tpeEV4N3dVTmNHOHM0bkZnXzE3NDQ0NjM2MjU6MTc0NDQ2NzIyNV9WNA)

### 镜像image相关

#### 检索：docker search

```
docker search <image name>
```

以搜索nginx image为例，NAME是名字，中间为DESCRIPTION描述，STARS是该镜像有多少人气，OFFICIAL表示官方镜像，ok表示官方镜像

``` shell
xw@xw:~$ docker search nginx
NAME                              DESCRIPTION                                     STARS     OFFICIAL
nginx                             Official build of Nginx.                        20126     [OK]
nginx/nginx-quic-qns              NGINX QUIC interop                              1         
nginx/nginx-ingress               NGINX and  NGINX Plus Ingress Controllers fo…   94        
nginx/nginx-ingress-operator      NGINX Ingress Operator for NGINX and NGINX P…   2         
nginx/nginx-prometheus-exporter   NGINX Prometheus Exporter for NGINX and NGIN…   43        
nginx/unit                        This repository is retired, use the Docker o…   63        
nginx/unit-preview                Unit preview features                           0         
bitnami/nginx                     Bitnami container image for NGINX               193       
rapidfort/nginx                   RapidFort optimized, hardened image for NGINX   15        
kasmweb/nginx                     An Nginx image based off nginx:alpine and in…   8         
ubuntu/nginx                      Nginx, a high-performance reverse proxy & we…   116       
chainguard/nginx                  Minimal Wolfi-based nginx HTTP, reverse prox…   2         
dockette/nginx                    Nginx SSL / HSTS / HTTP2                        3         
jitesoft/nginx                    Nginx on alpine linux                           0         
docksal/nginx                     Nginx service image for Docksal                 0         
geokrety/nginx                    Our customized nginx image                      0         
gluufederation/nginx               A customized NGINX image containing a consu…   1         
okteto/nginx                                                                      0         
objectscale/nginx                                                                 0         
intel/nginx                                                                       0         
circleci/nginx                    This image is for internal use                  2         
vmware/nginx                                                                      2         
rancher/nginx                                                                     2         
bitnamicharts/nginx                                                               0         
redash/nginx                      Pre-configured nginx to proxy linked contain…   2         
xw@xw:~$ 
```

#### 下载：docker pull

```
docker pull <image name>
```

注意这里`Using default tag: latest`，这是因为我们只给了image的name，没有指定版本号，所以docker将默认下载最新版本

``` shell
xw@xw:~$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
e4fff0779e6d: Pull complete 
2a0cb278fd9f: Pull complete 
7045d6c32ae2: Pull complete 
03de31afb035: Pull complete 
0f17be8dcff2: Pull complete 
14b7e5e8f394: Pull complete 
23fa5a7b99a6: Pull complete 
Digest: sha256:447a8665cc1dab95b1ca778e162215839ccbb9189104c79d7ec3a81e14577add
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
xw@xw:~$ 
```

#### 列表：docker images

- `docker images`
- `docker image ls`

``` shell
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
safe          udp       832157c79579   3 weeks ago     511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
ubuntu        20.04     5f5250218d28   2 months ago    72.8MB
hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
xw@xw:~$ 
```

#### 删除：docker rmi

- `docker rmi <image name>:<TAG>`
- `docker rmi <image ID>`

``` shell
xw@xw:~$ docker rmi ubuntu:20.04
Untagged: ubuntu:20.04
Untagged: ubuntu@sha256:0b897358ff6624825fb50d20ffb605ab0eaea77ced0adb8c6a4b756513dec6fc
Deleted: sha256:5f5250218d28ad6612bf653eced407165dd6475a4daf9210b299fed991e172e9
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
safe          udp       832157c79579   3 weeks ago     511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
xw@xw:~$ 
```

### 容器（container）相关

![img](https://kv56iiakc2.feishu.cn/space/api/box/stream/download/asynccode/?code=MGI1NmJlYTIyMjk5ZGExMTQ5YjU3YmY1MWIzOGMxNjFfNm1TQ3h3OEtPZnZpMTduQnRITWIxaE5XTWphU2JETklfVG9rZW46UGk3a2JBTE9Cb21ITEJ4TTVzOGMyclZtbkVkXzE3NDQ0NjM2MjU6MTc0NDQ2NzIyNV9WNA)

#### 运行（run）容器 / 查看：docker ps

- `docker run`
- `docker ps`
- `docker ps -a`

Docker run的相关options非常多，但是可以省略，然后image后面的command和arg可以不写，每一个image中都有自己的指令，**当你想修改image中默认的指令时可以带上后面的command和arg**

``` shell
xw@xw:~$ docker run --help

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Create and run a new container from an image

Aliases:
  docker container run, docker run

Options:
      --add-host list                    Add a custom host-to-IP mapping (host:ip)
      --annotation map                   Add an annotation to the container (passed through to the OCI runtime) (default map[])
  -a, --attach list                      Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16              Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device list         Block IO weight (relative device weight) (default [])
      --cap-add list                     Add Linux capabilities
      --cap-drop list                    Drop Linux capabilities
      --cgroup-parent string             Optional parent cgroup for the container
      --cgroupns string                  Cgroup namespace to use (host|private)
                                         'host':    Run the container in the Docker host's cgroup namespace
                                         'private': Run the container in its own private cgroup namespace
                                         '':        Use the cgroup namespace as configured by the
                                                    default-cgroupns-mode option on the daemon (default)
      --cidfile string                   Write the container ID to the file
      --cpu-period int                   Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                    Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int                Limit CPU real-time period in microseconds
      --cpu-rt-runtime int               Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                   CPU shares (relative weight)
      --cpus decimal                     Number of CPUs
      --cpuset-cpus string               CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string               MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                           Run container in background and print container ID
      --detach-keys string               Override the key sequence for detaching a container
      --device list                      Add a host device to the container
      --device-cgroup-rule list          Add a rule to the cgroup allowed devices list
      --device-read-bps list             Limit read rate (bytes per second) from a device (default [])
      --device-read-iops list            Limit read rate (IO per second) from a device (default [])
      --device-write-bps list            Limit write rate (bytes per second) to a device (default [])
      --device-write-iops list           Limit write rate (IO per second) to a device (default [])
      --disable-content-trust            Skip image verification (default true)
      --dns list                         Set custom DNS servers
      --dns-option list                  Set DNS options
      --dns-search list                  Set custom DNS search domains
      --domainname string                Container NIS domain name
      --entrypoint string                Overwrite the default ENTRYPOINT of the image
  -e, --env list                         Set environment variables
      --env-file list                    Read in a file of environment variables
      --expose list                      Expose a port or a range of ports
      --gpus gpu-request                 GPU devices to add to the container ('all' to pass all GPUs)
      --group-add list                   Add additional groups to join
      --health-cmd string                Command to run to check health
      --health-interval duration         Time between running the check (ms|s|m|h) (default 0s)
      --health-retries int               Consecutive failures needed to report unhealthy
      --health-start-interval duration   Time between running the check during the start period (ms|s|m|h) (default 0s)
      --health-start-period duration     Start period for the container to initialize before starting health-retries countdown
                                         (ms|s|m|h) (default 0s)
      --health-timeout duration          Maximum time to allow one check to run (ms|s|m|h) (default 0s)
      --help                             Print usage
  -h, --hostname string                  Container host name
      --init                             Run an init inside the container that forwards signals and reaps processes
  -i, --interactive                      Keep STDIN open even if not attached
      --ip string                        IPv4 address (e.g., 172.30.100.104)
      --ip6 string                       IPv6 address (e.g., 2001:db8::33)
      --ipc string                       IPC mode to use
      --isolation string                 Container isolation technology
      --kernel-memory bytes              Kernel memory limit
  -l, --label list                       Set meta data on a container
      --label-file list                  Read in a line delimited file of labels
      --link list                        Add link to another container
      --link-local-ip list               Container IPv4/IPv6 link-local addresses
      --log-driver string                Logging driver for the container
      --log-opt list                     Log driver options
      --mac-address string               Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                     Memory limit
      --memory-reservation bytes         Memory soft limit
      --memory-swap bytes                Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int            Tune container memory swappiness (0 to 100) (default -1)
      --mount mount                      Attach a filesystem mount to the container
      --name string                      Assign a name to the container
      --network network                  Connect a container to a network
      --network-alias list               Add network-scoped alias for the container
      --no-healthcheck                   Disable any container-specified HEALTHCHECK
      --oom-kill-disable                 Disable OOM Killer
      --oom-score-adj int                Tune host's OOM preferences (-1000 to 1000)
      --pid string                       PID namespace to use
      --pids-limit int                   Tune container pids limit (set -1 for unlimited)
      --platform string                  Set platform if server is multi-platform capable
      --privileged                       Give extended privileges to this container
  -p, --publish list                     Publish a container's port(s) to the host
  -P, --publish-all                      Publish all exposed ports to random ports
      --pull string                      Pull image before running ("always", "missing", "never") (default "missing")
  -q, --quiet                            Suppress the pull output
      --read-only                        Mount the container's root filesystem as read only
      --restart string                   Restart policy to apply when a container exits (default "no")
      --rm                               Automatically remove the container and its associated anonymous volumes when it exits
      --runtime string                   Runtime to use for this container
      --security-opt list                Security Options
      --shm-size bytes                   Size of /dev/shm
      --sig-proxy                        Proxy received signals to the process (default true)
      --stop-signal string               Signal to stop the container
      --stop-timeout int                 Timeout (in seconds) to stop a container
      --storage-opt list                 Storage driver options for the container
      --sysctl map                       Sysctl options (default map[])
      --tmpfs list                       Mount a tmpfs directory
  -t, --tty                              Allocate a pseudo-TTY
      --ulimit ulimit                    Ulimit options (default [])
  -u, --user string                      Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                    User namespace to use
      --uts string                       UTS namespace to use
  -v, --volume list                      Bind mount a volume
      --volume-driver string             Optional volume driver for the container
      --volumes-from list                Mount volumes from the specified container(s)
  -w, --workdir string                   Working directory inside the container
xw@xw:~$ 
```

- 示例1：
  - 首先使docker imags列出所有的image，然后docker run启动了hello-world这个image
  - 启动之后，terminal打印出信息，然后这个镜像就结束了
  - 使用docker ps命令可以查看running态的image，这里结束了就不再显示
  - 那么使用docker ps -a可以打印所有的运行过的image信息
  - 可以看到 hello-world是最新运行的，现在是exit状态

``` shell
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
safe          udp       832157c79579   3 weeks ago     511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
xw@xw:~$ docker run hello-world

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

xw@xw:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
xw@xw:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                      PORTS     NAMES
83d343115bfa   hello-world    "/hello"      8 seconds ago   Exited (0) 7 seconds ago              angry_hugle
42f4bc62acb6   ubuntu:22.04   "/bin/bash"   7 minutes ago   Exited (0) 7 minutes ago              confident_saha
9685dabd7205   ubuntu:22.04   "/bin/bash"   12 hours ago    Exited (0) 12 hours ago               competent_vaughan
d4ef396afd74   ubuntu:22.04   "/bin/bash"   13 hours ago    Exited (0) 13 hours ago               quirky_einstein
2887972b2a0e   hello-world    "/hello"      15 hours ago    Exited (0) 15 hours ago               dreamy_stonebraker
1a1c6b0a9014   safe:udp       "/bin/bash"   2 days ago      Exited (255) 45 hours ago             safe_udp
xw@xw:~$ 
```

- 示例2：
  - 这里运行了NGINX，它默认会占用terminal，所以我们再打开一个终端
  - 使用docker ps指令，可以看到IMAGE：nginx正在运行，STATUS是up状态，占用的端口号是80/tcp

``` shell
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    5ef79149e0ec   2 weeks ago     188MB
safe          udp       832157c79579   3 weeks ago     511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
xw@xw:~$ docker run nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/08/29 03:16:33 [notice] 1#1: using the "epoll" event method
2024/08/29 03:16:33 [notice] 1#1: nginx/1.27.1
2024/08/29 03:16:33 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/08/29 03:16:33 [notice] 1#1: OS: Linux 6.8.0-40-generic
2024/08/29 03:16:33 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/08/29 03:16:33 [notice] 1#1: start worker processes
2024/08/29 03:16:33 [notice] 1#1: start worker process 29
2024/08/29 03:16:33 [notice] 1#1: start worker process 30
2024/08/29 03:16:33 [notice] 1#1: start worker process 31
2024/08/29 03:16:33 [notice] 1#1: start worker process 32
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:47 [notice] 1#1: signal 28 (SIGWINCH) received


// another terminal
xw@xw:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS     NAMES
1882aa18c3e6   nginx     "/docker-entrypoint.…"   10 seconds ago   Up 9 seconds   80/tcp    zen_dewdney
xw@xw:~$ 
```

##### Docker run 附带参数option

- `docker run -d --name <yourname> -p 80:80 <image name>`

  - `-d` 表示后台运行

  - `--name mynginx` 表示将该容器命名为mynginx，不加这个选项的话将会随机命名

  - `-p 80:80` 表示端口映射，第一个80表示主机的80端口，第二个80代表的是容器的80端口；当想让外部可以访问容器内部，就必须进行端口映射

    - 端口映射对于主机来说，每个端口只能用一次，当再运行一个容器，这个新的容器的80端口还可以进行端口映射（因为每个容器之间是相互独立，相互隔离的，因此两个容器的80端口互不影响，就相当于有两台服务器），但是主机的80端口已经被之前的端口占用了，此时就需要用主机的其他端口对第二个容器进行端口映射

  - ``` shell
    xw@xw:~$ docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    xw@xw:~$ docker images
    REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
    nginx         latest    5ef79149e0ec   2 weeks ago     188MB
    safe          udp       832157c79579   3 weeks ago     511MB
    ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
    hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
    xw@xw:~$ docker run -d --name mynginx -p 80:80 nginx
    323597b28c968bd8d4ad27c6e4ac5b5acfc3a1d549c2eabe24cf6df003f9279e
    xw@xw:~$ docker ps
    CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS                               NAMES
    323597b28c96   nginx     "/docker-entrypoint.…"   10 seconds ago   Up 9 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   mynginx
    xw@xw:~$ 
    ```

#### 停止：docker stop

- `docker stop <container name>or<container ID>`

``` shell
xw@xw:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS     NAMES
1882aa18c3e6   nginx     "/docker-entrypoint.…"   10 seconds ago   Up 9 seconds   80/tcp    zen_dewdney
xw@xw:~$ docker stop nginx
Error response from daemon: No such container: nginx
xw@xw:~$ docker stop zen_dewdney
zen_dewdney
xw@xw:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
xw@xw:~$ 

// 原来启动nginx的终端
xw@xw:~$ docker run nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/08/29 03:16:33 [notice] 1#1: using the "epoll" event method
2024/08/29 03:16:33 [notice] 1#1: nginx/1.27.1
2024/08/29 03:16:33 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/08/29 03:16:33 [notice] 1#1: OS: Linux 6.8.0-40-generic
2024/08/29 03:16:33 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/08/29 03:16:33 [notice] 1#1: start worker processes
2024/08/29 03:16:33 [notice] 1#1: start worker process 29
2024/08/29 03:16:33 [notice] 1#1: start worker process 30
2024/08/29 03:16:33 [notice] 1#1: start worker process 31
2024/08/29 03:16:33 [notice] 1#1: start worker process 32
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:47 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:19:53 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:20:29 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:20:39 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
2024/08/29 03:20:39 [notice] 29#29: gracefully shutting down
2024/08/29 03:20:39 [notice] 31#31: gracefully shutting down
2024/08/29 03:20:39 [notice] 30#30: gracefully shutting down
2024/08/29 03:20:39 [notice] 31#31: exiting
2024/08/29 03:20:39 [notice] 30#30: exiting
2024/08/29 03:20:39 [notice] 29#29: exiting
2024/08/29 03:20:39 [notice] 29#29: exit
2024/08/29 03:20:39 [notice] 30#30: exit
2024/08/29 03:20:39 [notice] 31#31: exit
2024/08/29 03:20:39 [notice] 32#32: gracefully shutting down
2024/08/29 03:20:39 [notice] 32#32: exiting
2024/08/29 03:20:39 [notice] 32#32: exit
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 29
2024/08/29 03:20:39 [notice] 1#1: worker process 29 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: signal 29 (SIGIO) received
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 31
2024/08/29 03:20:39 [notice] 1#1: worker process 31 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: signal 29 (SIGIO) received
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2024/08/29 03:20:39 [notice] 1#1: worker process 32 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: signal 29 (SIGIO) received
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2024/08/29 03:20:39 [notice] 1#1: worker process 30 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: exit
xw@xw:~$ 
```

#### 启动：docker start

- `docker start <container ID>or<container name>`
  - 首先docker ps -a列出所有运行过的container
  - 根据ID或者NAME，运行docker start
  - 再用docker ps查看正在运行的container

``` shell
xw@xw:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS                    PORTS     NAMES
1882aa18c3e6   nginx          "/docker-entrypoint.…"   3 hours ago    Exited (0) 3 hours ago              zen_dewdney
83d343115bfa   hello-world    "/hello"                 4 hours ago    Exited (0) 4 hours ago              angry_hugle
42f4bc62acb6   ubuntu:22.04   "/bin/bash"              4 hours ago    Exited (0) 4 hours ago              confident_saha
9685dabd7205   ubuntu:22.04   "/bin/bash"              16 hours ago   Exited (0) 16 hours ago             competent_vaughan
d4ef396afd74   ubuntu:22.04   "/bin/bash"              17 hours ago   Exited (0) 17 hours ago             quirky_einstein
2887972b2a0e   hello-world    "/hello"                 18 hours ago   Exited (0) 18 hours ago             dreamy_stonebraker
1a1c6b0a9014   safe:udp       "/bin/bash"              2 days ago     Exited (255) 2 days ago             safe_udp
xw@xw:~$ docker start 188
188
xw@xw:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS          PORTS     NAMES
1882aa18c3e6   nginx     "/docker-entrypoint.…"   3 hours ago   Up 11 seconds   80/tcp    zen_dewdney
xw@xw:~$ 
```

#### 重启：docker restart

- `docker restart <container name>`
  - 注意，这里不管是正在运行的container还是已经终止得container，再使用restart命令后都会重启

#### 状态：docker stats

- `docker stats <container ID>or<container name>`

``` shell
CONTAINER ID   NAME          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O       BLOCK I/O    PIDS
1882aa18c3e6   zen_dewdney   0.00%     4.492MiB / 3.778GiB   0.12%     3.75kB / 0B   0B / 4.1kB   5
```

#### 日志：docker logs

- `docker logs <container ID>or<container name>`

``` shell
xw@xw:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS          PORTS     NAMES
1882aa18c3e6   nginx     "/docker-entrypoint.…"   4 hours ago   Up 12 minutes   80/tcp    zen_dewdney
xw@xw:~$ docker logs 188
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/08/29 03:16:33 [notice] 1#1: using the "epoll" event method
2024/08/29 03:16:33 [notice] 1#1: nginx/1.27.1
2024/08/29 03:16:33 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/08/29 03:16:33 [notice] 1#1: OS: Linux 6.8.0-40-generic
2024/08/29 03:16:33 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/08/29 03:16:33 [notice] 1#1: start worker processes
2024/08/29 03:16:33 [notice] 1#1: start worker process 29
2024/08/29 03:16:33 [notice] 1#1: start worker process 30
2024/08/29 03:16:33 [notice] 1#1: start worker process 31
2024/08/29 03:16:33 [notice] 1#1: start worker process 32
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:37 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:16:47 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:19:53 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:20:29 [notice] 1#1: signal 28 (SIGWINCH) received
2024/08/29 03:20:39 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
2024/08/29 03:20:39 [notice] 29#29: gracefully shutting down
2024/08/29 03:20:39 [notice] 31#31: gracefully shutting down
2024/08/29 03:20:39 [notice] 30#30: gracefully shutting down
2024/08/29 03:20:39 [notice] 31#31: exiting
2024/08/29 03:20:39 [notice] 30#30: exiting
2024/08/29 03:20:39 [notice] 29#29: exiting
2024/08/29 03:20:39 [notice] 29#29: exit
2024/08/29 03:20:39 [notice] 30#30: exit
2024/08/29 03:20:39 [notice] 31#31: exit
2024/08/29 03:20:39 [notice] 32#32: gracefully shutting down
2024/08/29 03:20:39 [notice] 32#32: exiting
2024/08/29 03:20:39 [notice] 32#32: exit
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 29
2024/08/29 03:20:39 [notice] 1#1: worker process 29 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: signal 29 (SIGIO) received
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 31
2024/08/29 03:20:39 [notice] 1#1: worker process 31 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: signal 29 (SIGIO) received
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2024/08/29 03:20:39 [notice] 1#1: worker process 32 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: signal 29 (SIGIO) received
2024/08/29 03:20:39 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2024/08/29 03:20:39 [notice] 1#1: worker process 30 exited with code 0
2024/08/29 03:20:39 [notice] 1#1: exit
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: IPv6 listen already enabled
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/08/29 06:41:40 [notice] 1#1: using the "epoll" event method
2024/08/29 06:41:40 [notice] 1#1: nginx/1.27.1
2024/08/29 06:41:40 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/08/29 06:41:40 [notice] 1#1: OS: Linux 6.8.0-40-generic
2024/08/29 06:41:40 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/08/29 06:41:40 [notice] 1#1: start worker processes
2024/08/29 06:41:40 [notice] 1#1: start worker process 22
2024/08/29 06:41:40 [notice] 1#1: start worker process 23
2024/08/29 06:41:40 [notice] 1#1: start worker process 24
2024/08/29 06:41:40 [notice] 1#1: start worker process 25
xw@xw:~$ 
```

#### 删除：docker rm

- `docker rm <container name>or<container ID>`
  - 如果容器在停止状态则可直接删除
  - 如果在容器在运行的状态下直接删除，会报错，同时提示你可以使用强制删除命令
  - `docker rm -f <container ID>or<container name>`

``` shell
xw@xw:~$ docker rm 188
Error response from daemon: cannot remove container "/zen_dewdney": container is running: stop the container before removing or force remove
xw@xw:~$ 

xw@xw:~$ docker rm -f 188
188
xw@xw:~$ 
```

#### 进入：docker exec

- `docker exec -it <container ID>or<container name> /bin/bash`

  - `docker exec`是进入这个容器

  - `-it` 是以交互模式进入

  - `/bin/bash` 使用控制台来进行交互

  - ``` shell
    xw@xw:~$ docker exec -it mynginx /bin/bash
    root@323597b28c96:/# 
    ```

可以看到这里从`xw@xw`变成了`root@323597b28c96`

此时，若需要退出该容器，使用`exit`即可

``` shell
xw@xw:~$ docker exec -it mynginx /bin/bash
root@323597b28c96:/# ls /
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  root  sbin  sys  usr
root@323597b28c96:/# ls
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  root  sbin  sys  usr
root@323597b28c96:/# exit
exit
xw@xw:~$ 
```

### 保存镜像

#### 提交：docker commit

将容器container提交成一个镜像image

帮助文档：

``` shell
xw@xw:~$ docker commit --help

Usage:  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

Create a new image from a container's changes

Aliases:
  docker container commit, docker commit

Options:
  -a, --author string    Author (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -c, --change list      Apply Dockerfile instruction to the created image
  -m, --message string   Commit message
  -p, --pause            Pause container during commit (default true)
xw@xw:~$
```

示例：

- `-m "commit test"`：表示要提交的信息
- 第一个`mynginx`是你指定的容器
- `mynginx::v1.0`：是指定镜像名称以及标签TAG

``` shell
xw@xw:~$ docker commit -m "commit test" mynginx mynginx:v1.0
sha256:0ba1a119628a2483936b768bb3834bac6db04e477ffd665a07d08b804bebc2f6
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
mynginx       v1.0      0ba1a119628a   4 seconds ago   188MB
nginx         latest    5ef79149e0ec   2 weeks ago     188MB
safe          udp       832157c79579   3 weeks ago     511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
xw@xw:~$ 
```

#### 保存：docker save

将镜像保存为文件

帮助文档：

``` shell
xw@xw:~$ docker save --help

Usage:  docker save [OPTIONS] IMAGE [IMAGE...]

Save one or more images to a tar archive (streamed to STDOUT by default)

Aliases:
  docker image save, docker save

Options:
  -o, --output string   Write to a file, instead of STDOUT
xw@xw:~$ 
```

示例：

- `docker save -o mynginx.tar mynginx:v1.0`
- `-o mynginx.tar`表示将镜像保存成压缩包
- `mynginx:v1.0` 表示要保存的那个镜像

``` shell
xw@xw:~$ docker save -o mynginx.tar mynginx:v1.0 
xw@xw:~$ ls
Clash    docker_sh  Downloads  learngit  mynginx.tar  Public    snap       Videos       work
Desktop  Documents  files      Music     Pictures      safe-udp  Templates  VscodeFiles  XV6
xw@xw:~$ 
```

#### 加载：docker load

将文件加载成镜像image

帮助文档：

``` shell
xw@xw:~$ docker load --help

Usage:  docker load [OPTIONS]

Load an image from a tar archive or STDIN

Aliases:
  docker image load, docker load

Options:
  -i, --input string   Read from tar archive file, instead of STDIN
  -q, --quiet          Suppress the load output
xw@xw:~$ 
```

示例：

- `docker load -i mynginx.tar`
- `-i` 表示从制定压缩包中读取镜像

``` shell
xw@xw:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                     PORTS     NAMES
323597b28c96   nginx          "/docker-entrypoint.…"   34 minutes ago   Exited (0) 2 seconds ago             mynginx
83d343115bfa   hello-world    "/hello"                 5 hours ago      Exited (0) 5 hours ago               angry_hugle
42f4bc62acb6   ubuntu:22.04   "/bin/bash"              5 hours ago      Exited (0) 5 hours ago               confident_saha
9685dabd7205   ubuntu:22.04   "/bin/bash"              18 hours ago     Exited (0) 18 hours ago              competent_vaughan
d4ef396afd74   ubuntu:22.04   "/bin/bash"              19 hours ago     Exited (0) 19 hours ago              quirky_einstein
2887972b2a0e   hello-world    "/hello"                 20 hours ago     Exited (0) 20 hours ago              dreamy_stonebraker
1a1c6b0a9014   safe:udp       "/bin/bash"              2 days ago       Exited (255) 2 days ago              safe_udp
xw@xw:~$ docker rm mynginx 
mynginx
xw@xw:~$ docker rm mynginx 
Error response from daemon: No such container: mynginx
xw@xw:~$ docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED        STATUS                    PORTS     NAMES
83d343115bfa   hello-world    "/hello"      5 hours ago    Exited (0) 5 hours ago              angry_hugle
42f4bc62acb6   ubuntu:22.04   "/bin/bash"   5 hours ago    Exited (0) 5 hours ago              confident_saha
9685dabd7205   ubuntu:22.04   "/bin/bash"   18 hours ago   Exited (0) 18 hours ago             competent_vaughan
d4ef396afd74   ubuntu:22.04   "/bin/bash"   19 hours ago   Exited (0) 19 hours ago             quirky_einstein
2887972b2a0e   hello-world    "/hello"      20 hours ago   Exited (0) 20 hours ago             dreamy_stonebraker
1a1c6b0a9014   safe:udp       "/bin/bash"   2 days ago     Exited (255) 2 days ago             safe_udp
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
mynginx       v1.0      0ba1a119628a   14 minutes ago   188MB
nginx         latest    5ef79149e0ec   2 weeks ago      188MB
safe          udp       832157c79579   3 weeks ago      511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago     77.9MB
hello-world   latest    d2c94e258dcb   16 months ago    13.3kB
xw@xw:~$ docker rmi mynginx:v1.0 
Untagged: mynginx:v1.0
Deleted: sha256:0ba1a119628a2483936b768bb3834bac6db04e477ffd665a07d08b804bebc2f6
Deleted: sha256:b1e8f285efa07055d5d661efc9c60f0b1b3b22e53a9e8aa4da128b9421f218c1
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    5ef79149e0ec   2 weeks ago     188MB
safe          udp       832157c79579   3 weeks ago     511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago    77.9MB
hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
xw@xw:~$ ls
Clash    docker_sh  Downloads  learngit  mynginx.tar  Public    snap       Videos       work
Desktop  Documents  files      Music     Pictures     safe-udp  Templates  VscodeFiles  XV6
xw@xw:~$ docker load -i mynginx.tar 
e7bd89846ea2: Loading layer [==================================================>]  11.78kB/11.78kB
Loaded image: mynginx:v1.0
xw@xw:~$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
mynginx       v1.0      0ba1a119628a   16 minutes ago   188MB
nginx         latest    5ef79149e0ec   2 weeks ago      188MB
safe          udp       832157c79579   3 weeks ago      511MB
ubuntu        22.04     8a3cdc4d1ad3   2 months ago     77.9MB
hello-world   latest    d2c94e258dcb   16 months ago    13.3kB
xw@xw:~$ 
```

### 分享镜像

#### 注册账号并登录

在[官方网站](https://app.docker.com/)注册账号后，即可登录

#### 登录 docker login

- `docker load`

``` shell
xw@xw:~$ docker login
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: xiaowuer
Password: 
WARNING! Your password will be stored unencrypted in /home/xw/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credential-stores

Login Succeeded
xw@xw:~$ 
```

#### 命名：docker tag

docker社区中规定，用户制作的镜像需要以`用户名/镜像名:标签`的命名，所以我们需要重新命名镜像

- `docker tag`

  - ``` shell
    xw@xw:~$ docker tag --help
    
    Usage:  docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
    
    Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
    
    Aliases:
      docker image tag, docker tag
    xw@xw:~$ 
    ```

示例：

- `docker tag mynginx:v1.0 xiaowuer/mynginx:v2.0`

``` shell
xw@xw:~$ docker tag mynginx:v1.0 xiaowuer/mynginx:v2.0
xw@xw:~$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
mynginx            v1.0      0ba1a119628a   31 minutes ago   188MB
xiaowuer/mynginx   v2.0      0ba1a119628a   31 minutes ago   188MB
nginx              latest    5ef79149e0ec   2 weeks ago      188MB
safe               udp       832157c79579   3 weeks ago      511MB
ubuntu             22.04     8a3cdc4d1ad3   2 months ago     77.9MB
hello-world        latest    d2c94e258dcb   16 months ago    13.3kB
xw@xw:~$ 
```

#### 推送：docker push

- `docker push <image name>`
- 这里因为网络原因没有推送结束

``` shell
xw@xw:~$ docker push xiaowuer/mynginx:v2.0 
The push refers to repository [docker.io/xiaowuer/mynginx]
e7bd89846ea2: Pushing [==================================================>]  11.78kB
5f0272c6e96d: Retrying in 11 seconds 
f4f00eaedec7: Retrying in 11 seconds 
55e54df86207: Retrying in 13 seconds 
ec1a2ca4ac87: Retrying in 11 seconds 
8b87c0c66524: Waiting 
72db5db515fd: Waiting 
9853575bc4f9: Waiting 
```

### Dockerfile

Dockerfile 相关详细命令可参考[官方网站](https://docs.docker.com/reference/dockerfile/)

The Dockerfile supports the following instructions: