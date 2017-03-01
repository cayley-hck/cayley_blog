---
title: hexo博客搭建
date: 2016-02-18 12:15:15
tags: hexo
categories: nodejs
comments: true

---

hexo博客搭建:
什么hexo?
用官方的话说是:
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页

1.安装node.js:
1)使用nvm(一种node版本管理器)安装:
cURL:
{% codeblock %}
$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh
{% endcodeblock %}

Wget:
{% codeblock %}
$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
{% endcodeblock %}

安装完成后，重启终端并执行下列命令即可安装 Node.js。
{% codeblock %}
$ nvm install 4
{% endcodeblock %}

2)可以使用yum等rpm包管理工具安装

3)使用源码安装
{% codeblock %}
wget 源码地址
tar xvf node-v4.2.tar.gz 
 cd node-v4.2 
 ./configure 
make 
make install
{% endcodeblock %}
2.安装hexo:
前提条件: 已安装node.js   git

安装方法: 
{% codeblock %}
 npm install -g hexo-cli
 {% endcodeblock %}

建立新blogs项目:
{% codeblock %}
$ hexo init <folder>
$ cd <folder>
$ npm install
{% endcodeblock %}
文件结构:
.
├── _config.yml :配置文件
├── package.json :包信息
├── scaffolds :模板文件夹,文章模板信息
├── source : 资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去
|   ├── _drafts
|   └── _posts
└── themes :主题文件夹

设置配置文件_config.yml
网站
参数  描述
title 网站标题
subtitle  网站副标题
description 网站描述
author  您的名字
language  网站使用的语言
timezone  网站时区。Hexo 默认使用您电脑的时区。时区列表。比如说：America/New_York, Japan, 和 UTC 。
网址
参数  描述  默认值
url 网址  
root  网站根目录 
permalink 文章的 永久链接 格式 :year/:month/:day/:title/
permalink_default 永久链接中各部分的默认值  
网站存放在子目录
如果您的网站存放在子目录中，例如 http://yoursite.com/blog，则请将您的 url 设为 http://yoursite.com/blog 并把 root 设为 /blog/。

目录
参数  描述  默认值
source_dir  资源文件夹，这个文件夹用来存放内容。  source
public_dir  公共文件夹，这个文件夹用于存放生成的站点文件。 public
tag_dir 标签文件夹 tags
archive_dir 归档文件夹 archives
category_dir  分类文件夹 categories
code_dir  Include code 文件夹  downloads/code
i18n_dir  国际化（i18n）文件夹  :lang
skip_render 跳过指定文件的渲染，您可使用 glob 表达式来匹配路径。 
文章
参数  描述  默认值
new_post_name 新文章的文件名称  :title.md
default_layout  预设布局  post
auto_spacing  在中文和英文之间加入空格  false
titlecase 把标题转换为 title case false
external_link 在新标签中打开链接 true
filename_case 把文件名称转换为 (1) 小写或 (2) 大写 0
render_drafts 显示草稿  false
post_asset_folder 启动 Asset 文件夹  false
relative_link 把链接改为与根目录的相对位址  false
future  显示未来的文章 true
highlight 代码块的设置  
分类 & 标签
参数  描述  默认值
default_category  默认分类  uncategorized
category_map  分类别名  
tag_map 标签别名  
日期 / 时间格式
Hexo 使用 Moment.js 来解析和显示时间。

参数  描述  默认值
date_format 日期格式  MMM D YYYY
time_format 时间格式  H:mm:ss
分页
参数  描述  默认值
per_page  每页显示的文章量 (0 = 关闭分页功能) 10
pagination_dir  分页目录  page
扩展
参数  描述
theme 当前主题名称。值为false时禁用主题
deploy  部署部分的设置

3.安装主题:
{% codeblock %}
git clone https://github.com/tufu9441/maupassant-hexo.git themes/maupassant
npm install hexo-renderer-sass --save
npm install hexo-renderer-jade --save
{% endcodeblock %}
编辑Hexo目录下的 _config.yml，将theme的值改为maupassant。
该主题配置文件_config.yml

