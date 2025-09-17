### CKA certification!

  <p align="center">
    <img src="/images/Kubernetes.png" alt="CKA Training" style="width:150px; display:block; margin:auto;" />
  </p>

#### Q1: Upgrade Kubernetes
    Upgrade the current version of kubernetes from 1.32.0 to 1.33.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the controlplane node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.


    Upgrade controlplane node first and drain node node01 before upgrading it. Pods for gold-nginx should run on the controlplane node subsequently.

<details>
<summary>Solution</summary>

1. Upgrade controlplane node:
```bash
kubectl drain controlplane --ignore-daemonsets
kubeadm upgrade plan
kubeadm upgrade apply v1.33.0
kubectl uncordon controlplane
```

2. Upgrade node01:
```bash
kubectl drain node01 --ignore-daemonsets
kubeadm upgrade plan
kubeadm upgrade apply v1.33.0
kubectl uncordon node01
```

3. Reschedule gold-nginx deployment:
```bash
kubectl get pods -n gold-nginx -o wide
kubectl drain <old-node> --ignore-daemonsets
kubectl uncordon <new-node>
```
4. Verify the upgrade:
```bash
kubectl get nodes
kubectl get pods -n gold-nginx -o wide
```
</details>

#### Q2: Weight 15
    Print the names of all deployments in the `admin2406` namespace in the following format:

`DEPLOYMENT`   `CONTAINER_IMAGE`   `READY_REPLICAS`   `NAMESPACE`

`<deployment name>`   `<container image used>`   `<ready replica count>`   `<Namespace>`. The data should be sorted by the increasing order of the deployment name.

Example:
`DEPLOYMENT`   `CONTAINER_IMAGE`   `READY_REPLICAS`   `NAMESPACE`
    deploy0   nginx:alpine   1   admin2406
    Write the result to the file /opt/admin2406_data.txt

<details>

<summary>Solution</summary>

```bash
kubectl get deployments -n admin2406 -o custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[0].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace --sort-by=.metadata.name > /opt/admin2406_data.txt
```
</details>  

