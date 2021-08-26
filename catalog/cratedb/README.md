# CrateDB
On `2021-03-23`, [CrateDB became 100% open source](https://crate.io/a/farewell-to-the-cratedb-enterprise-license-faq/). It is Apache 2.0 licensed and is a clustered SQL database as well as blob storage solution.

This provided StatefulSet was derived from the [CrateDB documentation](https://crate.io/docs/crate/howtos/en/latest/deployment/containers/kubernetes.html). The version used in the documentation may be confusion because during version 4.2 there was an enterprise license, whereas this statefulset deploys 4.6, which is completely free.

# Deploying
```text
$ kubectl create ns cratedb
$ kubectl -n cratedb apply -f https://raw.githubusercontent.com/scalabledelivery/kubernetes/master/catalog/cratedb/deploy.yaml
$ kubectl -n cratedb rollout status statefulset/crate --watch --timeout=15m
```

# Console Access
```text
$ kubectl -n cratedb exec -it crate-0 -- crash
```

# Accessing Web UI
To get to the Web UI run this port-proxy command and go to https://localhost:4200
```text
$ kubectl -n cratedb port-forward svc/crate-internal 4200:4200
```

# Security
This installation is not secure and is what you would get by default if you ran CrateDB out of the box.
