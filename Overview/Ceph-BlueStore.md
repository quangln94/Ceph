# 

Data được ghi trực tiếp vào raw block device và metadata được xử lý bới RocksDB. 

OSD được chia thành 2 phần RocksDB metadata và user data. User data được lưu trực tiếp xuống raw block device, metadata được ghi vào block device.

RocksDB không ghi trực tiếp vào raw disk, nó cần lớp filesystem để lưu persistent data của nó là BlueFS

RocksDB sử sụng WAL như 1 transaction log trên persistent storage, không giống Filestore, tất cả hoạt động ghi trước tiên vào journal, trong bluestore có 2 phần data để ghi, 1 là data ghi trực tiếp tới block device và 2 sử dụng cho deferred writes, với deferred writes data được ghi vào WAL và sau đó đẩy vào disk.


<img src=https://i.imgur.com/cQwIP1M.png>
## Tài liệu tham khảo
- https://ceph.io/community/bluestore-default-vs-tuned-performance-comparison/
