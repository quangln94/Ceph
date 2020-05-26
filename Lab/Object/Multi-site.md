# Cấu hình Multi Site

## 1. Cấu hình Master Zone

### 1.1 Tạo RGW keyring trong `/etc/ceph` và kiểm tra
```sh
cp /var/lib/ceph/radosgw/ceph-rgw.ceph01/keyring /etc/ceph/ceph.client.rgw.ceph01.keyring
cat /etc/ceph/ceph.client.rgw.ceph01.keyring
ceph -s --id rgw.ceph01
```

### 1.2 Tạo RGW multi-site v2 realm. 

Chạy lệnh sau trên Node `ceph01` để tạo `realm`:
```sh
radosgw-admin realm create --rgw-realm=movies
```

### 1.3 Tạo Master Zone group. 

1 RGW realm phải có ít nhật 1 RGW zone group, nó sẽ phục vụ như Master zone group cho realm. Chạy command sau trên Node `ceph01` để tạo Master zone group
```sh
radosgw-admin zonegroup create --rgw-zonegroup=vn \
--endpoints=http://192.168.20.143:8081 \
--rgw-realm=movies --master --default \
--id rgw.ceph01
```

### 1.4. Tạo Master zone

1 RGW zone group phải có ít nhất 1 RGW zone. Chạy command sau trên Node `ceph01` để tạo Master zone:
```sh
radosgw-admin zone create --rgw-zonegroup=vn \
--rgw-zone=ceph01 --master --default \
--endpoints=http://192.168.20.143:8081 \
--id rgw.ceph01
```

### 1.5. Remove default zone group và zone information từ Clvnter 1:
```sh
radosgw-admin zonegroup remove --rgw-zonegroup=default --rgw-zone=default --id rgw.ceph01
radosgw-admin zone delete --rgw-zone=default --id rgw.ceph01
radosgw-admin zonegroup delete --rgw-zonegroup=default --id rgw.ceph01
```

Update period với e new us zone group and ceph01 zone which will be used for multi-site v2:
```sh
radosgw-admin period update --commit --id rgw.ceph01
```
### 1.6 Remove RGW default pools:
```sh
for i in `ceph osd pool ls --id rgw.ceph01 | grep default.rgw`; do ceph osd pool delete $i $i --yes-i-really-really-mean-it --id rgw.ceph01; done
```

### 1.7 Create 1 RGW multi-site v2 system user. 

Trong Master zone, tạo 1 system user để thực hiện authentication giữa multi-site radosgw daemons:
```sh
radosgw-admin user create --uid="replication-user" \
--display-name="Multisite v2 replication user" \
--system --id rgw.ceph01
```
### 1.8 Cuối cùng, update the period với thông tin system user này:
```sh
radosgw-admin zone modify --rgw-zone=ceph01 \
--access-key=F3AI5H7WFWMT8AAEHSBR \
--secret=joPuBTpLvOb6vj7Q5iXjq9QWvTsTqlHdMcKgCV9s \
--id rgw.ceph01

radosgw-admin period update --commit --id rgw.ceph01
```
### 1.9 Update section `[client.rgw.ceph01]` trong file `ceph.conf` với `rgw_zone=ceph01`:
```sh
[client.rgw.ceph01]
rgw_zone=ceph01
```
### 1.10 Restart `ceph01` RGW daemon:
```sh
systemctl restart ceph-radosgw.target
```

## 2. Cấu hình Secondary zone

### 2.1 Tạo RGW keyring trong `/etc/ceph` và kiểm tra
```sh
cp /var/lib/ceph/radosgw/ceph-rgw.ceph02/keyring /etc/ceph/ceph.client.rgw.ceph02.keyring
cat /etc/ceph/ceph.client.rgw.ceph02.keyring
ceph -s --id rgw.ceph02
```
### 2.2 Đầu tiên, pull RGW realm.

Phải sử dụng RGW endpoint URL và access key và secret key của master zone trong master zone group để pull realm tới Node secondary zone RGW:

radosgw-admin realm pull \
--url=http://192.168.20.141:8081 \
--access-key=F3AI5H7WFWMT8AAEHSBR \
--secret=joPuBTpLvOb6vj7Q5iXjq9QWvTsTqlHdMcKgCV9s \
--id rgw.ceph01
