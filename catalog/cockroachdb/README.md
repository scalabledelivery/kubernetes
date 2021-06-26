# Cockroach DB
A distributed SQL database that scales horizontally. Documentation can be found [here](https://www.cockroachlabs.com/docs/).

**NOTE:** Be sure to check out the licensing model [here](https://www.cockroachlabs.com/docs/stable/licensing-faqs.html). Core versions of Cockroach DB become open source after 3 years. The core version also lacks 


# Deploying
```text
$ kubectl create ns cockroachdb
$ kubectl create secret generic cockroach-init-sync --from-literal=shared-secret=snakeoil
$ kubectl -n cockroachdb apply -f catalog/cockroachdb/deploy.yaml
$ kubectl -n cockroachdb apply -f https://raw.githubusercontent.com/scalabledelivery/kubernetes/master/catalog/cockroachdb/deploy.yaml
$ kubectl -n cockroachdb rollout status statefulset/cockroachdb --watch --timeout=10m
```

# Console Access
```text
$ kubectl -n cockroachdb exec -it cockroachdb-0 -- cockroach sql --certs-dir /cockroach/certs
```

# Client Drivers
There are various code samples [here](https://www.cockroachlabs.com/docs/stable/hello-world-example-apps.html) that can help you with getting started on developing against the database.
