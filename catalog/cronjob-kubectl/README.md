# CronJob - kubectl
This `deploy.yaml` shows an example of using the `bitnami/kubectl` container to do administrative tasks against your cluster.

You should edit `spec.jobTemplate.spec.template.spec.containers[0].command` accordingly, it is an inline script that you can build out to do whatever you  need.

To test permissions and such, you can break `spec.jobTemplate.spec.template` out and put it into a static pod template for testing.
