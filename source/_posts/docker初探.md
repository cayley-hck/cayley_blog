---
title: docker初探
date: 2016-09-21 10:33:55
tags:
---
先安装docker for mac ,可以使用homebrew||到官网下载
{% codeblock %}
brew cask install docker
{% endcodeblock %}

官网地址：
https://docs.docker.com/docker-for-mac/

命令：
 cayley@cayleydeMacBook-Air  ~  docker -h
Usage: docker [OPTIONS] COMMAND [arg...]
       docker [ --help | -v | --version ]

A self-sufficient runtime for containers.

Options:

  --config=~/.docker              Location of client config files
  -D, --debug                     Enable debug mode
  -H, --host=[]                   Daemon socket(s) to connect to
  -h, --help                      Print usage
  -l, --log-level=info            Set the logging level
  --tls                           Use TLS; implied by --tlsverify
  --tlscacert=~/.docker/ca.pem    Trust certs signed only by this CA
  --tlscert=~/.docker/cert.pem    Path to TLS certificate file
  --tlskey=~/.docker/key.pem      Path to TLS key file
  --tlsverify                     Use TLS and verify the remote
  -v, --version                   Print version information and quit

Commands:
    attach    连接到正在运行中的容器
    		  Usage: docker attach [OPTIONS] CONTAINER
    		  CONTAINER:容器名

			  Options:
      			--detach-keys string   Override the key sequence for detaching a container
      			--no-stdin             Do not attach STDIN
     			--sig-proxy            Proxy all received signals to the process (default true)

     		 {% codeblock %}
     		 runoob@runoob:~$ docker attach --sig-proxy=false mynginx
				192.168.239.1 - - [10/Jul/2016:16:54:26 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.93 Safari/537.36" "-"
			{% endcodeblock %}	

    build     可以使用Dockerfile来构建镜像
    		  使用:docker build [OPTIONS] PATH | URL | -

			  Options:
      			--build-arg value         设置创建时的变量参数 (default [])
      			--cgroup-parent string    Optional parent cgroup for the container
      			--cpu-period int          限制 CPU CFS周期
      			--cpu-quota int           限制 CPU CFS配额
  				-c, --cpu-shares int       设置 cpu 使用权重；
  				--cpuset-cpus string      指定使用的CPU id
     		    --cpuset-mems string      指定使用的内存 id
                --disable-content-trust   忽略校验，默认开启
  				-f, --file string         指定要使用的Dockerfile路径
      			--force-rm                设置镜像过程中删除中间容器
      			--isolation string        使用容器隔离技术；
      			--label value             设置镜像使用的元数据 (default [])
  				-m, --memory string       设置内存最大值
      			--memory-swap string      设置Swap的最大值为内存+swap，"-1"表示不限swap
      			--no-cache                表示在构建过程中不使用缓存
      			--pull                    尝试去更新镜像的新版本
  				-q, --quiet               安静模式，成功后只输出镜像ID
     			 --rm                     表示构建成功后，移除所有中间容器(default true)
      			--shm-size string         设置/dev/shm的大小，默认值是64M；
  				-t, --tag value               Name and optionally a tag in the 'name:tag' format (default [])
      			--ulimit value            Ulimit options (default [])
      		   例子：	
      			{% codeblock %}
      			 #使用当前目录的Dockerfile创建镜像。
				 docker build -t runoob/ubuntu:v1 . 
				 #使用URL github.com/creack/docker-firefox 的 Dockerfile 创建镜像。
				 docker build github.com/creack/docker-firefox
				{% endcodeblock %}

    commit    将容器的状态保持为镜像
    		  Usage:	docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
			  Options:
               -a, --author string    提交的镜像作者
               -c, --change value     使用Dockerfile指令来创建镜像 (default [])
               -m, --message string   提交时的说明文字
               -p, --pause            在commit时，将容器暂停 (default true)
              例子：
              {% codeblock %}
              runoob@runoob:~$ docker commit -a "runoob.com" -m "my apache" a404c6c174a2  mymysql:v1 
				sha256:37af1236adef1544e8886be23010b66577647a40bc02c0885a6600b33ee28057	
				runoob@runoob:~$ docker images mymysql:v1
				REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
				mymysql             v1                  37af1236adef        15 seconds ago      329 M
			   {% endcodeblock %}
    cp        用于容器与主机之间的数据拷贝
    		  Usage:	docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
						docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH

			  Options:
               -L, --follow-link   保持源目标中的链接
              例子：
              {% codeblock %}
               #将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。
               docker cp /www/runoob 96f7f14e99ab:/www/
               #将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www。
               docker cp /www/runoob 96f7f14e99ab:/www
               #将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中。
               docker cp  96f7f14e99ab:/www /tmp/
              {% endcodeblock %} 

    create    创建一个新的容器
    		  
    		  语法同 docker run

    diff      检查容器里文件结构的更改
    		  Usage:	docker diff CONTAINER

			  例子：
			  {% codeblock %}
			  查看容器mymysql的文件结构更改。
			  runoob@runoob:~$ docker diff mymysql
			  A /logs
			  A /mysql_data
              C /run
              C /run/mysqld
              A /run/mysqld/mysqld.pid
              A /run/mysqld/mysqld.sock
              C /tmp
			 {% endcodeblock %}

    events    从服务器获取实时事件

			  Usage:	docker events [OPTIONS]
			  Options:
               -f, --filter value   根据条件过滤事件 (default [])
               --since string   从指定的时间戳后显示所有事件
               --until string   流水时间显示到指定的时间为止
    exec      在运行的容器中执行命令
    		  Usage:	docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

              -d, --detach         分离模式: 在后台运行
              --detach-keys        Override the key sequence for detaching a container
              -i, --interactive    即使没有(attch)附加也保持STDIN 打开
              --privileged         Give extended privileges to the command
             -t, --tty            分配一个伪终端
             -u, --user           Username or UID (format: <name|uid>[:<group|gid>])

             例子：
             {% codeblock %}
             #在容器mynginx中以交互模式执行容器内/root/runoob.sh脚本
             runoob@runoob:~$ docker exec -it mynginx /bin/sh /root/runoob.sh
			 http://www.runoob.com/
			 #在容器mynginx中开启一个交互模式的终端
			 runoob@runoob:~$ docker exec -i -t  mynginx /bin/bash
			 root@b1a0703e41e7:/#
			 {% endcodeblock %}


    export    将文件系统作为一个tar归档文件导出到STDOUT
    		  Usage:	docker export [OPTIONS] CONTAINER
			   Options:
               -o, --output string   将输入内容写到文件
              例子：
                {% codeblock %}
                
                #为a404c6c174a2的容器按日期保存为tar文件。
				runoob@runoob:~$ docker export -o mysql-`date +%Y%m%d`.tar a404c6c174a2
				runoob@runoob:~$ ls mysql-`date +%Y%m%d`.tar
				mysql-20160711.tar
				
				{% endcodeblock %}


    history   查看看某个镜像的历史版本
			  Usage:	docker history [OPTIONS] IMAGE

			  Options:
                -H, --human     以可读的格式打印镜像大小和日期（default true)
                --no-trunc      显示完整的提交记录
                -q, --quiet      仅列出提交记录ID

    images    查看本机的镜像列表
    		  用法:docker images [OPTIONS] [REPOSITORY[:TAG]]

			  Options:
  				-a, --all             显示所有镜像(默认隐藏中间件镜像)
      			--digests             显示摘要
  				-f, --filter value    基于提供的条件过滤输出 (default []) 
     			--format string       Pretty-print images using a Go template
     			--no-trunc            Don't truncate output
  				-q, --quiet          仅显示id
  			  例子：
  			  {% codeblock %}
  			   docker images  --digests
				REPOSITORY（表示镜像的仓库源）          TAG（镜像的标签）                 DIGEST                                                                    IMAGE ID（镜像ID）            CREATED（镜像创建时间）             SIZE（镜像大小）
				busybox             latest              sha256:a59906e33509d14c036c8678d687bd4eec81ed7c4b8ce907b888c607f6a1e0e6   2b8fd9751c4c        12 weeks ago        
 				docker images  --no-trunc
				REPOSITORY          TAG                 IMAGE ID                                                                  CREATED             SIZE
				busybox             latest              sha256:2b8fd9751c4c0f5dd266fcae00707e67a2545ef34f9a29354585f93dac906749   12 weeks ago        1.093 MB
				docker images
				REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
				busybox             latest              2b8fd9751c4c        12 weeks ago        1.093 MB
			  {% endcodeblock %}	

    import    从归档文件中创建镜像
    		  usage:	docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]

			  Options:
               -c, --change value     应用docker 指令创建镜像； (default [])
               -m, --message string   提交时的说明文字
               例子：
               {% codeblock %}

				#从镜像归档文件my_ubuntu_v3.tar创建镜像，命名为runoob/ubuntu:v4
				runoob@runoob:~$ docker import  my_ubuntu_v3.tar runoob/ubuntu:v4  
				sha256:63ce4a6d6bc3fabb95dbd6c561404a309b7bdfc4e21c1d59fe9fe4299cbfea39
				runoob@runoob:~$ docker images runoob/ubuntu:v4
				REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
				runoob/ubuntu       v4                  63ce4a6d6bc3        20 seconds ago      142.1 MB
			   {% endcodeblock %}

    info      显示 system-wide信息 

    inspect   获取容器/镜像/task的元数据
    		  Usage:	docker inspect [OPTIONS] CONTAINER|IMAGE|TASK [CONTAINER|IMAGE|TASK...]

    		  OPTIONS:
               -f, --format       指定返回值的模板文件
               -s, --size         显示总的文件大小
               --type             为指定类型返回JSON(e.g image, container or task)

    kill      杀掉一个运行中的容器
    		  Usage:	docker kill [OPTIONS] CONTAINER [CONTAINER...]
			  Options:
               -s, --signal string   向容器发送一个信号 (default "KILL")
              例子：
              {% codeblock %}
              	#杀掉运行中的容器mynginx
				runoob@runoob:~$ docker kill -s KILL mynginx
				mynginx
              {% endocdeblock %} 
    load      Load an image from a tar archive or STDIN

    login     登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
    		  Usage:	docker login [OPTIONS] [SERVER]

			  Options:
               -p, --password string   Password
               -u, --username string   Username


    logout    登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

    logs      获取容器的日志
    		  Usage:	docker logs [OPTIONS] CONTAINER

			  Options:
               --details        Show extra details provided to logs
               -f, --follow         跟踪日志输出
               --help           Print usage
               --since string   显示某个开始时间的所有日志
               --tail string    仅列出最新N条容器日志 (default "all")
               -t, --timestamps     显示时间戳

    network   Manage Docker networks

    node      Manage Docker Swarm nodes

    pause     暂停容器中所有的进程
    		 Usage: docker pause [OPTIONS] CONTAINER [CONTAINER...]

    port      列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。
    		  Usage:	docker port CONTAINER [PRIVATE_PORT[/PROTO]]
    		  {% codeblock %}
    		  #查看容器mynginx的端口映射情况。
		       runoob@runoob:~$ docker port mymysql
               3306/tcp -> 0.0.0.0:3306
    		  {% endcodeblock %}
    ps        列出容器
    		  Usage:	docker ps [OPTIONS]

			 Options:
             -a, --all             显示所有的容器，包括未运行的 (default shows just running)
             -f, --filter value    根据条件过滤显示的内容 (default [])
             --format string       指定返回值的模板文件
             -n, --last int        列出最近创建的n个容器 (includes all states) (default -1)
             -l, --latest          显示最近创建的容器 (includes all states)
             --no-trunc            不截断输出
             -q, --quiet           静默模式，只显示容器id
             -s, --size            显示总的文件大小


    pull      从仓库获取所需要的镜像
			  用法：docker pull [OPTIONS] NAME[:TAG|@DIGEST]
			  TAG: 默认为latest
			  Options:
  				-a, --all-tags                下载所有tag镜像
      			--disable-content-trust   忽略校验，默认开启
      		   例子：
      		   {% codeblock %}
      		   	#获取一个镜像	
      		   	docker pull busybox
      		   	#也可以从每个私人或者第三方仓库获取
      		   	docker pull private/busybox:latest
      		   	#可以指定网络
      		   	docker pull xxx.xxx.com:5000/busybox:latest
      		   {% endcodeblock %} 
    push      提交镜像到仓库
    		  用法：docker push [OPTIONS] NAME[:TAG]
    		  Options:
      		  --disable-content-trust   忽略校验，默认开启
              例子：
              {% codeblock %}
              #提交到窗口
              docker push cayley/busybox
              #提交到某个私服
			  docker push xxx.xxx.com:5000/busbox:2016
			  #根据ip提交到私服
			  docker push 111.2.12.11:5000/busbox:2016
			  {% endcodeblock %}

    rename    重命名一个容器
    restart   重新启动一个容器
    		  Usage: docker restart [OPTIONS] CONTAINER [CONTAINER...]
    rm        移除一个或者多个容器
    		  Usage:	docker rm [OPTIONS] CONTAINER [CONTAINER...]
			  Options:
              -f, --force     通过SIGKILL信号强制删除一个运行中的容器 (uses SIGKILL)
              -l, --link      移除容器间的网络连接，而非容器本身
              -v, --volumes   删除与容器关联的卷

    rmi       移除一个或者多个镜像

    run       运行一个新的容器
    		  Usage:	docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

			  Options:
                --add-host value              Add a custom host-to-IP mapping (host:ip) (default [])
                -a, --attach value                Attach to STDIN, STDOUT or STDERR (default [])
                --blkio-weight value          Block IO (relative weight), between 10 and 1000
                --blkio-weight-device value   Block IO weight (relative device weight) (default [])
                --cap-add value               Add Linux capabilities (default [])
                --cap-drop value              Drop Linux capabilities (default [])
                --cgroup-parent string        Optional parent cgroup for the container
                --cidfile string              Write the container ID to the file
                --cpu-percent int             CPU percent (Windows only)
                --cpu-period int              Limit CPU CFS (Completely Fair Scheduler) period
     			--cpu-quota int               Limit CPU CFS (Completely Fair Scheduler) quota
 			    -c, --cpu-shares int              CPU shares (relative weight)
                --cpuset-cpus string          CPUs in which to allow execution (0-3, 0,1)
                --cpuset-mems string          MEMs in which to allow execution (0-3, 0,1)
                -d, --detach                   后台运行容器，并返回容器ID
                --detach-keys string          Override the key sequence for detaching a container
                --device value                Add a host device to the container (default [])
                --device-read-bps value       Limit read rate (bytes per second) from a device (default [])
                --device-read-iops value      Limit read rate (IO per second) from a device (default [])
                --device-write-bps value      Limit write rate (bytes per second) to a device (default [])
                --device-write-iops value     Limit write rate (IO per second) to a device (default [])
                --disable-content-trust       Skip image verification (default true)
                --dns value                   指定容器使用的DNS服务器，默认和宿主一致 (default [])
                --dns-opt value               Set DNS options (default [])
                --dns-search value            指定容器DNS搜索域名，默认和宿主一致 (default [])
                --entrypoint string           Overwrite the default ENTRYPOINT of the image
                -e, --env value               设置环境变量 (default [])
                --env-file value              从指定文件读入环境变量 (default [])
                --expose value                开放一个端口或一组端口 (default [])
                --group-add value             Add additional groups to join (default [])
                --health-cmd string           Command to run to check health
                --health-interval duration    Time between running the check (default 0s)
                --health-retries int          Consecutive failures needed to report unhealthy
                --health-timeout duration     Maximum time to allow one check to run (default 0s)
                -h, --hostname string         指定容器的hostname
                -i, --interactive             以交互模式运行容器，通常与 -t 同时使用
                --io-maxbandwidth string      Maximum IO bandwidth limit for the system drive (Windows only)
                --io-maxiops uint             Maximum IOps limit for the system drive (Windows only)
                --ip string                   Container IPv4 address (e.g. 172.30.100.104)
                --ip6 string                  Container IPv6 address (e.g. 2001:db8::33)
     			--ipc string                  IPC namespace to use
    			--isolation string            Container isolation technology
			    --kernel-memory string        Kernel memory limit
				-l, --label value                 Set meta data on a container (default [])
			    --label-file value            Read in a line delimited file of labels (default [])
			    --link value                  添加链接到另一个容器 (default [])
 			    --link-local-ip value         Container IPv4/IPv6 link-local addresses (default [])
			    --log-driver string           Logging driver for the container
  				--log-opt value               Log driver options (default [])
 				--mac-address string          Container MAC address (e.g. 92:d0:c6:0a:29:33)
 				-m, --memory string            设置容器使用内存最大值
    		    --memory-reservation string   Memory soft limit
                --memory-swap string          Swap limit equal to memory plus swap: '-1' to enable unlimited swap
                --memory-swappiness int       Tune container memory swappiness (0 to 100) (default -1)
                --name string                 为容器指定一个名称
                --network string              指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型； (default "default")
                --network-alias value         Add network-scoped alias for the container (default [])
                --no-healthcheck              Disable any container-specified HEALTHCHECK
                --oom-kill-disable            Disable OOM Killer
                --oom-score-adj int           Tune host's OOM preferences (-1000 to 1000)
                --pid string                  PID namespace to use
                --pids-limit int              Tune container pids limit (set -1 for unlimited)
                --privileged                  Give extended privileges to this container
                -p, --publish value               Publish a container's port(s) to the host (default [])
                -P, --publish-all                 Publish all exposed ports to random ports
                --read-only                   Mount the container's root filesystem as read only
                --restart string              Restart policy to apply when a container exits (default "no")
                --rm                          Automatically remove the container when it exits
                --runtime string              Runtime to use for this container
                --security-opt value          Security Options (default [])
                --shm-size string             Size of /dev/shm, default value is 64MB
                --sig-proxy                   Proxy received signals to the process (default true)
                --stop-signal string          Signal to stop a container, SIGTERM by default (default "SIGTERM")
                --storage-opt value           Storage driver options for the container (default [])
                --sysctl value                Sysctl options (default map[])
                --tmpfs value                 Mount a tmpfs directory (default [])
                -t, --tty                     为容器重新分配一个伪输入终端，通常与 -i 同时使用
                --ulimit value                Ulimit options (default [])
                -u, --user string                 Username or UID (format: <name|uid>[:<group|gid>])
                --userns string               User namespace to use
                --uts string                  UTS namespace to use
                -v, --volume value                Bind mount a volume (default [])
                --volume-driver string        Optional volume driver for the container
                --volumes-from value          Mount volumes from the specified container(s) (default [])
                -w, --workdir string              Working directory inside the container

                例子：
                {% codeblock %}
                 #使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx。
				 docker run --name mynginx -d nginx:latest
				#使用镜像nginx:latest以后台模式启动一个容器,并将容器的80端口映射到主机随机端口。
				docker run -P -d nginx:latest
                #使用镜像nginx:latest以后台模式启动一个容器,将容器的80端口映射到主机的80端口,主机的目录/data映射到容器的/data。
			    docker run -p 80:80 -v /data:/data -d nginx:latest
				#使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。
				runoob@runoob:~$ docker run -it nginx:latest /bin/bash
				root@b8573233d675:/#
               {% endcodeblock %}
               好的参数，要好好研究

    save      将指定镜像保存成 tar 归档文件
    		  Usage: docker save [OPTIONS] IMAGE [IMAGE...]
			  OPTIONS：
              -o :输出到的文件。
              例子：
              {% codeblock %}
              runoob@runoob:~$ docker save -o my_ubuntu_v3.tar runoob/ubuntu:v3
			  runoob@runoob:~$ ll my_ubuntu_v3.tar
               -rw------- 1 runoob runoob 142102016 Jul 11 01:37 my_ubuntu_v3.ta
			  {% endcodeblock %}

    search    从Docker Hub中搜索镜像 
    service   Manage Docker services
    start     启动一个或多少已经被停止的容器
    		  Usage: docker start [OPTIONS] CONTAINER [CONTAINER...]
    stats     Display a live stream of container(s) resource usage statistics

    stop      停止运行一个或者多个容器
    		  Usage: docker stop [OPTIONS] CONTAINER [CONTAINER...]

    swarm     Manage Docker Swarm
    tag       Tag an image into a repository
    top       Display the running processes of a container
    unpause   恢复容器中所有的进程。
    		  Usage: docker unpause [OPTIONS] CONTAINER [CONTAINER...]

    update    Update configuration of one or more containers

    version   Show the Docker version information

    volume    阻塞运行直到容器停止，然后打印出它的退出代码
    		  Useage: docker wait [OPTIONS] CONTAINER [CONTAINER...]

