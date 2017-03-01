---
title: yum源的配置和使用
date: 2016-02-22 10:34:39
tags: yum
categories: liunx
comments: true
---
1.yum?
Yum（Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中基于RPM包的软件包管理器。能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，自动安装软件.
yum 的主要功能是自动化地升级，安装/移除rpm包，收集rpm 包的相关信息，检查依赖性并自动提示用户解决.

yum 主要要有可靠的软件的仓库(repository),它可以是http 或ftp 站点，也可以是本地软件池，但必须包含rpm 的header，header 包括了rpm 包的各种信息，包括描述，功能，提供的文件，依赖性等。正是收集了这些header 并加以分析，才能自动化地完成余下的任务。

yum 可以同时配置多个资源库(Repository)，简洁的配置文件（/etc/yum.conf），自动解决增加或删除rpm 包时遇到的依赖性问题，保持与RPM 数据库的一致性.

查看本机安装的yum:

{% codeblock %}
[root@localhost ~]# rpm -qa|grep yum
yum-3.4.3-132.el7.centos.0.1.noarch  #yum主程序
yum-plugin-fastestmirror-1.1.31-34.el7.noarch #快速从资源库中查找bao的yum 插件
yum-metadata-parser-1.1.4-10.el7.x86_64 #分析 metadata
{% endcodeblock %}

centos 默认安装有yum,如果liunx没有安装yum,可以通过下载rpm包.
{% codeblock %}
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm 
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm 
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.29-40.el6.centos.noarch.rpm  
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-14.el6.noarch.rpm

# rpm -ivh yum-3.2.22-33.el5.centos.noarch.rpm yum-fastestmirror-1.1.16-14.el5.centos.1.noarch.rpm yum-metadata-parser-1.1.2-3.el5.centos.i386.rpm
{% endcodeblock %}

2.配置

yum 的配置文件分为两部分：main和repository

main 定义了全局配置选项，整个yum 配置文件应该只有一个main。常位于/etc/yum.conf 中。
repository 部分定义了每个源/服务器的具体配置，可以有一到多个。常位于/etc/yum.repo.d 目录下的各文件中。

1)yum.conf

{% codeblock %}
[main]
#yum的缓存目录.yum下载的rpm版保存的位置 ,默认/var/cache/yum
cachedir=/var/cache/yum/$basearch/$releasever
#安装后是否保存软件包,0(默认),不保存 1 保存
keepcache=0
#debug信息输出等级, 范围0-10 ,默认2
debuglevel=2
#日志文件位置
logfile=/var/log/yum.log
# 1 yum只安装和系统架构匹配的软件包 0 则相反
exactarch=1
# 1 允许更新陈旧软件包
obsoletes=1
#是否进行gpg校验，如果没有这一项，默认是1。
gpgcheck=1
#是否启用插件，默认1为允许，0表示不允许。我们一般会用yum-fastestmirror这个插件。
plugins=1
installonly_limit=5
bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
#指定一个软件包，yum 会根据这个包判断你的发行版本，默认是redhat-release，也可以是安装的任何针对自己发行版的rpm 包。
distroverpkg=centos-release
#包的策略。一共有两个选项，newest 和last，这个作用是如果你设置了多个repository，而同一软件在不同的repository 中同时存在，
#yum 应该安装哪一个，如果是newest，则yum 会安装最新的那个版本。如果是last，则yum 会将服务器id 以字母表排序，并选择最后的那个服务器上的软件安装。一般都是选newest。
pkgpolicy=newest
#有1和0两个选项，表示yum 是否容忍命令行发生与软件包有关的错误，比如你要安装1,2,3三个包，而其中3此前已经安装了，如果你设为1,则yum 不会出现错误信息。默认是0
tolerant=1
#exclude 排除某些软件在升级名单之外，可以用通配符，列表中各个项目要用空格隔开，这个对于安装了诸如美化包，中文补丁的朋友特别有用。
#exclude=xxx
#设置 keepcache=1，yum 在成功安装软件包之后保留缓存的头文件 (headers) 和软件包。默认值为 keepcache=0 不保存
#keepcache=[1 or 0]

#  This is the default, if you make this bigger yum won't see if the metadata
# is newer on the remote and so you'll "gain" the bandwidth of not having to
# download the new metadata and "pay" for it by yum not having correct
# information.
#  It is esp. important, to have correct metadata, for distributions like
# Fedora which don't keep old packages around. If you don't like this checking
# interupting your command line usage, it's much better to have something
# manually check the metadata once an hour (yum-updatesd will do this).
# metadata_expire=90m

