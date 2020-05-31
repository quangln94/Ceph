## Sự khác nhau giữa `GLOBAL` và `POOL`

Trong một số trường hợp, dung lượng sử dụng thật của disk nhỏ hơn dung lượng `USED` nhân với số bản copy trong tất cả các pool.

Xem xét ví dụ sau:

1 Cụm Cehp Cluster với thông số `ceph df` như sau:
```sh
$ ceph df
RAW STORAGE:
    CLASS     SIZE       AVAIL      USED        RAW USED     %RAW USED
    hdd       60 GiB     58 GiB     4.6 MiB      2.0 GiB          3.34
    TOTAL     60 GiB     58 GiB     4.6 MiB      2.0 GiB          3.34

POOLS:
    POOL                   ID     STORED     OBJECTS     USED     %USED     MAX AVAIL
    images                  1        0 B           0      0 B         0        27 GiB
    cephfs.cephfs.meta      2        0 B           0      0 B         0        27 GiB
    cephfs.cephfs.data      3        0 B           0      0 B         0        27 GiB
```
Trong đó:
- `GLOABAL`: `USER=4.6 MiB`
- `POOL` : Pool `images` với `USED=0 B`

Tạo 1 Object có kích thước 4MB và đặt vào trong pool `images`:
```sh
seq 1 4000 |xargs -I {} -P 10 dd if=/dev/zero of={} bs=1K seek=4095 count=1
```

- Sử dụng lệnh `ceph df` để kiểm tra:

```sh
$ ceph df
RAW STORAGE:
    CLASS     SIZE       AVAIL      USED        RAW USED     %RAW USED
    hdd       60 GiB     58 GiB     8.6 MiB      2.0 GiB          3.35
    TOTAL     60 GiB     58 GiB     8.6 MiB      2.0 GiB          3.35

POOLS:
    POOL                   ID     STORED     OBJECTS     USED      %USED     MAX AVAIL
    images                  1      4 MiB           1     4 MiB         0        55 GiB
```

Trong đó:
- `GLOBAL`: `USED=8.6 MiB` tằng
## Tài liệu tham khảo
- https://documentation.suse.com/ses/6/html/ses-all/ceph-monitor.html
- https://www.lagou.com/lgeduarticle/42306.html
- https://www.itdaan.com/tw/af35d47b5fbbd24df723f94e81f8aef5
- https://bean-li.github.io/ceph-space/
- https://www.codetd.com/en/article/7420688
- https://juejin.im/entry/5bebf831f265da614c4c5bfd
- https://docs.ceph.com/docs/master/releases/nautilus/
