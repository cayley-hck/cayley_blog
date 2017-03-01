---
title: php-xhproff分析扩展
date: 2016-07-03 18:03:07
tags: php 
categories: php
---
XHProf 是一个轻量级的分层性能测量分析器。 在数据收集阶段，它跟踪调用次数与测量数据，展示程序动态调用的弧线图。 它在报告、后期处理阶段计算了独占的性能度量，例如运行经过的时间、CPU 计算时间和内存开销。 函数性能报告可以由调用者和被调用者终止。 在数据搜集阶段 XHProf 通过调用图的循环来检测递归函数，通过赋予唯一的深度名称来避免递归调用的循环。

XHProf 包含了一个基于 HTML 的简单用户界面(由 PHP 写成)。 基于浏览器的用户界面使得浏览、分享性能数据结果更加简单方便。 同时也支持查看调用图。

XHProf 的报告对理解代码执行结构常常很有帮助。 比如此分层报告可用于确定在哪个调用链里调用了某个函数。


1. 编译安装
{% codeblock %}
  wget http://pecl.php.net/get/xhprof-0.9.3.tgz
  tar zxvf xhprof-0.9.2.tgz
  cd xhprof-0.9.2/extension/
  sudo phpize
  ./configure --with-php-config=/usr/local/php/bin/php-config
  sudo make
  sudo make install
  或者 yum install php-xhprof
{% endcodeblock %}

2. 配置 php.ini
{% codeblock %}
[xhprof]
extension=xhprof.so;
xhprof.output_dir=/tmp/xhprof
{% endcodeblock %}

重启PHP-fpm  or  Apache
3. 嵌入代码
{% codeblock %}
<?php
// cpu:XHPROF_FLAGS_CPU 内存:XHPROF_FLAGS_MEMORY
// 如果两个一起：XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY 
xhprof_enable(XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY);

//要测试的php代码

$data = xhprof_disable();   //返回运行数据
 
// xhprof_lib在下载的包里存在这个目录,记得将目录包含到运行的php代码中
include_once "xhprof_lib/utils/xhprof_lib.php”;  //（这个目录文件在源码中，需要复制到web-work中）
include_once "xhprof_lib/utils/xhprof_runs.php”;  //(这个目录文件在源码中，需要复制到web)
 
$objXhprofRun = new XHProfRuns_Default(); 

// 第一个参数j是xhprof_disable()函数返回的运行信息
// 第二个参数是自定义的命名空间字符串(任意字符串),
// 返回运行ID,用这个ID查看相关的运行结果
$run_id = $objXhprofRun->save_run($data, "xhprof");
var_dump($run_id);
{% endcodeblock %}

4. 页面展示
将xhprof_lib&&xhprof_html相关目录copy到可以访问到的地址
访问 xxx/xhprof_html/index.php?run=$run_id&source=xhprof 就可经看到你的php代码运行的相关情况

下面是一些参数说明
Inclusive Time                 包括子函数所有执行时间。
Exclusive Time/Self Time       函数执行本身花费的时间，不包括子树执行时间。
Wall Time                      花去了的时间或挂钟时间。
CPU Time                       用户耗的时间+内核耗的时间
Inclusive CPU                  包括子函数一起所占用的CPU
Exclusive CPU                  函数自身所占用的CPU