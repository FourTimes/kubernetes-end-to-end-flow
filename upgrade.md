1Login into the first master and upgrade the kubeadm tool only

```sh
apt-mark unhold kubeadm
apt update && apt install -y kubeadm=1.13.0-00 && apt-mark hold kubeadm
```

Verify the upgrade plan

```sh
kubeadm upgrade plan
```

Apply the upgrade plan

```sh
kubeadm upgrade plan apply v1.14.0
```

Update Kubelet and restart the service

```sh
apt-mark unhold kubelet && apt-get update && apt-get install -y kubelet=1.14.0-00 && apt-mark hold kubelet
systemctl restart kubelet
```




