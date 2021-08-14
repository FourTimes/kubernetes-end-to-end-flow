[kubernetes-end-to-end-flow](https://kubernetes.io/docs/concepts/)

| Server name | CPU | Memory | Operating system | IP address |
| --------------- | --------------- | --------------- | --------------- |--------------- |
| master1.dodonotdo.io | 2 CPU | 4GB RAM | Ubuntu 20+ | 10.0.0.3 |
| master2.dodonotdo.io | 2 CPU | 4GB RAM | Ubuntu 20+ | 10.0.0.4 |
| master3.dodonotdo.io | 2 CPU | 4GB RAM | Ubuntu 20+ | 10.0.0.5 |
| worker1.dodonotdo.io | 2 CPU | 4GB RAM | Ubuntu 20+ | 10.0.0.6 |
| worker2.dodonotdo.io | 2 CPU | 4GB RAM | Ubuntu 20+ | 10.0.0.7 |


[Master node](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)

| Protocol | Direction | Port Range | Purpose | Used By |
| --------------- | --------------- | --------------- | --------------- | --------------- |
| TCP | Inbound | 6443* | Kubernetes API server | All  |
| TCP | Inbound | 2379-2380 | etcd server client API | kube-apiserver, etcd |
| TCP | Inbound | 10250 | Kubelet API | Self, Control plane |
| TCP | Inbound | 10251 | kube-scheduler | Self |
| TCP | Inbound | 10252 |  kube-controller-manager | Self |


[Worker node](https://kubernetes.io/docs/concepts/overview/components/#node-components)


| Protocol | Direction | Port Range | Purpose | Used By |
| --------------- | --------------- | --------------- | --------------- | --------------- |
| TCP | Inbound | 10250 | Kubelet API | Self, Control plane  |
| TCP | Inbound | 30000-32767 | NodePort Services | All |


[Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)

Key |	Description |	Example | 	Type |
| --------------- | --------------- | --------------- | --------------- |
app.kubernetes.io/name |	The name of the application |	mysql |	string
app.kubernetes.io/instance |	A unique name identifying the instance of an application |	mysql-abcxzy |	string
app.kubernetes.io/version |	The current version of the application (e.g., a semantic version, revision hash, etc.) |	5.7.21 |	string
app.kubernetes.io/component |	The component within the architecture |	database |	string
app.kubernetes.io/part-of |	The name of a higher level application this one is part of | 	wordpress |	string
app.kubernetes.io/managed-by |	The tool being used to manage the operation of an application |	helm |	string
app.kubernetes.io/created-by |	The controller/user who created this resource |	controller-manager |	string

















