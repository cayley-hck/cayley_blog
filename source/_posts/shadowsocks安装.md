---
title: shadowsocks安装
date: 2016-02-23 16:30:22
tags: 科学上网
categories: liunx
comments: true
---

Shadowsocks是一个轻量级socks5代理，以python写成.当然也有其他语言版本的

1.安装Shadowsocks

1)手动安装:
先安装m2crypto,如果要使用 salsa20 或 chacha20 或 chacha20-ietf 算法，还要安装 libsodium.
同时可以安装git从官方地址(github)下载代码,方便升级. 
{% codeblock %}
yum install m2crypto git libsodium
{% endcodeblock %}
ps:这里安装的python版的,所以OS必须有python环境,目前绝大多数的发行版本的liunx的系统都会预装有.

从github处获取源码:
{% codeblock %}
git clone -b manyuser https://github.com/breakwa11/shadowsocks.git
{% endcodeblock %}
执行完毕后此目录会新建一个shadowsocks目录，其中根目录的是多用户版（即数据库版，个人用户请忽略这个），子目录中的是单用户版(即shadowsocks/shadowsocks)。
根目录即 ./shadowsocks :多用户版本
子目录即 ./shadowsocks/shadowsocks :单用户版本
<!-- more -->

服务端配置:
进入子目录：
cd shadowsocks/shadowsocks
快速运行
{% codeblock %}
python server.py -p 443 -k password -m aes-256-cfb -o http_simple
{% endcodeblock %}
#说明：-p 端口 -k 密码  -m 加密方式 -P 协议插件 -o 混淆插件
如果要后台运行：
{% codeblock %}
python server.py -p 443 -k password -m aes-256-cfb -o http_simple -d start
{% endcodeblock %}
如果要停止/重启：
{% codeblock %}
python server.py -d stop/restart
{% endcodeblock %}
用 -h 查看所有参数

通过配置文件运行

建立配置文件 vi /etc/shadowsocks.json

写入以下内容：
{% codeblock %}
{
    "server": "0.0.0.0", //服务器地址,客户端配置的时候使用,服务端配置时无需修改
    "server_ipv6": "::", //服务器的ipv6地址,客户端配置的时候使用,服务端配置时无需修改
    "server_port": 8388, //服务器监听的地址
    "local_address": "127.0.0.1", //本地监听地址
    "local_port": 1080,	//本地监听端口
    "password": "mypassword",//密码
    "timeout": 120,//超时时间
    "method": "aes-256-cfb", //加密方式,默认aes-256-cfb
    "protocol": "auth_sha1_compatible", //协议插件，默认"origin"
    "protocol_param": "", //协议插件参数，默认""
    "obfs": "tls1.0_session_auth_compatible", //混淆插件，默认"tls1.0_session_auth_compatible"
    "obfs_param": "",  //混淆插件参数，默认""
    "redirect": "", //重定向参数，默认""
    "dns_ipv6": false, //是否优先使用IPv6地址，有IPv6时可开启
    "fast_open": false, //true / false	快速打开(仅限linux客户端)
    "workers": 1   //线程（仅限linux客户端）
}
{% endcodeblock %}

其中protocol有如下取值：

protocol	说明
"origin"	原版协议
"verify_simple"	带校验的协议
"verify_deflate"	带压缩的协议
"verify_sha1"	带验证抗CCA攻击的协议，可兼容libev的OTA
"auth_simple"	抗重放攻击的协议
"auth_sha1"	带验证抗CCA攻击且抗重放攻击的协议
其中obfs有如下取值：

obfs	说明
"plain"	不混淆
"http_simple"	伪装为http协议
"tls_simple"	伪装为tls协议（不建议使用）
"random_head"	发送一个随机包再通讯的协议
"tls1.0_session_auth"	伪装为tls session握手协议，同时能抗重放攻击


