# Sizing

Khi sử dụng HDD kết hợp SSD, điều quan trọng là tạo `block.db` đủ lớn cho BlueStore. Thông thường `block.db` thường lớn nhất có thể.

Khuyến nghị chung là kích thước `block.db` thường  từ 1% - 4% của kích thước `block `
The general recommendation is to have block.db size in between 1% to 4% of block size. Với RGW workloads, được khuyến nghĩ rằng kích thước `block.db` không nhỏ hơn 4% của kích thước `block` bởi vì RGW sử dụng nhiều để lưu metadata. Ví dụ: kích thước block là 1TB thì kích thước `block.db` không nhỏ hơn 40GB. Với RBD workloads, 1% - 2% của block size thường là đủ.

Nếu không kết hợp HDD và SSD thì không cần thiết tạo 2 logical volumes riêng biệt cho `block.db` (hoặc `block.wal`). Bluestore sẽ từ quản lý trong không gian của `block`.

## Tự động Sizing cache

Bluestore có thể tự động thay đổi kích thước cache khi tc_malloc được cấu hình như là bộ cấp phát bộ nhớ và `bluestore_cache_autotune setting is enabled`, đây là tùy chọn mặc định. Bluestore sẽ duy trì việc sử dụng OSD heap memory theo kích thước được chỉ định qua tùy chọn `osd_memory_target`. Đây là thuật thoán tốt nhất và cache sẽ không nhỏ hơn giá trị chỉ định trong `osd_memory_cache_min`. Tỉ lệ cache sẽ đươch chọn dựa trên hệ thống phân cấp ưu tiên. Nếu thông tin ưu tiên không có sẵn,`bluestore_cache_meta_ratio` và `bluestore_cache_kv_ratio` được sử dụng dự phòng.

## Sizing thủ công

Lương bộ nhớ sử dụng của mỗi OSD cho BlueStore’s cache được xác đinh trong cấu hình `bluestore_cache_size`. Nếu không được set thì mặc định là 0, đây là 1 giá trị mặc định khác được sử dụng dựa vào việc sử dụng HDD hay SSD cho thiết bị Primary ( `bluestore_cache_size_ssd` `bluestore_cache_size_hdd`).

cache memory budget được cấu hình có thể sử dụng trong 1 số cách khác nhau:
- Key/Value metadata (i.e., RocksDB’s internal cache)
- BlueStore metadata
- BlueStore data (i.e., recently read or written object data)


Việc sử dụng bộ nhớ cache bị chi phối bởi các tùy chọn sau: `bluestore_cache_meta_ratio` và `bluestore_cache_kv_ratio`. Fraction của cache cho data bị chi phối bởi kích thước bluestore cache (dựa vào `bluestore_cache_size[_ssd|_hdd]` và device class của Primary device) cũng như tỷ lệ meta và kv. The data fraction can be calculated by <effective_cache_size> * (1 - bluestore_cache_meta_ratio - bluestore_cache_kv_ratio)

data fraction có thể được tính bằng:
```sh
<effective_cache_size> * (1 - bluestore_cache_meta_ratio - bluestore_cache_kv_ratio)
```

## Checksums

## Tài liệu tham khảo
- https://docs.ceph.com/docs/octopus/rados/configuration/bluestore-config-ref/
