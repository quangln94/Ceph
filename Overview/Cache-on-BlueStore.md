# Linux block cache trên Ceph BlueStore

Linux Block cache là giải pháp cho phép 1 hoặc nhiểu disk nhiw SSD hoạt động như 1 cache cho các disk chậm như HDD

Một thiết bị Logic trình bày cho file-system (hoặc ứng dụng) thay vì đến HDD nơi data lưu trữ thực tế.

## BLueStore on block cache

BlueStore dựa vào Linux block cache được triển khai như sau:
- DB và WAL sẽ được ghi trực tiếp xuống SSD
- Một Logic block device được tạo bằng cách kết hợp SSD và HDD. SSD được sử dụng làm cache cho HDD.
- BlueStore OSD ghi data tới Logical device thay vì HDD

<img src=https://i.imgur.com/uCvjOxl.png>


- Ceph BlueStore kiểm soát RAW disk và có các cấp khác nhau để quản lý RAW disk.
- Linux block cache cũng quản lý phân bổ RAW disk.
- Có thể có một số mâu thuẫn giữa BlueStore và Block cache, đặc biệt là đối với thiết bị SSD.
- Sẽ tốt hơn nếu để BlueStore kiểm soát tổng thể RAW disk. Hơn nữa, BlueStore có thể kiểm soát dữ liệu ưu tiên để lưu vào SSD.



## Tài liệu tham khảo
- http://bos.itdks.com/f422ee718b914181b58559fb1f5474cb.pdf
