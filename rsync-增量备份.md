# rsync-增量备份

```javascript
#!/bin/bash

SOURCE_DIR="/teambition/data/minio"
BACKUP_ROOT="/teambition/data/nfs"
INCREMENTAL_DIR_NAME="minio-$(date +%m-%d-%H-%M)"
LATEST_BACKUP=$(find "$BACKUP_ROOT" -type d  -name "minio-*" | sort | tail -n 1)

mkdir -p "${BACKUP_ROOT}/${INCREMENTAL_DIR_NAME}"
if [ -d "$LATEST_BACKUP" ]; then
    rsync -avh --progress --link-dest="$LATEST_BACKUP" "$SOURCE_DIR/" "${BACKUP_ROOT}/${INCREMENTAL_DIR_NAME}"
else
    echo "No previous backup found. Cannot perform incremental backup."
    exit 1
fi
```
```javascript
#!/bin/bash
SOURCE_DIR="/teambition/data/minio"
BACKUP_ROOT="/teambition/data/nfs"
INCREMENTAL_DIR_NAME="minio-$(date +%m-%d-%H-%M)"

rsync -avh --progress --link-dest="$(ls /teambition/data/nfs/ | sort | tail -n 1)" "$SOURCE_DIR/" "${BACKUP_ROOT}/${INCREMENTAL_DIR_NAME}" > ~/minio/output-`date +%m-%d-%H-%M`.log

# 检查备份文件数量，如果超过10个，则删除最早的备份
backup_count=$(ls -1 "$BACKUP_ROOT" | wc -l)
if [ "$backup_count" -gt 5 ]; then
    old_backup=$(ls -1t "$BACKUP_ROOT" | sort -r | tail -n +6)
    if [ -n "$old_backup" ]; then
        echo "Removing the oldest backup: ${old_backup}"
        rm -rf "${BACKUP_ROOT}/${old_backup}"
    fi
fi
```
```javascript
#!/bin/bash

SOURCE_DIR="/teambition/data/minio"
BACKUP_ROOT="/teambition/data/nfs"
INCREMENTAL_DIR_NAME="minio-$(date +%F)"

rsync -avzh --progress "$SOURCE_DIR/" "${BACKUP_ROOT}/${INCREMENTAL_DIR_NAME}" > ~/minio/output-`date +%F`.log
```