# kubernetes
This repo provides tooling to deploy a highly available k8s cluster.

The following gets installed by default:
* [metrics-server](https://github.com/kubernetes-sigs/metrics-server) - Resource metrics for Kubernetes built-in autoscaling pipelines
* [longhorn.io](https://longhorn.io/) - Cloud Native Storage
* [traefik](https://doc.traefik.io/traefik/) - An open-source Edge Router
* [klipper-lb](https://github.com/k3s-io/klipper-lb/) - A modified service load balancer that routes port 80 and 443 to traefik
* [resolve-host-patcher](https://github.com/scalabledelivery/resolve-host-patcher/) - Patches the resolvers on cluster hosts so that `*.cluster.local` will resolve in applied manifest
* [Argo CD](https://github.com/argoproj/argo-cd) - For GitOps deployments
* [Argo Workflows](https://github.com/argoproj/argo-workflows) - A workflow engine for Kubernetes that can be used for CI
* [Argo Events](https://github.com/argoproj/argo-events) - Used to trigger workflows

# Cluster Management
Documentation for managing the cluster.

## Adding Nodes
The process of creating clusters and adding nodes to a cluster has been simplified with the Ansible playbook, `playbooks/setup.yaml`.

All hosts must be `Ubuntu 20.04 LTS`.

Setup your inventory file like so.
```yaml
all:
  hosts:
    leader-01:
      ansible_host: 155.138.229.34
      ansible_port: 22
      ansible_user: root
      public_ip: 155.138.229.34
      private_ip: 10.6.96.3
      leader: yes
      # Needed if ansible_user != root
      #ansible_become_method: sudo
      #ansible_sudo_password: ....
    # ...
    worker-01:
      # ...
      leader: no
    # ...
```

Run the playbook against the inventory and it will do the following.

* If Kubernetes is not setup, the playbook will create the cluster.
* If Kubernetes is setup, it will add new leaders & workers to the cluster.
* If Kubernetes is setup and all the nodes are setup, it will pretty much do nothing.

## Running Setup Playbook
Do the full setup.
```text
$ ansible-playbook -i inventory.yaml playbooks/setup.yaml
```
Do the setup, but without traefik or the internal CA.
```text
$ ansible-playbook -i inventory.yaml playbooks/setup.yaml -e setup_internal_ca=no -e apply_traefik=no
```

### Setup Options
Below are the options you can pass with `-e` for the setup playbook.

`setup_internal_ca` Default: `yes`
* This will create a root CA that pods can use to make self-signed certs via an init container. This is useful for setting up registries that are only cluser accessible.

`apply_metrics_server` Default: `yes`
* Install the metrics server.

`apply_longhorn` Default: `yes`
* Install the longhorn.io CSI.

`apply_traefik` Default: `yes`
* Install traefik and the service load balancer.

`apply_argo_workflows` Default: `yes`
* Install Argo Workflows.

`apply_argo_cd` Default: `yes`
* Install Argo CD.

`apply_argo_events` Default: `yes`
* Install Argo Events.

`apply_resolve_host_patcher` Default: `yes`
* Install resolve host patcher.

`kubernetes_version` Default: `1.21.2-00`
* Set the kubernetes version.



## Removing Nodes
TODO: Needs cleanup and further explanation.

These are very rough notes for removing a node. Not all these steps are neccessary all the time. Sometimes a node may be stuck on finalizers and forceful removal is neccessary. Doing this wrong can result in data loss.
```text
$ kubectl drain NODE_TO_REMOVE
$ kubectl cordon NODE_TO_REMOVE
$ kubectl delete node NODE_TO_REMOVE
$ kubectl get nodes
$ kubectl edit NODE_TO_REMOVE
$ kubectl node-shell LEADER_NODE_NAME
# etcdctl member list
# etcdctl member remove 1234567890ABCDEF
$ kubectl get nodes
$ kubectl delete node NODE_TO_REMOVE
$ kubectl get nodes
$ kubectl edit node NODE_TO_REMOVE # Remove the finalizers.
$ kubectl get nodes
```

## Upgrades
TODO: Make playbook for upgrades.


# Dashboards
While it is possible to expose these services, it is not recommended. Keeping them internal to the cluster and only accessing them via `kubectl port-foward` adds a layer of security.

When visiting some of these dashboards, self signed certificates are in use. It is recommended to use chrome and allow insecure localhost from `chrome://flags/#allow-insecure-localhost`.

## Longhorn
```text
$ kubectl -n longhorn-system port-forward svc/longhorn-frontend 8100:80
```
URL: http://localhost:8100

## Traefik
```text
$ kubectl -n traefik-system port-forward svc/traefik-ingress-controller-dashboard-service 8101:8080
```
URL: http://localhost:8101

## Argo CD
```text
$ kubectl -n argocd port-forward svc/argocd-server 8102:443
```
URL: https://localhost:8102

## Argo Workflows
```text
$ kubectl -n argo port-forward svc/argo-server 8103:2746
```
URL: https://localhost:8103

# Argo for CI/CD
This cluster setup uses Argo for CI/CD pipelining from git based sources.

`Argo Workflows` is what you would use for CI. It can do pretty much any workload you want. Check out some workflow examples here [here](https://argoproj.github.io/argo-workflows/examples/).

Argo CD handles deployments for you. The user guide can be found [here](https://argo-cd.readthedocs.io/en/stable/user-guide/).

## Getting Credentials
For Argo CD.
```text
$ echo $(kubectl -n argocd get secrets argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -D)
```

For Argo Workflows.
```text
$ echo $(kubectl -n argo get secrets argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -D)
```

## Argo Events
You can find some examples [here](https://github.com/argoproj/argo-events/tree/stable/examples). You may want to setup a webhook to trigger workflows.

# Catalog
There are some useful manifests held in `catalog/` that aide with building scalable application. Ideally if you're using `Argo CD`, you should take the manifests you want and copy them to your own repo that you are using as a source of truth. That way you can change replica values in the repo.
