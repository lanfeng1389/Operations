# 非Lvm根目录分区

注意原本的挂载“/”的文件系统是ext4的还是xfs的很重要！！！

```javascript
1. 创建分区新建逻辑卷
fdisk /dev/vda
pvcreate /dev/vda3
vgcreate vg1 /dev/vda3
lvcreate -n lv1 -l 100%FREE vg1
mkfs.xfs /dev/mapper/vg1-lv1 或
mkfs.ext4 /dev/mapper/vg1-lv1

2.挂载磁盘，进行数据迁移
mkdir /mnt/new_root     #新的
mount /dev/mapper/vg1-lv1 /mnt/new_root/
mkdir /mnt/temp_root
mount /dev/mapper/centos_node1-root /mnt/temp_root/    #老的
yum -y install rsync
rsync -avxHAX /mnt/temp_root/ /mnt/new_root/  --info=progress2
###进行数据迁移
df -Th

3.更新/boot下的引导信息
grub2-mkconfig -o /boot/grub2/grub.cfg

4.把旧的根分区UUID更新为新逻辑卷的UUID
lsblk -f
vim /mnt/new_root/etc/fstab   ##把旧的删除，新的加进去
grub2-install /dev/vda  ##更新启动加载项
dracut -f /boot/initramfs-$(uname -r).img $(uname -r)  ##更新initramfs

5.修改boot启动配置文件
umount /mnt/temp_root   #卸载临时挂载点
vim /boot/grub2/grub.cfg
注释掉/boot配置文件中旧的启动项,大概是在87~118行
reboot  ##重启服务
lsblk
df -Th 验证

6.把旧的磁盘划分成逻辑卷加载到新的磁盘根目录
创建pvc
pvcreate /dev/vda2
卷组扩容
vgs
vgextend vg1 /dev/vda2
逻辑扩容
lvs
lvextend -l +100%FREE /dev/mapper/vg1-lv1
刷新文件系统
xfs_growfs /dev/mapper/vg1-lv1 或
resize2fs /dev/mapper/vg1-lv1
```

**数据迁移完成以后验证一致性**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EJK2XNBBhVbBWA9/c131503d6173490a8b33fd20e037eb983437.png)

**重启系统以后默认/etc/fstab已变更**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EJK2XNBBhVbBWA9/ee5add78b5104c449398130254988c133437.png)

**要注释的内容**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EJK2XNBBhVbBWA9/99ce18a94b4544869c038796c97340f13437.png)

**全部操作完成以后的成果**

**原本的磁盘**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EJK2XNBBhVbBWA9/810c71a9fd3c4dc0830bb9a8158c56393437.png)

**划分完以后的磁盘**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EJK2XNBBhVbBWA9/793a9fc52c30476ca05be8441fc9497d3437.png)

**非lvm扩容**

fdisk -lu **查看**

```shell
yum install -y cloud-utils-growpart
LC_ALL=en_US.UTF-8 growpart /dev/vdb 1
resize2fs /dev/vda1  或者
xfs_growfs /dev/vda1
真的是想的太复杂了，血的教训
```

需注意的是磁盘下如果有多个分区只能对最后一个进行分区！！！

```shell
yum install -y cloud-utils-growpart
yum install -y gdisk
LC_ALL=en_US.UTF-8 growpart /dev/vdb 1 
```

`Disk label type`值为`dos`表示MBR分区，值为`gpt`表示GPT分区。