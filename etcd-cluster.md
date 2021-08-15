Install the packages in Master01

```sh
sudo apt install golang-cfssl -y
```

Create the master and etcd certificates

```sh
# This CONFIGURATION used for kubernetes

cat <<EOF > /etc/etcd/ssl/ca-config.json
{
  "signing": {
    "default": {
      "expiry": "876000h"
    },
    "profiles": {
      "kubernetes": {
        "usages": ["signing", "key encipherment", "server auth", "client auth"],
        "expiry": "876000h"
      }
    }
  }
}
EOF
```

```sh
# This CSR used for kubernetes
cat <<EOF > /etc/etcd/ssl/ca-csr.json
{
  "CN": "kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "IN",
      "ST": "TN",
      "L": "CHE",
      "O": "DDND",
      "OU": "DDND"
    }
  ]
}
EOF
```

```sh
# This CSR used for ETCD
cat <<EOF > /etc/etcd/ssl/etcd-csr.json
{
  "CN": "etcd",
  "hosts": [
    "127.0.0.1",
    "*.dodonotdo.in"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "IN",
      "ST": "TN",
      "L": "CHE",
      "O": "DDND",
      "OU": "DDND"
    }
  ]
}
EOF
```

certificate creation process

```sh
cd /etc/etcd/ssl
cfssl gencert -initca ca-csr.json | cfssljson -bare ca 
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes etcd-csr.json | cfssljson -bare etcd 

```
Create the etcd systemd service (run all etcd nodes)

```sh
# user creation for etcd
useradd -c "etcd user" -d /var/lib/etcd -s /bin/false etcd
chown -R etcd:etcd /var/lib/etcd
chown -R etcd:etcd /etc/etcd/ssl

# set hostname variable for etcd
master1=master1.dodonotdo.in
master2=master2.dodonotdo.in
master3=master3.dodonotdo.in

# Install the etcd

VERION=3.5.0
wget https://storage.googleapis.com/etcd/v$VERION/etcd-v$VERION-linux-amd64.tar.gz 
tar -xvzf etcd-v$VERION-linux-amd64.tar.gz -C /tmp/
sudo mv /tmp/etcd-v$VERION-linux-amd64/etcd* /usr/local/bin/

# create the data and configuration directory

sudo mkdir -p /etc/etcd/ssl /var/lib/etcd


cat <<EOF > /etc/systemd/system/etcd.service
[Unit]
Description=etcd service
Documentation=https://github.com/coreos/etcd
 
[Service]
User=etcd
Type=notify
ExecStart=/usr/local/bin/etcd \\
 --name $(hostname) \\
 --data-dir /var/lib/etcd \\
 --cert-file=/etc/etcd/ssl/etcd.pem \\
 --key-file=/etc/etcd/ssl/etcd-key.pem \\
 --peer-cert-file=/etc/etcd/ssl/etcd.pem \\
 --peer-key-file=/etc/etcd/ssl/etcd-key.pem \\
 --trusted-ca-file=/etc/etcd/ssl/ca.pem \\
 --peer-trusted-ca-file=/etc/etcd/ssl/ca.pem \\
 --initial-advertise-peer-urls https://$(hostname):2380 \\
 --listen-peer-urls https://$(hostname):2380 \\
 --listen-client-urls https://$(hostname):2379,http://127.0.0.1:2379 \\
 --advertise-client-urls https://$(hostname):2379 \\
 --initial-cluster-token etcd-cluster-1 \\
 --initial-cluster master01=https://$master1:2380,master02=https://$master2:2380,master03=https://$master3:2380 \\
 --initial-cluster-state new \\
 --heartbeat-interval 1000 \\
 --election-timeout 5000
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target
EOF
```