Run 'docker COMMAND --help' for more information on a command.


DockerFile文件：
Dockerfile文件可以使我自动化的创建容器。
Dockerfile包含创建镜像所需要的全部指令。基于在Dockerfile中的指令，我们可以使用Docker build命令来创建镜像。通过减少镜像和容器的创建过程来简化部署。

Dockerfile 基本的语法是

1）使用#来注释

2）所有Dockerfile都必须以FROM命令开始。
FROM 指令告诉 Docker 使用哪个镜像作为基础
Usage: FROM <image name> 
如：
FROM centos

3）主体命令

MAINTAINER：设置该镜像的作者。语法如下：
MAINTAINER <author name>
如：
MAINTAINER "cayley" <cayley@gmail.com>


RUN：在shell或者exec的环境下执行的命令。RUN指令会在新创建的镜像上添加新的层面，接下来提交的结果用在Dockerfile的下一条指令中。语法如下：
RUN 《command》
如：
RUN echo "aaaa"

ADD：复制文件指令。它有两个参数<source>和<destination>。destination是容器内的路径。source可以是URL或者是启动配置上下文中的一个文件。语法如下：
ADD 《src》 《destination》
如：
ADD mongodb-enterprise.repo /etc/yum.repos.d/mongodb-enterprise.repo 

