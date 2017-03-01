---
title: 路由宝刷机pandorabox后利用自带存储卡
date: 2016-07-29 22:40:39
tags:
---
路由宝,自带8G的内存卡,但刷机后,它的分区太难用了,居然分了8个区,太乱了.
{% codeblock %}
[root@PandoraBox:/mnt/mmcblk0p2]#df
Filesystem                Size      Used Available Use% Mounted on
rootfs                   19.9M    772.0K     19.2M   4% /
/dev/root                10.8M     10.8M         0 100% /rom
tmpfs                    61.8M    396.0K     61.4M   1% /tmp
/dev/mtdblock7           19.9M    772.0K     19.2M   4% /overlay
overlayfs:/overlay       19.9M    772.0K     19.2M   4% /
tmpfs                   512.0K         0    512.0K   0% /dev
/dev/mmcblk0p2          126.0M      4.5K    126.0M   0% /mnt/mmcblk0p2
/dev/mmcblk0p3            2.0G      4.0K      2.0G   0% /mnt/mmcblk0p3
/dev/mmcblk0p1          126.0M      4.5K    126.0M   0% /mnt/mmcblk0p2
/dev/mmcblk0p5            2.0G      4.0K      2.0G   0% /mnt/mmcblk0p5
/dev/mmcblk0p6            2.0G      4.0K      2.0G   0% /mnt/mmcblk0p6
/dev/mmcblk0p7            1.1G      4.0K      1.1G   0% /mnt/mmcblk0p7
{% endcodeblock %}

所以我将它格式化了,并合并了分区,如下:

先使用opkg包管理器安装e2fsprogs,安装后才能使用(mkfs.ext mkfs.ext2 mkfs.ext3 mkfs.ext4) 等格式化命令
{% codeblock %}
[root@PandoraBox:/mnt/mmcblk0p2]#opkg install e2fsprogs
Installing e2fsprogs (1.42.4-1) to root...
Downloading http://downloads.openwrt.org.cn/PandoraBox/ralink/packages/base/e2fsprogs_1.42.4-1_ralink.ipk.
Configuring e2fsprogs.
{% endcodeblock %}
将内存卡的挂载点,umount掉,才能删除分区
{% codeblock %}
  umount: can't umount /dev/mmcblk0p4: Invalid argument
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p1
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p2
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p3
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p4
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p5
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p6
[root@PandoraBox:/mnt]#umount /dev/mmcblk0p7

#再看下,发现已经都取消挂载了
[root@PandoraBox:/mnt]#df
Filesystem                Size      Used Available Use% Mounted on
rootfs                   19.9M    968.0K     19.0M   5% /
/dev/root                10.8M     10.8M         0 100% /rom
tmpfs                    61.8M    568.0K     61.2M   1% /tmp
/dev/mtdblock7           19.9M    968.0K     19.0M   5% /overlay
overlayfs:/overlay       19.9M    968.0K     19.0M   5% /
tmpfs                   512.0K         0    512.0K   0% /dev
{% endcodeblock %}

然后再使用cfdisk命令,将分区删除并新建一个分区
{% codeblock %}
[root@PandoraBox:/mnt]#cfdisk /dev/mmcblk0 
{% endcodeblock %}

![分区](/images/cfdisk01.png)

最后结果:

{% codeblock %}

                                               cfdisk (util-linux 2.24.1)

                            Disk Drive: /dev/mmcblk0
                        Size: 7948206080 bytes, 7948 MB
              Heads: 4   Sectors per Track: 16   Cylinders: 242560

    Name        Flags      Part Type  FS Type          [Label]        Size (MB)
 ------------------------------------------------------------------------------
    mmcblk0p1               Primary   Linux                             7948.21 



     [ Bootable ]  [  Delete  ]  [   Help   ]  [ Maximize ]  [  Print   ]
     [   Quit   ]  [   Type   ]  [  Units   ]  [  Write   ]

                  Quit program without writing partition table

#最后记得write哦
{% endcodeblock %}


现在只有一个分区了,要格式化一下,我这里将内存卡格式化ext4格式的
{% codeblock %}
[root@PandoraBox:/mnt]#mkfs.ext4 /dev/mmcblk0
mke2fs 1.42.4 (12-June-2012)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
485760 inodes, 1940480 blocks
97024 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1988100096
60 block groups
32768 blocks per group, 32768 fragments per group
8096 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): 
done
Writing superblocks and filesystem accounting information: done 

{% endcodeblock %}

最近一步,当然是挂载新分区到相应的目录咯
{% codeblock %}

[root@PandoraBox:/]#mount /dev/mmcblk0 /data
#看下结果
[root@PandoraBox:/]#df
Filesystem                Size      Used Available Use% Mounted on
rootfs                   19.9M    972.0K     19.0M   5% /
/dev/root                10.8M     10.8M         0 100% /rom
tmpfs                    61.8M    572.0K     61.2M   1% /tmp
/dev/mtdblock7           19.9M    972.0K     19.0M   5% /overlay
overlayfs:/overlay       19.9M    972.0K     19.0M   5% /
tmpfs                   512.0K         0    512.0K   0% /dev
/dev/mmcblk0              7.2G     16.7M      6.8G   0% /data
[root@PandoraBox:/]#

{% endcodeblock %}


