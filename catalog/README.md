# Scalable Delivery Catalog
This catalog contains manifests that can be used in a Kubernetes cluster. The manifests are free to use and are examples of scalable applications.

These applications are provided as `StatefulSets`, `Deployments`, or `DaemonSets`.

Additional examples for each application will be contained in it's directory. Generic examples will be in the catalog prefixed with `example-`.

# Best Practices
For clustered application the best practices will vary between applications. However a general rule of thumb is that you don't want to go below 3 and you want to keep an odd number of them running.

When using this catalog, you should copy the manifests you want and use CI/CD tooling to do your deployments. The `README.md` will provide some explanation on how to deploy manifests directly from this repository, but that is only recommended for evaluating these apps.

These manifests will change over time and you don't want to get caught with application breaking changes ruining your deployments.

# Scaling Applications
Scaling these applications can be done with these commands:
```text
# kubectl scale statefulsets <stateful-set-name> --replicas=<new-replicas>
# kubectl scale deployment.v1.apps/<deployment-name> --replicas=<new-replicas>
```

Also there is the Horizontal Pod AutoScaler, that may be useful to you as well.
```text
# kubectl autoscale deployment.v1.apps/<deployment-name> --min=10 --max=15 --cpu-percent=80
```

More information on scaling applications:
* [Horizontal Pod AutoScaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
* [Scale a StatefulSet Documentation](https://kubernetes.io/docs/tasks/run-application/scale-stateful-set/)
* [Deployments Documentation / Scaling a Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#scaling-a-deployment)