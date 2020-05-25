# Cấu hình Multi Site

## 1. Cấu hình Master Zone

### 1.1 Tạo RGW keyring trong `/etc/ceph` và kiểm tra
```sh
cp /var/lib/ceph/radosgw/ceph-rgw.us-east-1/ keyring /etc/ceph/ceph.client.rgw.us-east-1.keyring
cat /etc/ceph/ceph.client.rgw.us-east-1.keyring
ceph -s --id rgw.us-east-1
```

### 1.2 Tạo RGW multi-site v2 realm. 

Chạy lệnh sau trên Node `useast-1` để tạo `realm`:
```sh
radosgw-admin realm create --rgw-realm=cookbookv2
```

### 1.3 Tạo Master Zone group. 

1 RGW realm phải có ít nhật 1 RGW zone group, nó sẽ phục vụ như Master zone group cho realm. Chạy command sau trên Node `us-east-1` để tạo Master zone group
```sh
radosgw-admin zonegroup create --rgw-zonegroup=us
--endpoints=http://us-east-1.cephcookbook.com:8080
--rgw-realm=cookbookv2 --master --default
--id rgw.us-east-1
```

### 1.4. Tạo Master zone

1 RGW zone group phải có ít nhất 1 RGW zone. Chạy command sau trên Node `us-east-1` để tạo Master zone:
```sh
radosgw-admin zone create --rgw-zonegroup=us
--rgw-zone=us-east-1 --master --default
--endpoints=http://us-east-1.cephcookbook.com:8080
--id rgw.us-east-1
```

### 1.5. Remove default zone group và zone information từ Cluster 1:
```sh
$ radosgw-admin zonegroup remove --rgw-zonegroup=default --rgw-zone=default --id rgw.us-east-1
$ radosgw-admin zone delete --rgw-zone=default --id rgw.us-east-1
$ radosgw-admin zonegroup delete --rgw-zonegroup=default --id rgw.us-east-1
```
### 1.6 Remove RGW default pools:
```sh
for i in `ceph osd pool ls --id rgw.us-east-1 | grep default.rgw`; do ceph osd pool delete $i $i --yes-i-really-really-mean-it --id rgw.us-east-1; done
```

### 1.7 Create 1 RGW multi-site v2 system user. 

Trong Master zone, tạp 1 system user để thực hiện authentication giữa multi-site radosgw daemons:
```sh
radosgw-admin user create --uid="replication-user"
--display-name="Multisite v2 replication user"
--system --id rgw.us-east-1
```
### 1.8 Cuối cùng, update the period với thông tin system user này:
```sh
$ radosgw-admin zone modify --rgw-zone=us-east-1
--access-key=ZYCDNTEASHKREV4X9BUJ
--secret=4JbC4OC4vC6fy6EY6Pfp8rPZMrpDnYmETZxNyyu9
--id rgw.us-east-1
$ radosgw-admin period update --commit --id rgw.us-east-1
```
### 1.9 Update section `[client.rgw.us-east-1]` trong file `ceph.conf` với `rgw_zone=us-east-1`:
```sh
[client.rgw.us-east-1]
rgw_zone=us-east-1
```
### 1.10 Restart `us-east-1` RGW daemon:
```sh
systemctl restart ceph-radosgw.target
```

## 2. Cấu hình Secondary zone
