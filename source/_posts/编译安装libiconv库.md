---
title: 编译安装libiconv库
date: 2016-07-27 16:29:07
tags: libiconv
categories: liunx
comments: true
---

iconv是一个计算机程序以及一套应用程序编程接口的名称。它的作用是在多种国际编码格式之间进行文本内码的转换。支持的内码包括：
Unicode相关编码，如UTF-8、UTF-16等等
各国采用的ANSI编码，其中包括GB2312、BIG5等中文编码方式。
作为应用程序的iconv采用命令行界面，允许将某种特定编码的文件转换为另一种编码。(维基百科)

编译安装libiconv库
{% codeblock %}
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
tar zxf libiconv-1.14.tar.gz
cd libiconv-1.14
./configure --prefix=/usr
make
make install
{% endcodeblock %}

如果安装出现如下问题:
{% codeblock %}
[root@niaoyun49026 libiconv-1.14]# make
cd lib && make all
make[1]: Entering directory `/usr/local/src/libiconv-1.14/lib'
make[1]: Nothing to be done for `all'.
make[1]: Leaving directory `/usr/local/src/libiconv-1.14/lib'
cd preload && make all
make[1]: Entering directory `/usr/local/src/libiconv-1.14/preload'
make[1]: Nothing to be done for `all'.
make[1]: Leaving directory `/usr/local/src/libiconv-1.14/preload'
cd srclib && make all
make[1]: Entering directory `/usr/local/src/libiconv-1.14/srclib'
make[2]: Entering directory `/usr/local/src/libiconv-1.14'
make[2]: Nothing to be done for `am--refresh'.
make[2]: Leaving directory `/usr/local/src/libiconv-1.14'
make  all-am
make[2]: Entering directory `/usr/local/src/libiconv-1.14/srclib'
make[3]: Entering directory `/usr/local/src/libiconv-1.14'
make[3]: Nothing to be done for `am--refresh'.
make[3]: Leaving directory `/usr/local/src/libiconv-1.14'
gcc -DHAVE_CONFIG_H -DEXEEXT=\"\" -I. -I.. -I../lib  -I../intl -DDEPENDS_ON_LIBICONV=1 -DDEPENDS_ON_LIBINTL=1   -g -O2 -c progname.c
In file included from progname.c:26:0:
./stdio.h:1010:1: error: [01mgetsundeclared here (not in a function)
 _GL_WARN_ON_USE (gets, "gets is a security hole - use fgets instead");
 ^
make[2]: *** [progname.o] Error 1
make[2]: Leaving directory `/usr/local/src/libiconv-1.14/srclib'
make[1]: *** [all] Error 2
make[1]: Leaving directory `/usr/local/src/libiconv-1.14/srclib'
make: *** [all] Error 2
[root@niaoyun49026 libiconv-1.14]#
{% endcodeblock %}

解决方法(靠谱):
libiconv-1.14/srclib/stdio.in.h

在stdio.in.h中找到 //_GL_WARN_ON_USE (gets, "gets is a securityhole - use fgets instead");
修改为：
/* It is very rare that the developer ever has full control of stdin,
   so any use of gets warrants an unconditional warning.  Assume it is
   always declared, since it is required by C89.  */
//_GL_WARN_ON_USE (gets, "gets is a security hole - use fgets instead");
#if defined(__GLIBC__) &&!defined(__UCLIBC__) &&!__GLIBC_PREREQ(2, 16)
_GL_WARN_ON_USE (gets, "gets is a security hole -use fgets instead");
#endif


或者需要安装一下补丁(网上的查到的)
参考地址{http://www.itkb.ro/kb/linux/patch-libiconv-pentru-glibc-216}
{% codeblock %}
wget -c http://www.itkb.ro/userfiles/file/libiconv-glibc-2.16.patch.gz
cd libiconv-1.14/srclib
patch -p1 stdio.in.h
{% endcodeblock %}
但我执行打补丁命令,等n久都没反应......

使用例子
文件infile从GB18030编码转换至UTF-8编码并写入到文件outfile中：

iconv -f GB18030 -t utf-8 < infile > outfile

php中使用:
默认已激活此扩展， 可以使用--without-iconv 选项禁用它。

--with-iconv=dir 用于 PHP编译时指定 iconv 在系统里的路径，否则会扫描默认路径

使用例子:
./configure --with-iconv=/usr/


PHP语言对iconv封装了的函数：
iconv_get_encoding — 获取 iconv 扩展的内部配置变量
iconv_mime_decode_headers — 一次性解码多个 MIME 头字段
iconv_mime_decode — 解码一个MIME头字段
iconv_mime_encode — Composes a MIME header field
iconv_set_encoding — 为字符编码转换设定当前设置
iconv_strlen — 返回字符串的字符数统计
iconv_strpos — Finds position of first occurrence of a needle within a haystack
iconv_strrpos — Finds the last occurrence of a needle within a haystack
iconv_substr — 截取字符串的部分
iconv — 字符串按要求的字符编码来转换
ob_iconv_handler — 以输出缓冲处理程序转换字符编码


