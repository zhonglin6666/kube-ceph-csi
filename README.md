# 1. RBAC

# 2. POD

# 3. 

### 3.1 set secret for 


# Test RBD CSI Driver

## Create RBD StorageClass

```console
kubectl create -f test-csi-rbd/storegeclass.yaml
```

## Create RBD Secret

Create a Secret that matches `adminid` or `userid`

Then create a Secret using admin and `kubernetes` keyrings:

you need your Ceph admin/user password encoded in base64.


Run `ceph auth ls` in your rook ceph operator pod, to encode the key of your
admin/user run `echo -n KEY|base64`
and replace `BASE64-ENCODED-PASSWORD` by your encoded key.

```bash
# ceph auth get-or-create-key client.kubernetes mon "allow profile rbd" osd "profile rbd pool=rbd"
AQBS3thcXPhNGhAAM7CC2v2xHVPMAbq6xezLDQ==

# echo -n AQBS3thcXPhNGhAAM7CC2v2xHVPMAbq6xezLDQ==|base64
QVFCUzN0aGNYUGhOR2hBQU03Q0MydjJ4SFZQTUFicTZ4ZXpMRFE9PQ==
```


```bash
# ceph auth ls
installed auth entries:

osd.0
	key: AQBQ2Nhcahc4KRAAZxudpHoKQYgYHoWuw+GIgw==
	caps: [mgr] allow profile osd
	caps: [mon] allow profile osd
	caps: [osd] allow *
osd.1
	key: AQAt2dhcaY+bIxAA1dUUe+gwFZci+g9Ul4cQxw==
	caps: [mgr] allow profile osd
	caps: [mon] allow profile osd
	caps: [osd] allow *
client.admin
	key: AQA119hcfv1gHhAAei7Avf6LOl2TD6V9UsfQ5Q==
	caps: [mds] allow *
	caps: [mgr] allow *
	caps: [mon] allow *
	caps: [osd] allow *
client.bootstrap-mds
	key: AQBK19hcc2EhKhAAAs95zcguOHUa2A8PbKmBsQ==
	caps: [mon] allow profile bootstrap-mds
client.bootstrap-mgr
	key: AQBK19hcooghKhAATAMRKMMh36NstuwIhShoZw==
	caps: [mon] allow profile bootstrap-mgr
client.bootstrap-osd
	key: AQBK19hcPK4hKhAAyvXv7mzHWrsTS+g8QqetaA==
	caps: [mon] allow profile bootstrap-osd
client.bootstrap-rbd
	key: AQBK19hcdNQhKhAAKRtz9Imcxo+IhdZdhn5CLA==
	caps: [mon] allow profile bootstrap-rbd
client.bootstrap-rbd-mirror
	key: AQBK19hcPPshKhAAxXji2D5odpfuABj0cI432w==
	caps: [mon] allow profile bootstrap-rbd-mirror
client.bootstrap-rgw
	key: AQBK19hcSyAiKhAASCU/hSp7QkpZSJLSUAY81Q==
	caps: [mon] allow profile bootstrap-rgw
mgr.master
	key: AQCw19hcrBDsGRAAKTpMeGaTjNkZ5hnqF2vTIQ==
	caps: [mds] allow *
	caps: [mon] allow profile mgr
	caps: [osd] allow *

#encode admin/user key
sh-4.2# echo -n AQA119hcfv1gHhAAei7Avf6LOl2TD6V9UsfQ5Q==|base64
QVFBMTE5aGNmdjFnSGhBQWVpN0F2ZjZMT2wyVEQ2VjlVc2ZRNVE9PQ==
#or
sh-4.2# ceph auth get-key client.admin|base64
QVFBMTE5aGNmdjFnSGhBQWVpN0F2ZjZMT2wyVEQ2VjlVc2ZRNVE9PQ==
```

```console
kubectl create -f test-csi-rbd/secret.yaml
```
```
