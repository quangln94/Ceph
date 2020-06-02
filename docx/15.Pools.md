# Pools

Khi deploy 1 cluster mà không tạo pool, Ceph sử dụng default pools để lưu data. Pool cung cấp:

- **Resilience:** Có thể đặt số OSD cho phép lỗi mà không mất dữ liệu. Với replicated pools, nó là số lượng bản copies/replicas mong muốn của 1 object. Thường cấu hình lưu 1 object và 1 copy (size = 2), nhưng có thể xác định số bản copies/replicas. For erasure coded pools, it is the number of coding chunks (i.e. m=2 in the erasure code profile)
- **Placement Groups:** Có thể đặt số Placement groups cho pool. Thường cấu hình sử dụng xấp xỉ 100 Placement groups / OSD để cung cấp cân bằng tải tối ưu mà không sử dụng quá nhiểu resources. Khi cấu hình nhiều pools, phải đảm bảo rằng đặt số Placement groups cho pool và toàn bộ Cluster.
- **CRUSH Rules**: Khi lưu data trong 1 pool, placement của object và replicas của nó(hoặc chunks cho erasure coded pools) trong Cluster in your cluster được chi phối bởi CRUSH rules. Có thể tạo 1 custom CRUSH rule cho pool nếu default rule không phù hợp.
- **Snapshots:** Khi tạo snapshots với `ceph osd pool mksnap`, bạn có 1 snapshot của pool.
## Tài liệu tham khảo 
- https://docs.ceph.com/docs/master/rados/operations/pools/
