---
title: ssh秘钥登陆
date: 2016-02-19 11:48:06
tags: https
categories: liunx
comments: true
---

ssh 是一个专为远程登录会话和其他网络服务提供安全性的协议。默认状态下ssh链接是需要密码认证的，可以通过添加系统认证（即公钥-私钥）的修改，修改后系统间切换可以避免密码输入和ssh认证

1.用ssh-keygen创建公钥

{% codeblock %}
#进入目录
$cd ~./ssh   

#生成ssh key,如果不想输入密码,可以将密码设置为空(直接回车就行)
huangchkaideAir:.ssh kai$ ssh-keygen -t rsa -C "hck920927@qq.com"
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in github_rsa.
Your public key has been saved in github_rsa.pub.
The key fingerprint is:
4d:4c:51:fa:07:c6:49:b6:a4:1c:36:e8:a3:9b:6b:bb hck920927@qq.com
The key's randomart image is:
+--[ RSA 2048]----+
|         .*o=    |
|        .= X o   |
|       .  * *    |
|        oo o .   |
|       .S.. . .  |
|      .      .   |
|       o         |
|      +          |
|     .E+         |
+-----------------+
huangchkaideAir:.ssh kai$ ls
github_rsa	github_rsa.pub	id_rsa	id_rsa.pub	known_hosts

{% endcodeblock %}

加入SSH Agent
ssh-agent 是专为既令人愉快又安全的处理 RSA 和 DSA 密钥而设计的特殊程序， ssh-agent 是个长时间持续运行的守护进程（daemon），设计它的唯一目的就是对解密的专用密钥进行高速缓存。
运行ssh-agent，它会打印出来它使用的 ssh 的环境和变量。要使用这些变量，有两种方法，一种是手动进行声明环境变量，另一种是运行eval命令自动声明环境变量。
方法一：手动声明环境变量
{% codeblock %}
huangchkaideAir:# SSH_AUTH_SOCK=/tmp/ssh-vEGjCM2147/agent.2147; export SSH_AUTH_SOCK;
huangchkaideAir:# SSH_AGENT_PID=2148; export SSH_AGENT_PID;
huangchkaideAir:# printenv | grep SSH     #检查 ssh 环境变量是否已经加入当前会话的环境变量
SSH_AGENT_PID=2148
SSH_AUTH_SOCK=/tmp/ssh-vEGjCM2147/agent.2147
 {% endcodeblock %}
方法二：运行eval命令自动声明环境变量
{% codeblock %}
huangchkaideAir:#  eval `ssh-agent`
Agent pid 2157
huangchkaideAir:#  printenv | grep SSH     #检查 ssh 环境变量是否已经加入当前会话的环境变量
SSH_AGENT_PID=2148
SSH_AUTH_SOCK=/tmp/ssh-vEGjCM2147/agent.2147 
{% endcodeblock %}
所以加入SSH Agent的步骤如下:
输入：
{% codeblock %}
huangchkaideAir:.ssh kai$ ssh-agent -s
SSH_AUTH_SOCK=/var/folders/3w/ygb7_1m10f98xfnwgh86939c0000gn/T//ssh-lGyzWDf7xrpC/agent.1537; export SSH_AUTH_SOCK;
SSH_AGENT_PID=1538; export SSH_AGENT_PID;
echo Agent pid 1538;
{% endcodeblock %}

如果没有显示上面所示的结果的话,就输入：
{% codeblock %}
eval `ssh-agent -s`
{% endcodeblock %}


直到出现上面的结果后再输入：
{% codeblock %}
huangchkaideAir:.ssh kai$ ssh-add ~/.ssh/id_rsa
Identity added: /Users/kai/.ssh/id_rsa (/Users/kai/.ssh/id_rsa)
{% endcodeblock %}
这样，你成功的在本地生成了一个可用的SSH key

2)上传公钥到linux服务器

登陆服务器，进入需要远程登陆的用户目录(~)，把公钥放到用户目录的 .ssh 这个目录下
如果目录不存在，需要创建~/.ssh目录，并把目录权限设置为700,
并将把公钥改名为authorized_keys，并且把它的用户权限设成600.

{% codeblock %}
[root@localhost ~]# mkdir ~/.ssh    
[root@localhost ~]# chmod 700 .ssh 
[root@localhost ~]# cd .ssh/
[root@localhost .ssh]#  rz
rz waiting to receive.
Starting zmodem transfer.  Press Ctrl+C to cancel.
Transferring id_rsa.pub...
  100%     398 bytes  398 bytes/sec 00:00:01       0 Errors  
[root@localhost .ssh]# ls
id_rsa.pub
[root@localhost .ssh]# mv id_rsa.pub authorized_keys
[root@localhost .ssh]# ls
authorized_keys
[root@localhost .ssh]# chmod 600 authorized_keys
{% endcodeblock %}

3)这里设置密码短语为空,所以 ssh-add ~/.ssh/id_rsa的不用输入密码.
如果设置ssh-keygen -t rsa -C "hck920927@qq.com”的时候设置密码.
需要在ssh-add ~/.ssh/id_rsa的时候输入密码:
{% codeblock %}
[root@server ~]# ssh-add ~/.ssh/id_dsa
Enter passphrase for /home/user/.ssh/id_dsa:     #输入你的密码短语
Identity added: /home/user/.ssh/id_dsa (/home/user/.ssh/id_dsa) 
{% endcodeblock %}

还可以查看和清除:
{% codeblock %}
huangchkaideAir:.ssh kai$ ssh-add -l
2048 4d:4c:51:fa:07:c6:49:b6:a4:1c:36:e8:a3:9b:6b:bb /Users/kai/.ssh/id_rsa (RSA)
2048 4c:82:57:82:65:61:1f:de:07:88:18:f0:d7:7f:dc:45 /Users/kai/.ssh/id_rsa (RSA)
{% endcodeblock%}

{% codeblock %}

[root@server ~]# ssh-agent -k
unset SSH_AUTH_SOCK;
unset SSH_AGENT_PID;
echo Agent pid 2148 killed;
{% endcodeblock %}


