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
- [Link tham khảo](https://docs.ceph.com/docs/octopus/rados/operations/health-checks/#osd-out-of-order-full)

## 2. Lỗi đồng bộ thời gian giữa các máy chủ ceph-mon
### 2.1 Nguyên nhân
Có thể do 1 số nguyên nhân sau:
- Do thời gian giữa các máy chủ không đồng bồ
- Do sai lệch thời gian nhỏ hơn phút
### 2.2 Cách khắc phục
- Cài đặt và kiểm tra dịch vụ NTP trên các máy chủ ceph-mon
- Check log của ceph-mon thấy output tương tự như sau:
<img src=https://i.imgur.com/sJDYCkc.png>

Log này là do mặc định hẹ thống chỉ cho phép sai lệch không quá 0.05s. Để khắc phục việc này, đặt tham số `mon clock drift allowed` trong section `global` thỏa mã điều kiện tương tự như sau: ( Ví dụ ở đây sai lệnh trong khoảng <1s)

```sh
[global]
mon clock drift allowed = 1
```
