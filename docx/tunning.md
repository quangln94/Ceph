Show các loại và số lượng Backend của osd:
```sh
ceph osd count-metadata osd_objectstore
```
Show Backend của 1 OSD
```sh
sudo ceph osd metadata 1 | grep objectstore
```

## Tài liệu tham khảo
- https://documentation.suse.com/ses/6/html/ses-all/tuning-ceph.html
- https://object-storage-ca-ymq-1.vexxhost.net/swift/v1/6e4619c416ff4bd19e1c087f27a43eea/www-assets-prod/presentation-media/Advanced-Tuning-and-Operation-guide-for-Block-Storage-using-Ceph-Boston-2017-final.pdf
- https://access.redhat.com/documentation/en-us/red_hat_ceph_storage/3/html/administration_guide/osd-bluestore
- https://swamireddy.wordpress.com/2019/09/03/ceph-how-to-find-osds-backend/
