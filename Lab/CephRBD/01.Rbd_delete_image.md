# Rbd delete image report: rbd: error: image still has watchers
## 1. Solution ideas
Có 2 lý do dẫn đến việc không thể delete image trong Ceph:
- Image đang có snapshot, cần xóa snapshot trước mới có thể xóa được image.
- Image đang được truy cập bởi client khác, được hiển thị dưới dạng `watcher in the image`. Nếu đó là 1 client khác thương, image không thể bị xóa. Ví dụ minh họa bên dưới.

<img src=https://i.imgur.com/6SXdn3L.png>

### 1.1 Image đang có snapshot
Với trường hợp này, cần xóa tất cả các snapshot của iamge trước, sau đó thực hiện xóa iamge. Thực hiện như sau:

***Xóa lần lượt từng snapshot***
```sh
rbd snap rm pool_names/image_names@snapshot_names
```
***Hoặc xóa toàn bộ snapshot***
```sh
rbd snap purge -p pool_names iamge_names
```
### 1.2 Image đang được truy cập bởi client khác

***Hiển thị `watcher` trên image hiện tại***

<img src=https://i.imgur.com/OfuUCK1.png>

**Tìm image header object**

<img src=https://i.imgur.com/IxbcLTu.png>

**Show thông tin `watcher` trên image header object**

<img src=https://i.imgur.com/PomZSoX.png>

**Delete `watcher` trên image**

- Thêm `watcher` vào `blacklist`:
```sh
ceph osd blacklist add 192.168.40.32:0/1646463270
```
- Hiển thị thông tin `watcher` trên image header object:
```sh
rados -p volumes_hdd listwatchers rbd_header.864620b51da499
```
- Thông tin về `watcher` của client không con tồn tại nữa và image bây giờ có thể delete.
```sh
rbd -p volumes_hdd rm volume-deda7d7c-eb41-442a-9705-39358e86d854 
```

### 1.3 Xóa client trong blacklist
Sau khi delete image, cần xóa client trong `blacklist`
- Show blacklist
```sh
ceph osd blacklist ls
```
- Remove client trong blacklist
```sh
ceph osd blacklist rm 192.168.40.32:0/1646463270
```
- Clear the blacklist
```sh
ceph osd blacklist clear
```

## Tài liệu tham khảo
- https://www.programmersought.com/article/513030853/
- https://www.codetd.com/en/article/8458376
