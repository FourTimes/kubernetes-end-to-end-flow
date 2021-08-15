Download the etcd binaries

```sh
VERION=3.5.0
wget https://storage.googleapis.com/etcd/v$VERION/etcd-v$VERION-linux-amd64.tar.gz 
tar -xvzf etcd-v$VERION-linux-amd64.tar.gz -C /tmp/
sudo mv /tmp/etcd-v$VERION-linux-amd64/etcd* /usr/local/bin/

```

Create the etcd data and configuration directory
```sh

sudo mkdir -p /etc/etcd/ssl /var/lib/etcd

```

Install the packages
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
user creation for etcd

```sh
useradd -c "etcd user" -d /var/lib/etcd -s /bin/false etcd
chown -R etcd:etcd /var/lib/etcd
chown -R etcd:etcd /etc/etcd/ssl
```










