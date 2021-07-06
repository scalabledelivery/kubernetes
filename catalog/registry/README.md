# Registry
This example `deploy.yaml` is designed to create an internal registry for clusters setup with [this playbook](https://github.com/scalabledelivery/kubernetes/blob/master/playbooks/setup.yaml). The playbook deploys a CA certificate and key for generating valid SSL certificates.

# Pushing Container Images
The conifiguration in the example is just a self hosted registry. If you've reconfigured this to be a [mirror](https://docs.docker.com/registry/recipes/mirror/), this section does not apply to you.

To simplify pushing to this registry with Docker for Desktop, open the settings and go to `Docker Engine`. Add this entry with your own hostname:
```text
"insecure-registries": [
    "MY-COMPUTER-8675309.local:5000"
  ],
```

Port forward to the internal service.
```text
kubectl port-forward --address 0.0.0.0 5000:5000
```

Now you can push images to the repository `MY-COMPUTER-8675309.local:5000/some/path/here`