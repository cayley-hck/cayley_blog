---
title: php7扩展开发之初见
date: 2016-12-28 22:12:56
tags:	php
---

作为一名php程序猿，再次申明php是世界上最好的语言（骄傲脸）。
本来以PHP7作为基础，记录如何从零开始创建一个PHP扩展，分析php扩展基本骨架组成。认识如果开发一个php7的扩展。

PHP 扩展由几个文件组成，这些文件对所有扩展来说都是通用的。不同扩展之间，这些文件的很多细节是相似的，只是要费力去复制每个文件的内容。
所以提供php官方提供一个脚本做所有的初始化工作，名为 ext_skel，在php7源码的./ext目录下
该工具的使用如下：
{% codeblock %}
./ext_skel
./ext_skel --extname=module [--proto=file] [--stubs=file] [--xml[=file]]
           [--skel=dir] [--full-xml] [--no-help]

  --extname=module   module is the name of your extension
  --proto=file       file contains prototypes of functions to create
  --stubs=file       generate only function stubs in file
  --xml              generate xml documentation to be added to phpdoc-svn
  --skel=dir         path to the skeleton directory
  --full-xml         generate xml documentation for a self-contained extension
                     (not yet implemented)
  --no-help          don't try to be nice and create comments in the code
                     and helper functions to test if the module compiled
{% endcodeblock %}
其中比较重要的参数是--extname 会将扩展的名称传给 ext_skel
而--no-help，会使ext_skel 不会在生成的文件里省略很多有用的注释。

接下来创建个名cayley的php扩展
{% codeblock %}
./ext_skel --extname=cayley
Creating directory cayley
Creating basic files: config.m4 config.w32 .gitignore cayley.c php_cayley.h CREDITS EXPERIMENTAL tests/001.phpt cayley.php [done].

To use your new extension, you will have to execute the following steps:

1.  $ cd ..
2.  $ vi ext/cayley/config.m4
3.  $ ./buildconf
4.  $ ./configure --[with|enable]-cayley
5.  $ make
6.  $ ./sapi/cli/php -f ext/cayley/cayley.php
7.  $ vi ext/cayley/cayley.c
8.  $ make

Repeat steps 3-6 until you are satisfied with ext/cayley/config.m4 and
step 6 confirms that your module is compiled into PHP. Then, start writing
code and repeat the last two steps as often as necessary.
{% endcodeblock %}

现在来看下cayley扩展的结构：
{% codeblock %}
 ls -al
total 64
drwxr-xr-x  12 cayley  staff   408 12 28 22:30 .
drwxr-xr-x@ 78 cayley  staff  2652 12 28 16:45 ..
-rw-r--r--   1 cayley  staff   398 12 28 16:45 .gitignore
-rw-r--r--   1 cayley  staff     7 12 28 16:45 CREDITS
-rw-r--r--   1 cayley  staff     0 12 28 16:45 EXPERIMENTAL
-rw-r--r--   1 cayley  staff  5298 12 28 21:32 cayley.c
-rw-r--r--   1 cayley  staff   518 12 28 21:58 cayley.php
-rw-r--r--   1 cayley  staff  2076 12 28 17:06 config.m4
-rw-r--r--   1 cayley  staff   355 12 28 16:45 config.w32
drwxr-xr-x   2 cayley  staff    68 12 28 21:40 include
-rw-r--r--   1 cayley  staff  2316 12 28 16:45 php_cayley.h
drwxr-xr-x   3 cayley  staff   102 12 28 16:45 tests
{% endcodeblock %}

