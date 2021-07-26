# Kubernetes
This repo provides tooling to deploy a highly available Kubernetes clusters.

# Node Management
Ansible playbooks are provided for `kubeadm` and `k3s` in the `playbooks/` directory of this repository. There are corresponding `README.md` files in their respective directories.

# Catalog
There are some useful manifests held in `catalog/` that aide with building scalable application. Ideally if you're using `Argo CD`, you should take the manifests you want and copy them to your own repo that you are using as a source of truth. That way you can change replica values in the repo.
