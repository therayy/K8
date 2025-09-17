### CKA certification!

  <p align="center">
    <img src="/images/Kubernetes.png" alt="CKA Training" style="width:150px; display:block; margin:auto;" />
  </p>

#### Q1: Upgrade Kubernetes
Upgrade the current version of kubernetes from 1.32.0 to 1.33.0 exactly using the kubeadm utility. Make sure that the upgrade is carried out one node at a time starting with the controlplane node. To minimize downtime, the deployment gold-nginx should be rescheduled on an alternate node before upgrading each node.
</br>

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
</br>

`DEPLOYMENT`   `CONTAINER_IMAGE`   `READY_REPLICAS`   `NAMESPACE`

</br>

`<deployment name>`   `<container image used>`   `<ready replica count>`   `<Namespace>`. The data should be sorted by the increasing order of the deployment name.

</br>

> Example:
`DEPLOYMENT`   `CONTAINER_IMAGE`   `READY_REPLICAS`   `NAMESPACE`
    deploy0   nginx:alpine   1   admin2406
    Write the result to the file /opt/admin2406_data.txt

<details>

<summary>Solution</summary>

```bash
kubectl get deployments -n admin2406 -o custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[0].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace --sort-by=.metadata.name > /opt/admin2406_data.txt
```
</details>  

#### Q3: Weight 8
A kubeconfig file called `admin.kubeconfig` has been created in `/root/CKA`. There is something wrong with the configuration. Troubleshoot and fix it.

> Fix /root/CKA/admin.kubeconfig

<details>
<summary>Solution</summary>

```bash
# Check the kubeconfig file for errors
kubectl --kubeconfig=/root/CKA/admin.kubeconfig get nodes
# If there are errors, fix them. Common issues include:
# 1. Incorrect cluster server URL
# 2. Invalid certificate authority data
# 3. Incorrect user credentials
# 4. Context not set properly
# Example fix: Update the cluster server URL
kubectl config --kubeconfig=/root/CKA/admin.kubeconfig set-cluster my-cluster --server=https://<correct-server-url>
# Verify the fix
kubectl --kubeconfig=/root/CKA/admin.kubeconfig get nodes
```
</details>

#### Q4: Weight 12
Create a new deployment called `nginx-deploy`, with image `nginx:1.16` and `1` replica.
Next, upgrade the deployment to version `1.17` using `rolling update ` and add the annotation message
`Updated nginx image to 1.17`.

`Image: nginx:1.16`

> Task: Upgrade the version of the deployment to 1:17

<details>
<summary>Solution</summary>

```bash
kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1
kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
kubectl annotate deployment/nginx-deploy kubernetes.io/change-cause="Updated nginx image to 1.17"
```
</details>

#### Q5: Weight 20
A new deployment called `alpha-mysql` has been deployed in the `alpha` namespace. However, the pods are not running. Troubleshoot and fix the issue. The deployment should make use of the persistent volume `alpha-pv` to be mounted at `/var/lib/mysql` and should use the environment variable `MYSQL_ALLOW_EMPTY_PASSWORD=1` to make use of an empty root password.


> Important: Do not alter the persistent volume.

Troubleshoot and fix the issues

<details>
<summary>Solution</summary>

```bash
kubectl describe deployment alpha-mysql -n alpha
kubectl logs deployment/alpha-mysql -n alpha
kubectl edit deployment alpha-mysql -n alpha
```
</details>

#### Q6: Weight 10
Take the backup of ETCD at the location `/opt/etcd-backup.db` on the `controlplane` node.

> Troubleshoot and fix the issues

<details>
<summary>Solution</summary>

```bash
ETCDCTL_API=3 etcdctl snapshot save /opt/etcd-backup.db
```
</details>

#### Q7: Weight 20
Create a pod called `secret-1401` in the `admin1401` namespace using the busybox image. The container within the pod should be called `secret-admin` and should sleep for 4800 seconds.

The container should mount a read-only secret volume called `secret-volume` at the path `/etc/secret-volume`. The secret being mounted has already been created for you and is called `dotfile-secret`.


> Pod created correctly?

<details>
<summary>Solution</summary>

```bash
kubectl create namespace admin1401
kubectl create secret generic dotfile-secret --from-file=dotfile=/etc/secret-volume/dotfile
kubectl run secret-1401 --image=busybox --restart=Never --namespace=admin1401 --command -- sleep 4800
kubectl patch pod secret-1401 -n admin1401 -p '{"spec":{"containers":[{"name":"secret-admin","volumeMounts":[{"name":"secret-volume","mountPath":"/etc/secret-volume","readOnly":true}]}]}}}'
kubectl get pod secret-1401 -n admin1401
```
</details>

