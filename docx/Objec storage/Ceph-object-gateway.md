# Ceph Object Gateway

## 2. Cấu hình Pool

### 2.1 Pools
Ceph Object Gateway sử dụng 1 số pool cho các nhu cầu khác nhau, được liệt kê trong Zone object `radosgw-admin zone get`. Zone `default` được tạo mặc định với tên `default.rgw.` nhưng Multisite Configuration sẽ có multiple zones.

### 2.2 Tuning

Khi `radosgw` hoạt động trên 1 zone pool không tồn tại, nó sẽ tạo pool với giá trị mặc định with từ `osd pool default pg num` và `osd pool default pgp num`. Các giá trị mặc định này đủ cho 1 số pool, nhưng 1 số khác (đặc biệt là trong `placement_pools` cho bucket index và data) sẽ yêu cầu bổ sung. Khuyến nghị sử dụng [Ceph Placement Group’s per Pool Calculator](https://ceph.com/pgcalc/) để tính placement groups phù hợp cho pools. SeePoolsfor details on pool creation.

### 2.3 Pool Namespaces

Xuất hiện từ bản Luminous.

Pool names cụ thể cho 1 zone đặt như sau: `{zone-name}.pool-name`. Ví dụ: tên zone là: `us-east` sẽ có pool sau:
- .rgw.root
- us-east.rgw.control
- us-east.rgw.meta
- us-east.rgw.log
- us-east.rgw.buckets.index
- us-east.rgw.buckets.data

Một số pool được hợp nhất bằng cách sử dụng namespace. Ví dụ: pool `us-east.rgw.meta` sử dụng namespace:
```
"user_keys_pool": "us-east.rgw.meta:users.keys",
"user_email_pool": "us-east.rgw.meta:users.email",
"user_swift_pool": "us-east.rgw.meta:users.swift",
"user_uid_pool": "us-east.rgw.meta:users.uid",
```
## Tài liệu tham khảo
- https://www.bookstack.cn/read/ceph-en/83f8fe8d08b24484.md
