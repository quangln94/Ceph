# 
## 1. Tính năng

- Quản lý trực tiếp thiết bị lưu trữ: BlueStore sử dụng raw block devices hoặc partitions tránh việc can thiếp của các lớp trừu tượng như local file systems XFS có thể làm giảm hiệu năng hoặc tăng sự phức tạp.
- Quản lý management với RocksDB: BlueStore sử dụng RocksDB’ key-value database để quản lý internal metadata như mapping từ object names đến block locations trên disk.
- Full data and metadata checksumming: Mặc định tất cả data và metadata ghi vào BlueStore được bảo vệ bởi 1 hoặc nhiểu checksums. Không có data hoặc metadata được đọc từ disk hoặc trả vể cho user mà không  có verification.
- Efficient copy-on-write: Ceph Block Device và Ceph File System snapshots rely on a copy-on-write clone mechanism that is implemented efficiently in BlueStore. This results in efficient I/O both for regular snapshots and for erasure coded pools which rely on cloning to implement efficient two-phase commits.
- No large double-writes: BlueStore first writes any new data to unallocated space on a block device, and then commits a RocksDB transaction that updates the object metadata to reference the new region of the disk. Only when the write operation is below a configurable size threshold, it falls back to a write-ahead journaling scheme, similar to what how FileStore operates.
- Multi-device support: BlueStore can use multiple block devices for storing different data, for example: Hard Disk Drive (HDD) for the data, Solid-state Drive (SSD) for metadata, Non-volatile Memory (NVM) or Non-volatile random-access memory (NVRAM) or persistent memory for the RocksDB write-ahead log (WAL). See Section 9.2, “BlueStore Devices” for details.
