# Một số lỗi thường gặp:
## 1. Lỗi `full ratio(s) out of order`

<img src=https://i.imgur.com/gid0HOm.png>

Kiểm tra trạng thái của ceph bằng lệnh sau:
```sh
ceph health detail
```
<img src=https://i.imgur.com/4czR8lv.png>

Lỗi này do `osd_failsafe_full_ratio (0.97)` < `full_ratio (0.99)`

Điều chỉnh lại chỉ số `full_ratio (0.99)`< `osd_failsafe_full_ratio (0.97)` bằng lệnh sau:
```sh
ceph osd set-full-ratio 0.9
```
- [Link tham khảo](https://docs.ceph.com/docs/master/rados/operations/health-checks/#osd-out-of-order-full)