CMD：提供了容器默认的执行命令。 Dockerfile只允许使用一次CMD指令。 使用多个CMD会抵消之前所有的指令，只有最后一个指令生效。 CMD有三种形式：
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
如：
CMD ["/usr/sbin/init"]

EXPOSE：指定容器在运行时监听的端口。语法如下：
EXPOSE <port>;
如：
EXPOSE 27017 15672

ENTRYPOINT：配置给容器一个可执行的命令，这意味着在每次使用镜像创建容器时一个特定的应用程序可以被设置为默认程序。同时也意味着该镜像每次被调用时仅能运行指定的应用。类似于CMD，Docker只允许一个ENTRYPOINT，多个ENTRYPOINT会抵消之前所有的指令，只执行最后的ENTRYPOINT指令。语法如下：
ENTRYPOINT ["executable", "param1","param2"]
ENTRYPOINT command param1 param2

WORKDIR：指定RUN、CMD与ENTRYPOINT命令的工作目录。语法如下：
WORKDIR /path/to/workdir

ENV：设置环境变量。它们使用键值对，增加运行程序的灵活性。语法如下：
ENV <key> <value>
如：
ENV C_FORCE_ROOT true


USER：镜像正在运行时设置一个UID。语法如下：
USER <uid>

VOLUME：授权访问从容器内到主机上的目录。语法如下：
VOLUME ["/data"]
如：
VOLUME [ "/sys/fs/cgroup" ]


