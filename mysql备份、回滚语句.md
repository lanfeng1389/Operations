# mysql备份、回滚语句

```javascript
1. 备份语句
kubectl exec -it mariadb-galera-0 -n middleware -- /bin/bash -c "mysqldump -u root -pdZQDgk84ZIZKpOoZ aesi_previsioning" > aesi_previsioning.sql
kubectl exec -it mariadb-galera-0 -n middleware -- /bin/bash -c "mysqldump -u root -pdZQDgk84ZIZKpOoZ seat" > seat.sql
kubectl exec -it mariadb-galera-0 -n middleware -- /bin/bash -c "mysqldump -u root -pdZQDgk84ZIZKpOoZ tb_tdp" > tb_tdp.sql

需要先创库，在执行语句
kubectl exec -it mariadb-galera-0 -n middleware sh
mysql -u root -pdZQDgk84ZIZKpOoZ
show databases;
create database aesi_previsioning;
create database seat;
create database tb_tdp;

2.恢复语句
kubectl exec -it mariadb-galera-0 -n middleware -- /bin/bash -c "mysql -u root -pdZQDgk84ZIZKpOoZ -D aesi_previsioning" < aesi_previsioning.sql
kubectl exec -it mariadb-galera-0 -n middleware -- /bin/bash -c "mysql -u root -pdZQDgk84ZIZKpOoZ -D seat" < seat.sql
kubectl exec -it mariadb-galera-0 -n middleware -- /bin/bash -c "mysql -u root -pdZQDgk84ZIZKpOoZ -D tb_tdp" < tb_tdp.sql
```