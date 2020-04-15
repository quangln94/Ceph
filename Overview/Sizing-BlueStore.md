# Sizing

Khi sử dụng HDD kết hợp SSD, điều quan trọng là tạo `block.db` đủ lớn cho BlueStore. Thông thường `block.db` thường lớn nhất có thể.

Khuyến nghị chung là kích thước `block.db` thường  từ 1% - 4% của kích thước `block `
The general recommendation is to have block.db size in between 1% to 4% of block size. Với RGW workloads, được khuyến nghĩ rằng kích thước `block.db` không nhỏ hơn 4% của kích thước `block` bởi vì RGW sử dụng nhiều để lưu metadata. Ví dụ: kích thước block là 1TB thì kích thước `block.db` không nhỏ hơn 40GB. Với RBD workloads, 1% - 2% của block size thường là đủ.

Nếu không kết hợp HDD và SSD thì không cần thiết tạo 2 logical volumes riêng biệt cho `block.db` (hoặc `block.wal`). Bluestore sẽ từ quản lý trong không gian của `block`.

## Tự động Sizing cache

Bluestore có thể tự động thay đổi kích thước cache khi tc_malloc được cấu hình như là bộ cấp phát bộ nhớ và `bluestore_cache_autotune setting is enabled`, đây là tùy chọn mặc định. Bluestore sẽ duy trì việc sử dụng OSD heap memory theo kích thước được chỉ định qua tùy chọn `osd_memory_target`. Đây là thuật thoán tốt nhất và cache sẽ không nhỏ hơn giá trị chỉ định trong `osd_memory_cache_min`. Tỉ lệ cache sẽ đươch chọn dựa trên hệ thống phân cấp ưu tiên. Nếu thông tin ưu tiên không có sẵn,`bluestore_cache_meta_ratio` và `bluestore_cache_kv_ratio` được sử dụng dự phòng.

## Tài liệu tham khảo
- https://docs.ceph.com/docs/octopus/rados/configuration/bluestore-config-ref/
