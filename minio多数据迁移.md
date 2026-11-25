# minio多数据迁移

```shell
情况说明：客户有500G+的数据迁移
1、用rsync
yum -y install rsync
nohup rsync -avP --ignore-existing /mnt/sfs_turbo/minio/ /data/minio/ > rsync_migration.log 2>&1 &

2、用rclone
wget https://downloads.rclone.org/rclone-current-linux-amd64.zip
nohup rclone copy /mnt/sfs_turbo/minio/ /data/minio/ -P --transfers=16 --checkers=20 --ignore-existing > minio_migration.log 2>&1 &
```