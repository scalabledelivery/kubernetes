# ScyllaDB
An Open Source NoSQL database that is a drop in replacement for Cassandra and has [DynamoDB compatibility](https://docs.scylladb.com/using-scylla/alternator/), written in C++. Check out the official documentation can be found [here](https://docs.scylladb.com/).

# Deploying
```text
$ kubectl create ns scylladb
$ kubectl -n scylladb apply -f https://raw.githubusercontent.com/scalabledelivery/kubernetes/master/catalog/scylladb/deploy.yaml
$ kubectl -n scylladb rollout status statefulset/scylladb --watch --timeout=10m
```

# Console Access
```text
$ kubectl -n scylladb exec -it scylladb-0 -- cqlsh
```

# Client Drivers
CQL drivers information can be found [here](https://docs.scylladb.com/using-scylla/drivers/cql-drivers/) and DynamoDB driver information [here](https://docs.scylladb.com/using-scylla/drivers/dynamo-drivers/).