# PUT YOUR REPOS HERE OR IN separate files named file.repo
# in /etc/yum.repos.d
#默认都会被include 进来 也就是说 /etc/yum.repos.d/xx.repo 无论配置文件有多少个 每个里面有多少个[name] 最后其实都被整合到 一个里面看就是了 重复的[name]后面的覆盖前面的
reposdir=/etc/yum.repos.d/

{% endcodeblock %}

2)rpo配置

yum可以配置多个资源库,可以在/etc/yum.repos.d/新增和修改,也可以在/etc/yum.conf直接配置.
格式:
[serverid] #唯一
name=Some name for this server
baseurl=url://path/to/repository/
serverid 是用于区别各个不同的repository，必须有一个独一无二的名称；
name 是对repository 的描述，支持像$releasever $basearch这样的变量；
baseurl 是服务器设置中最重要的部分，只有设置正确，才能从上面获取软件。url 支持的协议有 http:// ftp:// file:// 


例如下面:
{% codeblock %}
[centosplus]
#支持$releasever(发行版本) $basearch(cpu的基本体系组) $arch(cpu体系)这样的变量
name=CentOS-$releasever - Plus - 163.com
baseurl=http://mirrors.163.com/centos/$releasever/centosplus/$basearch/
使用gpg对包进行校验，确保下载包的完整性
gpgcheck=1
##当某个软件仓库被配置成 enabled=0 时，yum 在安装或升级软件包时不会将该仓库做为软件包提供源。使用这个选项，可以启用或禁用软件仓库。
#通过 yum 的 --enablerepo=[repo_name] 和 --disablerepo=[repo_name] 选项，或者通过 PackageKit 的"添加/删除软件"工具，也能够方便地启用和禁用指定的软件仓库
enabled=0
#确保下载包的完整性，可以指定repository站点的gpg key.
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7
{% endcodeblock %}

还有注意的是,如果要使用gpg对包进行校验,还需要导入每个reposity的GPG,先到repo库站点下载,然后用
rpm --import xxx.txt 命令将它们导入.
最好把发行版自带GPG-KEY也导入，rpm --import /usr/share/doc/redhat-release-*/RPM-GPG-KEY 官方软件升级用的上.

3)更换yum源
有时候centos的自动的yum地址访问不了,或者下载速度太慢,可以换为国内的地址:
如换为网易163 yum源:

推荐先备份/etc/yum.repos.d/CentOS-Base.repo
{% codeblock %}
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
{% endcodeblock %}

然后修改/etc/yum.repos.d/CentOS-Base.repo文件
{% codeblock %}
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
#
#
[base]
name=CentOS-$releasever - Base - 163.com
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
baseurl=http://mirrors.163.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates - 163.com
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates
baseurl=http://mirrors.163.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras - 163.com
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
baseurl=http://mirrors.163.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus - 163.com
baseurl=http://mirrors.163.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7
{% endcodeblock %}

最后清除旧缓存,生产新缓存
{% codeblock %}
yum clean all
yum makecache
{% endcodeblock %}

更换为阿里云yum源:
有时候服务器使用的阿里云的话,换为阿里云的yum源会比较快.
阿里云Linux安装镜像源地址：http://mirrors.aliyun.com/
{% codeblock %}
#备份原镜像文件(老规矩)
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
#下载新的CentOS-Base.repo 到/etc/yum.repos.d/
#CentOS 5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
#CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
#CentOS 7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
#生成缓存
yum clean all
yum makecache
{% endcodeblock %}

当然国内还有其他的yum源

3.yum命令使用

Usage: yum [options] COMMAND

List of Commands:
检查问题
check          Check for problems in the rpmdb
检查可更新的程序
check-update   Check for available package updates
清除缓存
clean          Remove cached data
yum clean packages 清除缓存目录下的软件包
yum clean headers 清除缓存目录下的 headers
yum clean oldheaders 清除缓存目录下旧的 headers
yum clean, yum clean all (= yum clean packages; yum clean oldheaders) 清除缓存目录下的软件包及旧的headers

查看包的依赖列表
deplist        List a package's dependencies

