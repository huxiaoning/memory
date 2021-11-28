# OpenWRT扩展Overlay

[overlay视频教程](https://www.youtube.com/watch?v=YwbwzuXKNlg)





先看下分区情况

```bash
# 分区情况
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0         7:0    0 946.7M  0 loop /overlay
mmcblk0     179:0    0  14.9G  0 disk 
├─mmcblk0p1 179:1    0    64M  0 part /mnt/mmcblk0p1
└─mmcblk0p2 179:2    0   1.2G  0 part /rom

# 分区挂载情况
$ df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root               254.0M    254.0M         0 100% /rom
tmpfs                     1.9G     18.2M      1.9G   1% /tmp
/dev/loop0              944.7M    155.3M    789.4M  16% /overlay
overlayfs:/overlay      944.7M    155.3M    789.4M  16% /
tmpfs                   512.0K         0    512.0K   0% /dev
cgroup                    1.9G         0      1.9G   0% /sys/fs/cgroup
/dev/mmcblk0p1           63.0M     12.5M     49.2M  20% /mnt/mmcblk0p1
overlayfs:/overlay      944.7M    155.3M    789.4M  16% /opt/docker
 
```



```bash
# x86使用 cfdisk 或者 cfdisk /dev/sda
$ cfdisk /dev/mmcblk0
###  做了一些图形界面操作,增了一个G的Overlay

$ lsblk 
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0         7:0    0 946.7M  0 loop /overlay
mmcblk0     179:0    0  14.9G  0 disk 
├─mmcblk0p1 179:1    0    64M  0 part /mnt/mmcblk0p1
├─mmcblk0p2 179:2    0   1.2G  0 part /rom
└─mmcblk0p3 179:3    0     1G  0 part /mnt/mmcblk0p3

# 格式化新增的分区 (这里注意如果已经挂载了，要先卸载(umount /dev/mmcblk0p3)再格式化)
$ mkfs.ext4 /dev/mmcblk0p3
# 挂载分区
$ mount /dev/mmcblk0p3 /mnt/mmcblk0p3
mount: /mnt/mmcblk0p3: mount point does not exist.
# 上面报错了，需要提前创建挂载点目录
$ mkdir -p /mnt/mmcblk0p3
# 再次挂载就成功了
$ mount /dev/mmcblk0p3 /mnt/mmcblk0p3
# 验证挂载成功
$ ls /mnt/mmcblk0p3
lost+found
```



迁移Overlay中的配置文件

```
$ cp -r /overlay/* /mnt/mmcblk0p3/
$ ls /mnt/mmcblk0p3              
lost+found  upper       work
```



在`OpenWRT WEB`管理后台中启用新的`overlay`挂载点

![image-20211127202519672](https://raw.githubusercontent.com/huxiaoning/img/master/20211127202519.png)





![image-20211127202645177](https://raw.githubusercontent.com/huxiaoning/img/master/20211127202645.png)

这一步别忘了保存应用。然后重启路由器。



照葫芦画瓢，再弄一个8G大小的分区给Docker用。[docker教程](https://www.youtube.com/watch?v=isr6cPKy8eg&t=593s)

