# Maria DB - 10.5 (Galera)
This statefulset will deploy a multi-master Maria DB 10.5 cluster, with galera.

# Deploying
```text
$ kubectl create ns mariadb
$ kubectl -n mariadb apply -f https://raw.githubusercontent.com/scalabledelivery/kubernetes/master/catalog/mariadb-10.5-galera/deploy.yaml
$ kubectl -n mariadb rollout status statefulset/mariadb --watch --timeout=10m
```

# Console Access
To get a mysql console as root.
```text
# kubectl -n mariadb exec -it mariadb-0 -c mariadb -- mysql
```

This installation is not secured by default. You may want to consider openning a shell and running `mysql_secure_installation`.
```text
# kubectl -n mariadb exec -it mariadb-0 -c mariadb -- bash
```

# Testing the Cluster
```text
# kubectl -n mariadb exec mariadb-0 -c mariadb -- mysql -e 'SHOW GLOBAL STATUS LIKE "wsrep_cluster_size";'
# kubectl -n mariadb exec mariadb-0 -c mariadb -- mysql -e 'SHOW GLOBAL STATUS LIKE "wsrep%";'
```

Here's some commands to quickly test out the replication.
```text
# kubectl -n mariadb exec mariadb-1 -c mariadb -- mysql -e 'CREATE DATABASE shop; USE shop; CREATE TABLE users (id INT, name VARCHAR(20), email VARCHAR(20)); INSERT INTO users (id,name,email) VALUES(1,"Bob","abc@xyz.com");'
# kubectl -n mariadb exec mariadb-0 -c mariadb -- mysql -e 'USE shop; SELECT * FROM users;'
# kubectl -n mariadb exec mariadb-1 -c mariadb -- mysql -e 'USE shop; SELECT * FROM users;'
# kubectl -n mariadb exec mariadb-2 -c mariadb -- mysql -e 'USE shop; SELECT * FROM users;'
```

Here's a test to see if recovery happens. We kill a pod, insert a row, and check if it was replicated by the live nodes.
```text
# kubectl delete pod mariadb-0 --force
# kubectl -n mariadb exec mariadb-1 -c mariadb -- mysql -e 'USE shop; INSERT INTO users (id,name,email) VALUES(2,"Joe","zxc@xyz.com");'
# kubectl -n mariadb exec mariadb-1 -c mariadb -- mysql -e 'USE shop; SELECT * FROM users;'
# kubectl -n mariadb exec mariadb-2 -c mariadb -- mysql -e 'USE shop; SELECT * FROM users;'
```

Once the killed pod recovers, check if it also replicated the data.
```text
# kubectl -n mariadb exec mariadb-0 -c mariadb -- mysql -e 'USE shop; SELECT * FROM users;'
```
