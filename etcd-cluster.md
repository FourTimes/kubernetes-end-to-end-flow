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
