# CRUSh

Với các hoạt động đọc/ghi vào Ceph Cluster, Client liên lạc với Ceph monitor để lấy 1 bản copy của cluster map bao gồm: namely
the monitor, OSD, MDS, CRUSH và PG maps. Các cluster maps giúp clients biết trạng thái và cấu hình của Ceph cluster. Sau đó data được converted sang objects using 1 object name và pool names/IDs. Object sau đó được hashed với số PGs để tạo 1 PG cuối cùng trong Ceph pool được yêu cầu. PG được tính toán đi qua function CRUSH để xác định primary, secondary, và tertiary OSD locations để lưu trữ và lấy dữ liệu.

Khi Client lấy được OSD ID, nó liên lạc trực tiếp với OSDs và lưu data. Toàn bộ việc tính toán được thực hiện bởi Clients, do đó chúng không ảnh hưởng đến hiệu suất của Cluster.

<img src=https://i.imgur.com/JLtyYG8.png>