4.服务器:
先安装 hexo-server.
{% codeblock %}
$ npm install hexo-server —save
{% endcodeblock %}
启动服务器
{% codeblock %}
$ hexo server
{% endcodeblock %}
启动服务器。默认情况下，访问网址为： http://localhost:4000/。
选项  描述
-p, --port  重设端口
-s, --static  只使用静态文件
-l, --log 启动日记记录，使用覆盖记录格式
停止服务器的话,只能将该进程kill

5.部署

就是将hexo生产的静态文件(public文件夹)同步到服务器上
需要用到deploy命令
{% codeblock %}
$ hexo deploy
{% endcodeblock %}
部署网站。
参数  描述
-g, --generate  部署之前预先生成静态文件

同时需要在_config.yml中配置:


同时根据type安装想要相应的插件:
git :安装 hexo-deployer-git。

$ npm install hexo-deployer-git --save
修改配置。
{% codeblock %}
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
  {% endcodeblock %}

参数  描述
repo  库（Repository）地址
branch  分支名称。如果您使用的是 GitHub 或 GitCafe 的话，程序会尝试自动检测。
message 自定义提交信息 (默认为 Site updated: now("YYYY-MM-DD HH:mm:ss"))

6.使用命令


init
{% codeblock %}
$ hexo init [folder]
{% endcodeblock %}
新建一个网站。如果没有设置 folder ，Hexo 默认在目前的文件夹建立网站。

new
{% codeblock %}
$ hexo new [layout] <title>
{% endcodeblock %}
新建一篇文章。如果没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数代替。如果标题包含空格的话，请使用引号括起来。

generate
{% codeblock %}
$ hexo generate
{% endcodeblock %}
生成静态文件。

选项  描述
-d, --deploy  文件生成后立即部署网站
-w, --watch 监视文件变动
publish
{% codeblock %}
$ hexo publish [layout] <filename>
{% endcodeblock %}
发表草稿。

server
{% codeblock %}
$ hexo server
{% endcodeblock %}
启动服务器。默认情况下，访问网址为： http://localhost:4000/。

选项  描述
-p, --port  重设端口
-s, --static  只使用静态文件
-l, --log 启动日记记录，使用覆盖记录格式
deploy
{% codeblock %}
$ hexo deploy
{% endcodeblock %}
部署网站。

参数  描述
-g, --generate  部署之前预先生成静态文件
render
{% codeblock %}
$ hexo render <file1> [file2] ...
{% endcodeblock %}
渲染文件。

参数  描述
-o, --output  设置输出路径
migrate
{% codeblock %}
$ hexo migrate <type>
{% endcodeblock %}
从其他博客系统 迁移内容。

clean
{% codeblock %}
$ hexo clean
{% endcodeblock %}
清除缓存文件 (db.json) 和已生成的静态文件 (public)。

list
{% codeblock %}
$ hexo list <type>
{% endcodeblock %}
列出网站资料。

version
{% codeblock %}
$ hexo version
{% endcodeblock %}
显示 Hexo 版本。

选项
安全模式
{% codeblock %}
$ hexo --safe
{% endcodeblock %}
在安全模式下，不会载入插件和脚本。当您在安装新插件遭遇问题时，可以尝试以安全模式重新执行。

调试模式
{% codeblock %}
$ hexo --debug
{% endcodeblock %}
在终端中显示调试信息并记录到 debug.log。当您碰到问题时，可以尝试用调试模式重新执行一次，并 提交调试信息到 GitHub。

简洁模式
{% codeblock %}
$ hexo --silent
{% endcodeblock %}
隐藏终端信息。

自定义配置文件的路径
{% codeblock %}
$ hexo --config custom.yml
{% endcodeblock %}
自定义配置文件的路径，执行后将不再使用 _config.yml。

显示草稿
{% codeblock %}
$ hexo --draft
{% endcodeblock %}
显示 source/_drafts 文件夹中的草稿文章。

