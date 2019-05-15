# 1. RBAC

```
rbd rbac
# kubectl create -f rbac-rbd/
ceph rbac
# kubectl create -f rbac-cephfs/
```

# 2. start ceph process
### 2.1 

# 3. Test RBD CSI Driver

### 3.1 set secret for RBD
Create a Secret that matches `adminid` or `userid`

Then create a Secret using admin and `kubernetes` keyrings:

you need your Ceph admin/user password encoded in base64.

```bash
# ceph auth get-or-create-key client.kubernetes mon "allow profile rbd" osd "profile rbd pool=rbd"
AQBS3thcXPhNGhAAM7CC2v2xHVPMAbq6xezLDQ==

# echo -n AQBS3thcXPhNGhAAM7CC2v2xHVPMAbq6xezLDQ==|base64
QVFCUzN0aGNYUGhOR2hBQU03Q0MydjJ4SFZQTUFicTZ4ZXpMRFE9PQ==
```

```bash
sh-4.2# ceph auth get-key client.admin|base64
QVFBMTE5aGNmdjFnSGhBQWVpN0F2ZjZMT2wyVEQ2VjlVc2ZRNVE9PQ==
```

```console
kubectl create -f test-csi-rbd/secret.yaml
```
 

### 3.2 create RBD StorageClass

```console
kubectl create -f test-csi-rbd/storegeclass.yaml
```

### 3.3 create pvc with storageclass

```console
kubectl create -f test-csi-rbd/pvc.yaml
```









