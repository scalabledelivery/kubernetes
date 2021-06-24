# Cassandra
An Open Source NoSQL database. Check out the official website [here](https://cassandra.apache.org/).

There is a quickstart guide for using Cassandra [here](https://cassandra.apache.org/quickstart/).

# Deploying
```text
$ kubectl create ns cassandra
$ kubectl -n cassandra apply -f https://raw.githubusercontent.com/scalabledelivery/kubernetes/master/catalog/cassandra/deploy.yaml
$ kubectl -n cassandra rollout status statefulset/cassandra --watch --timeout=10m
```

# Console Access
```text
$ kubectl -n cassandra run --rm -it cql-console --image nuvo/docker-cqlsh -- cqlsh cassandra.cassandra.svc.cluster.local 9042 --cqlversion='3.4.4'
```