config.m4 ： 是与UNIX构建的系统交互的文件，文件告诉 UNIX 构建系统哪些扩展 configure 选项是支持的，你需要哪些扩展库，以及哪些源文件要编译成它的一部分。
config.m4 文件使用 GNU autoconf 语法编写。注释用字符串 dnl 分隔，字符串则放在左右方括号中间（例如，[ 和 ]）。字符串可按需要多次嵌套引用
关于config.m4更多说明，请参考官方的 {% link 与UNIX构建系统交互:config.m4  http://php.net/manual/en/internals2.buildsys.configunix.php %}

cofig.w32 : 顾名思义它是用于 Windows 构建的 扩展的 ，用法与 config.m4 文件类似，但它是使用 JavaScript 编写的。更多请参数官方的{% link 使用Windows 构建系统:config.w32  http://php.net/manual/en/internals2.buildsys.configwin.php %}

php_cayely.h : 当将扩展作为静态模块构建并放入 PHP 二进制包时，构建系统要求用 php_ 加扩展的名称命名的头文件包含一个对扩展模块结构的指针定义。就象其他头文件，此文件经常包含附加的宏、原型和全局量。

cayley.c : 主要的扩展源文件。此文件包含模块结构定义、INI 条目、管理函数、用户空间函数和其它扩展所需的内容。

CREDITS :  说明该扩展的开发者和贡献者资料

package.xml ：  文件是基于PECL 的扩展特有的，是一个元信息文件，记录了扩展的信赖关系、作者、安装需求和其他有价值的信息。对于不是基于 PECL 的扩展来说，此文件就无关紧要了

更多文件结构信息请参考官方文档{% link 扩展的结构 http://php.net/manual/zh/internals2.structure.php %}


下面开发一个简单的php7扩展步骤

一）要修改config.m4文件
{% codeblock %}

vim ./config.m4

dnl 看到注释说明，如果你所编写的扩展如果依赖其它的扩展或者lib库，需要去掉PHP_ARG_WITH相关代码的注释，否则去掉PHP_ARG_ENABLE注释
dnl 所以我们简单的php扩展不需要依赖其他的扩展库，所以去掉PHP_ARG_ENABLE注释

dnl If your extension references something external, use with:

dnl PHP_ARG_WITH(cayley, for cayley support,
dnl Make sure that the comment is aligned:
dnl [  --with-cayley             Include cayley support])

dnl Otherwise use enable:

dnl 取消了这里的注释
PHP_ARG_ENABLE(cayley, whether to enable cayley support,
Make sure that the comment is aligned:
[  --enable-cayley           Enable cayley support])
{% endcodeblock %}

上面PHP_ARG_ENABLE函数有三个参数，第一个参数是我们的扩展名(注意不用加引号)，第二个参数是当我们运行./configure脚本时显示的内容，最后一个参数则是我们在调用./configure --help时显示的帮助信息

还有config.m4：
{% codeblock %}
  dnl  PHP_NEW_EXTENSION函数申明
  PHP_NEW_EXTENSION(cayley, cayley.c, $ext_shared,, -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1)
{% endcodeblock %}

PHP_NEW_EXTENSION函数声明了这个扩展的名称、需要的源文件名、此扩展的编译形式。如果我们的扩展使用了多个文件，便可以将这多个文件名罗列在函数的参数里，用空格分开

PHP_NEW_EXTENSION(cayley, cayley.c cayley2.c cayley3.c, $ext_shared)

最后的$ext_shared参数用来声明这个扩展不是一个静态模块，而是在php运行时动态加载的



二）实现代码

至于开发语言是C,所以要使用C语言的编码标准，例如注释要用 /*  */，而不用C++的//
更多标准请查看官方的 {% link PECL/PHP编码标准 http://git.php.net/?p=php-src.git;a=blob_plain;f=CODING_STANDARDS;hb=HEAD %}
 
主要是修改caylye.c文件
{% codeblock %}
vim cayley.c
{% endcodeblock %}

找到PHP_FUNCTION(confirm_say_compiled)，在其上面增加如下代码：
{% codeblock %}
PHP_FUNCTION(cayley)
{
        zend_string *strg;
        strg = strpprintf(0, "hello word");
        RETURN_STR(strg);
}
{% endcodeblock %}
在const zend_function_entry cayley_functions 里面添加代码
PHP_FE(cayley, NULL)
{% codeblock %}
const zend_function_entry cayley_functions[] = {
	PHP_FE(cayley, NULL) 						/* 插入的代码 */
	PHP_FE(confirm_cayley_compiled,	NULL)		/* For testing, remove later. */
	PHP_FE_END	/* Must be the last line in cayley_functions[] */
};
{% endcodeblock %}

三）编译

phpize

{% codeblock %}
phpize

Configuring for:
PHP Api Version:         20151012
Zend Module Api No:      20151012
Zend Extension Api No:   320151012
cp: /Users/cayley/src/php7/php-7.0.14/ext/cayley/build/

{% endcodeblock %}


./configure
{% codeblock %}

./configure
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for a sed that does not truncate output... /usr/bin/sed
checking for cc... cc
......
.....
creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h


{% endcodeblock %}


make && make install

{% codeblock %}

  make

........
/bin/sh /Users/cayley/src/php7/php-7.0.14/ext/cayley/libtool --mode=install cp ./cayley.la /Users/cayley/src/php7/php-7.0.14/ext/cayley/modules
cp ./.libs/cayley.so /Users/cayley/src/php7/php-7.0.14/ext/cayley/modules/cayley.so
cp ./.libs/cayley.lai /Users/cayley/src/php7/php-7.0.14/ext/cayley/modules/cayley.la
----------------------------------------------------------------------
Libraries have been installed in:
   /Users/cayley/src/php7/php-7.0.14/ext/cayley/modules
.....

install
Installing shared extensions:     /usr/local/Cellar/php70/7.0.12_5/lib/php/extensions/no-debug-non-zts-20151012/
 cayley@cayley  ~/src/php7/php-7.0.14/ext/cayley  ll /usr/local/Cellar/php70/7.0.12_5/lib/php/extensions/no-debug-non-zts-20151012/
total 24
-rwxr-xr-x  1 cayley  admin    10K 12 28 21:40 cayley.so

{% endcodeblock %}


还有一种编译和使用扩展的方式，就是静态扩展，也就是说将我们的扩展编译到PHP主程序中

直接使用 ./configure --help 是不会出现扩展cayley,需要选使用php源码的buildconf命令生成新的configure脚本

{% codeblock %}
 ./configure --help |grep cayley

 ./buildconf --force
Forcing buildconf
Removing configure caches
buildconf: checking installation...
buildconf: autoconf version 2.69 (ok)
rebuilding aclocal.m4
rebuilding configure
rebuilding main/php_config.h.in

./configure --help |grep cayley
  --enable-cayley           Enable cayley support

{% endcodeblock %}

就像help所说，我们编译php的，使用configure --enable-cayley就可以将cayley扩展编译到php主程序中了



四）使用扩展

修改php.ini 添加代码
{% codeblock %}
[cayley]
extension = cayey.so
{% endcodeblock %}

查看是否安装成功
{% codeblock %}
php -m |grep cayley
cayley
{% endcodeblock %}

测试

vim cayley.php添加如下代码
{% codeblock %}

echo cayley();

{% endcodeblock %}

运行

{% codeblock %}
php cayley.php
Functions available in the test extension:
cayley
confirm_cayley_compiled

hello word%                                                                                                                                            
{% endcodeblock %}

到这里一个简单的php7的扩展就完成，可见改扩展只提供了一个cayley()函数而已，而该函数只是输出了“hello word”


现在来看看写这个扩展，我们做了啥

其实使用ZEND_FUNCTION宏弄了个函数cayley

这时候的只是我在C中实现了它，但是php还不知道有这个函数

所以让php知道它，扩展定义的zend_module_entry cayley_module_entry（它是联系扩展与PHP的重要纽带）
{% codeblock %}

/*  cayley_module_entry
 */
zend_module_entry cayley_module_entry = {
	STANDARD_MODULE_HEADER,	
	"cayley",				/* 这个是扩展的名称 */
	cayley_functions,		/* 扩展的函数  */
	PHP_MINIT(cayley),
	PHP_MSHUTDOWN(cayley),
	PHP_RINIT(cayley),		/* Replace with NULL if there's nothing to do at request start */
	PHP_RSHUTDOWN(cayley),	/* Replace with NULL if there's nothing to do at request end */
	PHP_MINFO(cayley),
	PHP_CAYLEY_VERSION,		/* 扩展的版本 */
	STANDARD_MODULE_PROPERTIES
};

{% endcodeblock %}
然后使用ZEND_FE()宏函数是对我们walu_hello函数的一个声明，如果我们有多个函数，可以直接以类似的形式添加到PHP_FE(confirm_cayley_compiled,	NULL)之前

{% codeblock %}
const zend_function_entry cayley_functions[] = {
	PHP_FE(cayley, NULL)						/* cayley函数的申明 */
	PHP_FE(confirm_cayley_compiled,	NULL)		/* For testing, remove later. */
	PHP_FE_END	/* Must be the last line in cayley_functions[] */
};
{% endcodeblock %}

test123
