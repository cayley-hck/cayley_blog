---
title: openwrt上的uhttp的使用与配置
date: 2016-08-04 21:21:10
tags:
---

uHTTPd 是一个 OpenWrt/LUCI 开发者从头编写的 Web 服务器。 它着力于实现一个稳定高效的服务器，能够满足嵌入式设备的轻量级任务需求，且能够与 OpenWrt 的配置框架 (UCI) 整合。默认情况下它被用于 OpenWrt 的 Web 管理接口 LuCI。
uHTTPd 也能提供一个常规 Web 服务器所需要的所有功能。包括 TLS (SSL)、CGI 以及 Lua。uHTTPd 是单线程的，但是支持多个实例 (例如，支持监听多个端口，每个端口都可以使用独立的文档根目录以及其他功能)。与其他很多 Web 服务器相比， uHTTPd 支持进程内执行 Lua，可以提高 Lua CGI 脚本的执行效率(官方说法)

安装:
{% codeblock %}
opkg update
opkg install uhttpd
{% endcodeblock %}

使用:
{% codeblock %}
[root@PandoraBox:/data/codebase/owncloud]#/etc/init.d/uhttpd -h
Syntax: /etc/init.d/uhttpd [command]

Available commands:
	start	Start the service
	stop	Stop the service
	restart	Restart the service
	reload	Reload configuration files (or restart if that fails)
	enable	Enable service autostart
	disable	Disable service autostart
{% endcodeblock %}

配置:

config 'uhttpd' 'main'
        option 'listen_http' '80'
        option 'home'        '/www'


项:
listen_http	  		设置http   ip地址&端口   0.0.0.0:88'   [::]:80 
listen_https  		设置TLS,https ip地址&端口   0.0.0.0:443'   [::]:433 
home 		  		设置网站根目录  默认值: /www 
cert          		设置listen_https项后,启用https,需要设置crt证书文件 	默认值:/etc/uhttpd.crt
key			  		设置listen_https项后,启用https,需要设置key文件 	    默认值:/etc/uhttpd.key
cgi_prefix    		定义cgi脚本的前缀 ,相对于完整根目录  默认:/cgi-bin
lua_prefix    		定义lua脚本的前缀
lua_handler   		定义lua脚本的handler
script_timeout		cgi脚本超时时间, 默认60
network_timeout		网络请求超时时间, 默认30
realm 				Basic authentication realm when prompting the client for credentials (HTTP 400)  local hostname
Config      		配置文件
index_file			主页文件,默认加载的文件 index.html, index.htm, default.html, default.htm
index_page			主页,默认index.html
error_page			错误页
no_symlinks			默认0
no_dirlists			是否可以浏览文件夹列表,默认 0
http_keepalive		http保持连接 ,可复用连接数,默认30
max_requests		最大请求数,默认3
max_connections		最大连接数, 默认100


配置文件路径:/etc/config/uhttp

使用php的配置:
{% codeblock %}
 list interpreter ".php=/usr/bin/php-cgi"
 {% endcodeblock %}

使用lua(源自官网):
{% codeblock %}
root@OpenWrt:~# opkg install uhttpd-mod-lua
Installing uhttpd-mod-lua (18) to root...
Downloading http://downloads.openwrt.org/snapshots/trunk/ar71xx/packages/uhttpd-mod-lua_18_ar71xx.ipk.
Configuring uhttpd-mod-lua.
root@OpenWrt:~# uci set uhttpd.main.lua_prefix=/lua
root@OpenWrt:~# uci set uhttpd.main.lua_handler=/root/test.lua
root@OpenWrt:~# cat /root/test.lua
function handle_request(env)
        uhttpd.send("Status: 200 OK\r\n")
        uhttpd.send("Content-Type: text/plain\r\n\r\n")
        uhttpd.send("Hello world.\n")
end
root@OpenWrt:~# /etc/init.d/uhttpd restart
root@OpenWrt:~# wget -qO- http://127.0.0.1/lua/
Hello world.
root@OpenWrt:~#
{% endcodeblock %}

参考:

{% link https://wiki.openwrt.org/doc/howto/owncloud %}