自定义 CWD
{% codeblock %}
$ hexo --cwd /path/to/cwd
{% endcodeblock %}
自定义当前工作目录（Current working directory）的路径。

7.写作

使用这个命令来创建新文章:
{% codeblock %}
$ hexo new [layout] <title>
{% endcodeblock %}
指定文章的布局（layout），默认为 post，可以通过修改 _config.yml 中的 default_layout 参数来指定默认布局。

布局（Layout）
Hexo 有三种默认布局：post、page 和 draft，它们分别对应不同的路径，而您自定义的其他布局和 post 相同，都将储存到 source/_posts 文件夹。

布局  路径
post  source/_posts
page  source
draft source/_drafts :草稿, 可通过 publish 命令将草稿移动到 source/_posts 文件
不要处理文章,需要将 Front-Matter 中的layout: 设为 false 。

文件名称
Hexo 默认以标题做为文件名称，但您可编辑 new_post_name 参数来改变默认的文件名称，举例来说，设为 :year-:month-:day-:title.md 可让您更方便的通过日期来管理文章。
变量  描述
:title  标题（小写，空格将会被替换为短杠）
:year 建立的年份，比如， 2015
:month  建立的月份（有前导零），比如， 04
:i_month  建立的月份（无前导零），比如， 4
:day  建立的日期（有前导零），比如， 07
:i_day  建立的日期（无前导零），比如， 7

模版（Scaffold）
在新建文章时，Hexo 会根据 scaffolds 文件夹内相对应的文件来建立文件，例如：
{% codeblock %}
$ hexo new photo "My Gallery"
{% endcodeblock %}
在执行这行指令时，Hexo 会尝试在 scaffolds 文件夹中寻找 photo.md，并根据其内容建立文章，以下是您可以在模版中使用的变量：
变量  描述
layout  布局
title 标题
date  文件建立日期


Front-matter 是文件最上方以 --- 分隔的区域，用于指定个别文件的变量，举例来说：
{% codeblock %}
title: Hello World
date: 2013/7/13 20:46:25
---
{% endcodeblock %}
以下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

参数  描述  默认值
layout  布局  
title 标题  
date  建立日期  文件建立日期
updated 更新日期  文件更新日期
comments  开启文章的评论功能 true
tags  标签（不适用于分页）  
categories  分类（不适用于分页）  
permalink 覆盖文章网址  

8.github博客

前提条件: 安装hexo,hexo-deployer-git插件

1)首先在github上建立与自己用户名对应的仓库，仓库名必须为【your_user_name.github.io】
所以对应的博客地址为: https://your_user_name.github.io


2)在_config.yml中配置好github创库信息

{% codeblock %}
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
-  type: git
   repo: https://github.com/cayley-hck/cayley-hck.github.io.git
{% endcodeblock %}

3)配置github ssh key一健登陆

发现每次执行 hexl deploy 都输入github账号和密码,特别麻烦,所以配置ssh key.

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
github_rsa	github_rsa.pub	id_rsa		id_rsa.pub	known_hosts

{% endcodeblock %}

加入SSH Agent

下一步输入：
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

在本机设置SSH Key之后，需要添加到GitHub上，以完成SSH链接的设置.

登陆gihub,点击由上角的用户头像,下拉选择settings. 进入个人设置页面

点击右边的,SSH Keys,选择 New SSH Key

复制 id_rsa.pub的内容,提交保存


测试是否成功,只要输入命令:

{% codeblock %}
huangchkaideAir:.ssh kai$ ssh -T  git@github.com
Hi cayley-hck! You've successfully authenticated, but GitHub does not provide shell access.
{% endcodeblock %}

根据提示输入密码即可.
如果出错的话,输入命令 ssh -T -v git@github.com 根据info解决问题



常见问题
1.修改配置文件时注意YAML语法，参数冒号:后一定要留空格
2.中文乱码请修改文件编码格式为UTF-8

参考地址:
{% link hexo官方文档 https://hexo.io/zh-cn/docs/tag-plugins.html %}
{% link maupassant主题 https://github.com/tufu9441/maupassant-hexo %}
