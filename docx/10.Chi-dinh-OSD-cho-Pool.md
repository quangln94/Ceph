# Chỉ định OSD cho Pool

## 1. Lấy CRUSH map và decompile:
```sh
ceph osd getcrushmap -o crushmapdump
crushtool -d crushmapdump -o crushmapdump-decompiled
```
## 2. Sửa file CRUSH map `crushmapdump-decompiled` và thêm các section sau section `root default`:
```sh
$ vim crushmapdump-decompiled
...
root ssd {
         id -5
         alg straw
         hash 0
         item osd.0 weight 0.010
         item osd.3 weight 0.010
         item osd.6 weight 0.010
}

root sata {
          id -6
          alg straw
          hash 0
          item osd.1 weight 0.010
          item osd.4 weight 0.010
          item osd.7 weight 0.010
}
```
## 3. Tạo CRUSH rule bằng cách thêm rules bên dưới section `rule`của CRUSH map:
```sh
rule ssd-pool {
         ruleset1
         type replicated
         min_size 1
         max_size 10
         step take ssd
         step chooseleaf firstn 0 type ssd
         step emit
}

rule sata-pool {
         ruleset2
         type replicated
         min_size 1
         max_size 10
         step take ssd
         step chooseleaf firstn 0 type ssd
         step emit
}
```
## 4. Compile và inject new CRUSH map trong Ceph cluster:
```sh
crushtool -c crushmapdump-decompiled -o crushmapdump-compiled
ceph osd setcrushmap -i crushmapdump-compiled
```
***Lưu ý: Thêm Option `osd_crush_update_on_start=false` vào section `[global]` hoặc `[osd]` trong `ceph.conf`***

## 5. Khi CRUSH map được applied, kiểm tra OSD tree:
```
ceph osd tree
```
## 6. Tạo và verify `ssd-pool`.
- Tạo `ssd-pool`:
```sh
ceph osd pool create ssd-pool 8 8
```
- Verify `ssd-pool`; chú ý rằng mặc định `crush_ruleset 0`:
```sh
ceph osd dump | grep -i ssd
```
- Thay đổi `crush_ruleset 1`, pool được tạo trên SSD:
```sh
ceph osd pool set ssd-pool crush_ruleset 1
```
- Verify pool và thông báo thay đổi trong `crush_ruleset`:
```sh
ceph osd dump | grep -i ssd
```
## 7. Tạo và verify `sata-pool`.
- Tạo `sata-pool`:
```sh
ceph osd pool create sata-pool 8 8
```
- Verify `sata-pool`; chú ý rằng mặc định `crush_ruleset 0`:
```sh
ceph osd dump | grep -i sata
```
- Thay đổi `crush_ruleset 2`, pool được tạo trên Sata:
```sh
ceph osd pool set sata-pool crush_ruleset 2
```
- Verify pool và thông báo thay đổi trong `crush_ruleset`:
```sh
ceph osd dump | grep -i sata
```
## 8. Add object vào pool
```sh
rados -p ssd-pool put dummy_object1 /etc/hosts
rados -p sata-pool put dummy_object1 /etc/hosts
```
## 9. Kiểm tra Object nằm trên OSD nào
```sh
ceph osd map ssd-pool dummy_object1
ceph osd map sata-pool dummy_object1
```
