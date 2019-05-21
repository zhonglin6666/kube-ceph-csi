# 1. RBAC

```
rbd rbac
# kubectl create -f rbac-rbd/
ceph rbac
# kubectl create -f rbac-cephfs/
```

# 2. install ceph cluster

### 2.0 prepare
kubectl label nodes master-node ceph-mon=enabled

kubectl label nodes master-node ceph-mgr=enabled

kubectl label nodes master-node ceph-osd=enabled

kubectl label nodes node1 ceph-osd=enabled

```ecma script level 4
delete all nodes data and config
rm /etc/ceph/* -rf
rm /var/lib/ceph/* -rf
```


### 2.1 install ceph monitors
set env `MON_IP` with node ip address

set env `CEPH_PUBLIC_NETWORK` with node ip arrange
```console

kubectl apply -f ceph-install/ceph-mon.yaml
```


### 2.2 create osd ceph.keyring
```cassandraql
ceph auth get client.bootstrap-osd -o /var/lib/ceph/bootstrap-osd/ceph.keyring
```

### 2.3 transfer ceph config to other nodes
```cassandraql
scp -r /etc/ceph root@192.168.73.184:/etc/
```

### 2.4 transfer ceph.keyring to other nodes which need to install osd process
```cassandraql
scp -r /var/lib/ceph/bootstrap-osd 192.168.73.184:/var/lib/ceph/
```

### 2.5 install ceph mgr
```cassandraql
kubectl create -f ceph-install/ceph-mgr.yaml
``` 

### 2.6 install ceph osd
```cassandraql
kubectl create -f ceph-install/ceph-osd.yaml
``` 

```ecma script level 4
[lin@lin kube-ceph-csi]$ kubectl get pods -nkube-csi -o wide
NAME                        READY   STATUS    RESTARTS   AGE    IP               NODE          NOMINATED NODE   READINESS GATES
ceph-mgr-7d676d95d4-cpvgr   1/1     Running   0          34s    192.168.74.57    master-node   <none>           <none>
ceph-mon-66db844857-gt8v7   1/1     Running   0          2m6s   192.168.74.57    master-node   <none>           <none>
ceph-osd-9hp2p              1/1     Running   0          11s    192.168.74.57    master-node   <none>           <none>
ceph-osd-q7b9g              1/1     Running   0          11s    192.168.73.184   node1         <none>           <none>
```

### 2.7 create rbd pool
```ecma script level 4
ceph osd pool create rbd 128
```

```ecma script level 4
[root@master plugins]# ceph -s
  cluster:
    id:     231afd78-7a43-4634-9ae0-7606e14a828b
    health: HEALTH_WARN
            Degraded data redundancy: 128 pgs undersized
 
  services:
    mon: 1 daemons, quorum master (age 9m)
    mgr: master(active, since 7m)
    osd: 2 osds: 2 up (since 7m), 2 in (since 7m)
 
  data:
    pools:   1 pools, 128 pgs
    objects: 0 objects, 0 B
    usage:   2.0 GiB used, 18 GiB / 20 GiB avail
    pgs:     128 active+undersized
```


# 3. install ceph rbd csi plugin
```ecma script level 4
kubectl apply -f ./rbd
```

```ecma script level 4
[lin@lin kube-ceph-csi]$ kubectl get pods -nkube-csi
NAME                             READY   STATUS    RESTARTS   AGE
csi-rbdplugin-provisioner-0      3/3     Running   0          56s
csi-rbdplugin-srjmp              2/2     Running   0          56s
csi-rbdplugin-tj2h4              2/2     Running   0          56s
```


# 4. Test RBD CSI Driver

### 4.1 set secret for RBD
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
 

### 4.2 create RBD StorageClass

```console
kubectl create -f test-csi-rbd/storegeclass.yaml
```

### 4.3 create pvc with storageclass

```console
kubectl create -f test-csi-rbd/pvc.yaml
```
