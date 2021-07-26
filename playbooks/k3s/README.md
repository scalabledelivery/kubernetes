# k3s
This directory provides playbooks that manage a Kubernetes cluster deployed with `k3s`.


## Adding Nodes
The k3s playbooks separate hosts into groups.

To add a node, setup an inventory and test it. An example inventory is provided, `example-inventory.yaml`.
```text
$ ansible -i inventory.yaml -m ping all
```

Run the `setup.yaml` playbook against the inventory and it will do the following.

* If Kubernetes is not setup, the playbook will create the cluster.
* If Kubernetes is setup, it will add new leaders & workers to the cluster.
* If Kubernetes is setup and all the nodes are setup, it will pretty much do nothing.

## Running Setup Playbook
Do the full setup.
```text
$ ansible-playbook -i inventory.yaml playbooks/k3s/setup.yaml
```

Do the setup, but without traefik.
```text
$ ansible-playbook -i inventory.yaml playbooks/k3s/setup.yaml -e k3s_flags="--disable=traefik"
```

Do the setup but without traefik or local-storage.
```text
$ ansible-playbook -i inventory.yaml playbooks/k3s/setup.yaml -e k3s_flags="--disable=traefik,local-storage"
```

### Setup Options
Below are the options you can pass with `-e` for the setup playbook.

`k3s_flags` Default: ` `
* Will pass flags directly to all k3s master nodes.

`k3s_version` Default: `v1.21.2+k3s1`
* Set the kubernetes version.

`setup_internal_ca` Default: `yes`
* This will create a root CA that pods can use to make self-signed certs via an init container. This is useful for setting up registries that are only cluser accessible.

`apply_longhorn` Default: `yes`
* Install the longhorn.io CSI.

`apply_argo_workflows` Default: `no`
* Install Argo Workflows.

`apply_argo_cd` Default: `no`
* Install Argo CD.

`apply_argo_events` Default: `no`
* Install Argo Events.

`apply_resolve_host_patcher` Default: `yes`
* Install resolve host patcher.
