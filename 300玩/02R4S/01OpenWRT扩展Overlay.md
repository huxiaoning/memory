# OpenWRT扩展Overlay

[视频教程](https://www.youtube.com/watch?v=YwbwzuXKNlg)



先看下分区情况

```bash
$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
mmcblk0     179:0    0 14.9G  0 disk 
├─mmcblk0p1 179:1    0   64M  0 part /mnt/mmcblk0p1
└─mmcblk0p2 179:2    0  1.2G  0 part /opt/docker
                                     /
$ df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                 1.2G    829.6M    335.6M  71% /
tmpfs                     1.9G     18.6M      1.9G   1% /tmp
tmpfs                   512.0K         0    512.0K   0% /dev
cgroup                    1.9G         0      1.9G   0% /sys/fs/cgroup
/dev/mmcblk0p1           63.0M     12.4M     49.3M  20% /mnt/mmcblk0p1
/dev/root                 1.2G    829.6M    335.6M  71% /opt/docker
 
```



```bash
# x86使用 cfdisk 或者 cfdisk /dev/sda
$ cfdisk /dev/mmcblk0
###  做了一些图形界面操作,增了一个G的Overlay

$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
mmcblk0     179:0    0 14.9G  0 disk 
├─mmcblk0p1 179:1    0   64M  0 part /mnt/mmcblk0p1
├─mmcblk0p2 179:2    0  1.2G  0 part /opt/docker
│                                    /
└─mmcblk0p3 179:3    0    1G  0 part

# 格式化新增的分区
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

未完12:20