distribution-synchronization Synchronize installed packages to the latest available versions
downgrade      downgrade a package
erase          Remove a package or packages from your system
fs             Creates filesystem snapshots, or lists/deletes current snapshots.
fssnapshot     Creates filesystem snapshots, or lists/deletes current snapshots.
groups         Display, or use, the groups information
显示帮助信息
help           Display a helpful usage message
显示使用历史
history        Display, or use, the transaction history
显示包信息
info           Display details about a package or group of packages
安装指定包
install        Install a package or packages on your system
列出所有可安裝的软件清单
list           List a package or groups of packages
load-transaction load a saved transaction from filename
生产缓存
makecache      Generate the metadata cache
provides       Find what package provides the given value
删除指定的rpm软件包
remove
重新安装
reinstall      reinstall a package
repo-pkgs      Treat a repo. as a group of packages, so we can install/remove all of them
repolist       Display the configured software repositories
搜索软件包
search         Search package details for the given string
进入yum的shell提示符
shell          Run an interactive yum shell
swap           Simple way to swap packages, instead of using shell
全部更新 
update         Update a package or packages on your system
update-minimal Works like upgrade, but goes to the 'newest' package match which fixes a problem that affects your system
yum update 全部更新
yum update package1 更新指定程序包package1

updateinfo     Acts on repository update information
升级指定程序包
upgrade        Update packages taking obsoletes into account
显示yum版本信息
version        Display a version for the machine and/or available repos.


Options:
  -h, --help            show this help message and exit  显示帮助信息
  -t, --tolerant        be tolerant of errors 包错误是否执行
  -C, --cacheonly       run entirely from system cache, don't update cache  缓存安装,完全从缓存中运行，而不去下载或者更新任何头文件。

  -c [config file], --config=[config file]  指定配置文件
                        config file location
  -R [minutes], --randomwait=[minutes]   设置yum处理一个命令的最大等待之间
                        maximum command wait time
  -d [debug level], --debuglevel=[debug level]  设置调试等级(0-10)
                        debugging output level
  --showduplicates      show duplicates, in repos, in list/search commands
  -e [error level], --errorlevel=[error level] 设置错误等级(0-10)
                        error output level
  --rpmverbosity=[debug level name]
                        debugging output level for rpm
  -q, --quiet           quiet operation 安装模式,不显示信息
  -v, --verbose         verbose operation 显示操作信息
  -y, --assumeyes       answer yes for all questions 允许安装
  --assumeno            answer no for all questions
  --version             show Yum version and exit
  --installroot=[path]  set install root
  --enablerepo=[repo]   enable one or more repositories (wildcards allowed)
  --disablerepo=[repo]  disable one or more repositories (wildcards allowed)
  -x [package], --exclude=[package]
                        exclude package(s) by name or glob
  --disableexcludes=[repo]
                        disable exclude from main, for a repo or for
                        everything
  --disableincludes=[repo]
                        disable includepkgs for a repo or for everything
  --obsoletes           enable obsoletes processing during updates
  --noplugins           disable Yum plugins  禁止使用插件
  --nogpgcheck          disable gpg signature checking  不进行gpg检查
  --disableplugin=[plugin]    根据插件名称禁止使用插件
                        disable plugins by name
  --enableplugin=[plugin]	  启用某个插件
                        enable plugins by name
  --skip-broken         skip packages with depsolving problems
  --color=COLOR         control whether color is used
  --releasever=RELEASEVER
                        set value of $releasever in yum config and repo files
  --downloadonly        don't update, just download
  --downloaddir=DLDIR   specifies an alternate directory to store packages
  --setopt=SETOPTS      set arbitrary config and repo options
  --bugfix              Include bugfix relevant packages, in updates
  --security            Include security relevant packages, in updates
  --advisory=ADVS, --advisories=ADVS
                        Include packages needed to fix the given advisory, in
                        updates
  --bzs=BZS             Include packages needed to fix the given BZ, in
                        updates
  --cves=CVES           Include packages needed to fix the given CVE, in
                        updates
  --sec-severity=SEVS, --secseverity=SEVS
                        Include security relevant packages matching the
                        severity, in updates


参考:
{% link 163CentOS镜像使用帮助 http://mirrors.163.com/.help/centos.html %}
{% link rpm命令 http://man.linuxde.net/rpm %}
{% link yum命令 http://man.linuxde.net/yum %}

