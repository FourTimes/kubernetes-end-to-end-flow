# kubernetes-end-to-end-flow

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