各混淆插件的说明请点击这里查看：{% link 混淆插件说明  https://github.com/breakwa11/shadowsocks-rss/wiki/obfs %}

注：客户端的protocol和obfs配置必须与服务端的一致。

redirect参数说明：

值为空字符串或一个列表，若为列表示例如
"redirect":["bing.com", "cloudflare.com:443"],
作用是在连接方的数据不正确的时候，把数据重定向到列表中的其中一个地址和端口（不写端口则视为80），以伪装为目标服务器。

dns_ipv6参数说明：

为true则指定服务器优先使用IPv6地址。仅当服务器能访问IPv6地址时可以用，否则会导致有IPv6地址的网站无法打开。

一般情况下，只需要修改以下五项即可：

"server_port":8388,        //端口
"password":"password",     //密码
"protocol":"origin",       //协议插件
"obfs":"http_simple",      //混淆插件
"method":"aes-256-cfb",    //加密方式


2)脚本自动安装

自动安装shell脚本:

{% link 所有版本一建安装脚本 https://github.com/teddysun/shadowsocks_install %}

{% codeblock %}

下面是自动安装脚本:

#! /bin/bash
# Check OS
function checkos(){
    if [ -f /etc/redhat-release ];then
        OS=CentOS
    else
        echo "Not support OS, Please  run  CentOS install "
        exit 1
    fi
}

# Config shadowsocks
function config_shadowsocks(){
	echo "Please input password for shadowsocks-python:"
    read -p "(Default password: teddysun.com):" shadowsockspwd
    [ -z "$shadowsockspwd" ] && shadowsockspwd="teddysun.com"
    echo ""
    echo "---------------------------"
    echo "password = $shadowsockspwd"
    echo "---------------------------"
    echo ""
    # Set shadowsocks config port
    while true
    do
    echo -e "Please input port for shadowsocks-python [1-65535]:"
    read -p "(Default port: 8989):" shadowsocksport
    [ -z "$shadowsocksport" ] && shadowsocksport="8989"
    expr $shadowsocksport + 0 &>/dev/null
    if [ $? -eq 0 ]; then
        if [ $shadowsocksport -ge 1 ] && [ $shadowsocksport -le 65535 ]; then
            echo ""
            echo "---------------------------"
            echo "port = $shadowsocksport"
            echo "---------------------------"
            echo ""
            break
        else
            echo "Input error! Please input correct numbers."
        fi
    else
        echo "Input error! Please input correct numbers."
    fi
    done
    cat > /etc/shadowsocks.json<<-EOF
{
    "server": "0.0.0.0",
    "server_ipv6": "::",
    "server_port": ${shadowsocksport},
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "${shadowsockspwd}",
    "timeout": 120,
    "method": "aes-256-cfb",
    "protocol": "auth_sha1_compatible",
    "protocol_param": "",
    "obfs": "tls1.0_session_auth_compatible",
    "obfs_param": "",
    "redirect": "",
    "dns_ipv6": false,
    "fast_open": false,
    "workers": 1
}
EOF

# Get IP address
    echo "Getting Public IP address, Please wait a moment..."
    IP=$(curl -s -4 icanhazip.com)
    if [[ "$IP" = "" ]]; then
        IP=$(curl -s -4 ipinfo.io/ip)
    fi
    echo -e "Your main public IP is\t\033[32m$IP\033[0m"
    echo ""
    #Current folder
    cur_dir=`pwd`
    cd $cur_dir

}


# iptables set
function iptables_set(){
    echo "iptables start setting..."
    /etc/init.d/iptables status 1>/dev/null 2>&1
    if [ $? -eq 0 ]; then
        /etc/init.d/iptables status | grep '${shadowsocksport}' | grep 'ACCEPT' >/dev/null 2>&1
        if [ $? -ne 0 ]; then
            /sbin/iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport ${shadowsocksport} -j ACCEPT
            /etc/init.d/iptables save
            /etc/init.d/iptables restart
        else
            echo "port ${shadowsocksport} has been set up."
        fi
    else
        echo "iptables looks like shutdown, please manually set it if necessary."
    fi
}

# Uninstall Shadowsocks
function uninstall(){
    printf "Are you sure uninstall Shadowsocks? (y/n) "
    printf "\n"
    read -p "(Default: n):" answer
    if [ -z $answer ]; then
        answer="n"
    fi
    if [ "$answer" = "y" ]; then
        ps -ef | grep -v grep | grep -v ps | grep -i "ssserver" > /dev/null 2>&1
        if [ $? -eq 0 ]; then
            /etc/init.d/shadowsocks stop
        fi
        # delete config file
        rm -f /etc/shadowsocks.json
        rm -f /var/run/shadowsocks.pid
        rm -f /etc/init.d/shadowsocks
        rm -rf /usr/local/shadowsocks
        chkconfig --del shadowsocks
        if [ $? -eq 0 ]; then
            echo "Shadowsocks uninstall success!"
        else
            echo "Shadowsocks uninstall failed!"
        fi
    else
        echo "uninstall cancelled, Nothing to do"
    fi
}


function init_d(){
	    cat > /etc/init.d/shadowsocks<<EOF
#!/bin/sh
# chkconfig: 2345 90 10
# description: Start or stop the Shadowsocks server
#
 
name=shadowsocks
BIN=/usr/local/shadowsocks/shadowsocks/server.py
conf=/etc/shadowsocks.json
 
start(){
    python \$BIN -c \$conf -d start
    RETVAL=\$?
    if [ "\$RETVAL" = "0" ]; then
        echo "\$name start success"
    else
        echo "\$name start failed"
    fi
}
 
stop(){
    pid=\`ps -ef | grep -v grep | grep -v ps | grep -i "\${BIN}" | awk '{print \$2}'\`
    if [ ! -z \$pid ]; then
        python \$BIN -c \$conf -d stop
        RETVAL=\$?
        if [ "\$RETVAL" = "0" ]; then
            echo "\$name stop success"
        else
            echo "\$name stop failed"
        fi
    else
        echo "\$name is not running"
        RETVAL=1
    fi
}
 
status(){
    pid=\`ps -ef | grep -v grep | grep -v ps | grep -i "\${BIN}" | awk '{print \$2}'\`
    if [ -z \$pid ]; then
        echo "\$name is not running"
        RETVAL=1
    else
        echo "\$name is running with PID \$pid"
        RETVAL=0
    fi
}
 
case "\$1" in
'start')
    start
    ;;
'stop')
    stop
    ;;
'status')
    status
    ;;
'restart')
    stop
    start
    RETVAL=\$?
    ;;
*)
    echo "Usage: \$0 { start | stop | restart | status }"
    RETVAL=1
    ;;
esac
exit \$RETVAL
EOF

}

function install(){
	cd /usr/local
	yum install -y m2crypto git libsodium

	#wget https://github.com/jedisct1/libsodium/releases/download/1.0.8/libsodium-1.0.8.tar.gz
	#tar xf libsodium-1.0.8.tar.gz && cd libsodium-1.0.8
	#./configure && make -j2 && make install
	#ldconfig

	if [ -d /usr/local/shadowsocks ]; then
		echo "shadowsocks already exists and is not an empty directory"
		exit 1
	fi

	git clone -b manyuser https://github.com/breakwa11/shadowsocks.git

	cd /usr/local/shadowsocks/shadowsocks

	if [ -d /usr/local/shadowsocks ]; then
		    init_d
            # Add run on system start up
            if [ -f  /etc/init.d/shadowsocks ]; then
            	chmod +x /etc/init.d/shadowsocks
                chkconfig --add shadowsocks
                chkconfig shadowsocks on
            fi
            # Run shadowsocks in the background
            /etc/init.d/shadowsocks start
        else
            echo ""
            echo "Shadowsocks install failed! Please visit https://teddysun.com/342.html and contact."
            exit 1
        fi
        clear
        echo ""
        echo "Congratulations, shadowsocks install completed!"
        echo -e "Your Server IP: \033[41;37m ${IP} \033[0m"
        echo -e "Your Server Port: \033[41;37m ${shadowsocksport} \033[0m"
        echo -e "Your Password: \033[41;37m ${shadowsockspwd} \033[0m"
        echo -e "Your Local IP: \033[41;37m 127.0.0.1 \033[0m"
        echo -e "Your Local Port: \033[41;37m 1080 \033[0m"
        echo -e "Your Encryption Method: \033[41;37m aes-256-cfb \033[0m"
        echo ""
        echo "Welcome to visit:https://teddysun.com/342.html"
        echo "Enjoy it!"
        echo ""
        exit 0

}

# Initialization step
action=$1
[  -z $1 ] && action=install
case "$action" in
install)
    config_shadowsocks
    install
    ;;
uninstall)
    uninstall
    ;;
*)
    echo "Arguments error! [${action} ]"
    echo "Usage: `basename $0` {install|uninstall}"
    ;;
esac

{% endcodeblock %}