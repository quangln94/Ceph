# Driver backups
## 1. Chuẩn bị
**Thực hiện tạo volume trên hệ thống Openstack**
```sh
openstack volume create vl01 --size 1
```
**Lấy ID của volume vừa khởi tạo**
```sh
openstack volume list
```
<img src=https://i.imgur.com/qNNvBCn.png>

ID của volume: 90010680-6737-4b19-b760-fdad69f55bff

## 2. 
### 2.1 Thực hiện lần 1
#### 2.1.1 Thực hiện khởi tạo `backup full` từ hệ thống Openstack
```sh
openstack volume backup create --name bkfull01-vl01 vl01
```
<img src=https://i.imgur.com/tOvDjII.png>

#### 2.1.2 Thực hiện khởi tạo `backup full` từ hệ thống Openstack

### 2.2 Thực hiện lần 2
#### 2.2.1 Thực hiện khởi tạo `backup full` từ hệ thống Openstack
```sh
openstack volume backup create --name bkfull02-vl01 vl01
```
<img src=https://i.imgur.com/9Q8OVqy.png>

#### 2.2.2 Kiểm tra dưới hệ thống Ceph
**Kiểm tra pool `volumes_ssd` và show `snapshot` của volume

```sh
rbd -p volumes_ssd ls - | grep 90010680-6737-4b19-b760-fdad69f55bff
```
<img src=https://i.imgur.com/mpz3Rrt.png>

```sh
rbd snap ls volumes_ssd/volume-90010680-6737-4b19-b760-fdad69f55bff
```
<img src=https://i.imgur.com/UQG4xNk.png>

**Kiểm tra pool `backups` và show `snapshot`

```sh
rbd -p backups ls - | grep 90010680-6737-4b19-b760-fdad69f55bff
```
<img src=blob:https://imgur.com/e6477deb-3c5d-4658-8629-9badc27d88db>

Kết luận: Khi khởi tạo các bản `backup full` từ volume ban đầu, dưới hệ thống Ceph sẽ tạo ra:
- Trong pool `backups`: Tạo ra các image riêng biệt. Mỗi image chỉ có 1 bản Snapshot duy nhất.
- Trong pool `volumes_ssd:` Volume được tạo ra có nhiều bản snapshot. Mỗi bản snapshot tương ứng với 1 bản `snapshot` hoặc `backup`

### 2.3 Thực hiện lần 3
#### 2.3.1 Thực hiện khởi tạo `backup incremental` từ hệ thống Openstack
```sh
openstack volume backup create --name bkincremental01frombkfull02-vl01 vl01 --incremental
```
***Bản backup này sẽ là bản `incremental` so với bản `bk02-vl01`

<img src=https://i.imgur.com/iK4plbg.png>

### 2.4 Thực hiện lần 4
#### 2.4.1 Thực hiện khởi tạo `backup incremental` từ hệ thống Openstack
```sh
openstack volume backup create --name bkincremental02frombkfull02-vl01 vl01 --incremental
```
***Bản backup này sẽ là bản `incremental` so với bản `bkincremental01frombkfull02-vl01`

<img src=https://i.imgur.com/NwHBwtt.png>

#### 2.4.2 Kiểm tra dưới hệ thống Ceph
**Kiểm tra pool `volumes_ssd` và show `snapshot` của volume

```sh
rbd snap ls volumes_ssd/volume-90010680-6737-4b19-b760-fdad69f55bff
```
<img src=https://i.imgur.com/jfEy3kE.png>

**Kiểm tra pool `backups` và show `snapshot`

```sh
rbd -p backups ls - | grep f9d22999-295d-417e-9c8c-e65c2e2d9eb9
```
<img src=https://i.imgur.com/lUUk5eA.png>

Kết luận: Khi khởi tạo các bản `backup incremental`, dưới hệ thống Ceph sẽ tạo ra:
- Trong pool `backups`: Không tạo thêm các image. Trong image tương ứng với bản backup sau cùng sẽ tạo ra các bản `snapshot` tương ứng với các bản backup `incremental`.
- Trong pool `volumes_ssd:` Volume được tạo ra có nhiều bản snapshot. Mỗi bản snapshot tương ứng với 1 bản `snapshot` hoặc `backup`, bản `backup` bao gồm cả `backup full` và `backup incremental`.

### 2.5 Thực hiện lần 5
#### 2.5.1 Thực hiện khởi tạo `backup full` từ hệ thống Openstack
```sh
openstack volume backup create --name bkfull03-vl01 vl01
```

***Bản backup này sẽ là bản `backup full`***

<img src=https://i.imgur.com/AVdA5Nm.png>

### 2.6 Thực hiện lần 6
#### 2.6.1 Thực hiện restore `vl01` về bản `backup02` từ hệ thống Openstack
```sh
openstack volume backup restore bkfull02-vl01 90010680-6737-4b19-b760-fdad69f55bff
```

### 2.7 Thực hiện lần 7
#### 2.7.1 Thực hiện khởi tạo `backup incremental` từ hệ thống Openstack
```sh
openstack volume backup create --name bkincremental03frombkfull03-vl01 vl01 --incremental
```
***Bản backup này sẽ là bản `backup incremental` so với bản `bkfull03` là bản `backup full` mới nhất vừa tạo.

**Kiểm tra pool `volumes_ssd` và show `snapshot` của volume

```sh
rbd snap ls volumes_ssd/volume-90010680-6737-4b19-b760-fdad69f55bff
```
<img src=https://i.imgur.com/bOUuSrR.png>

**Kiểm tra pool `backups` và show `snapshot`

```sh
rbd -p backups ls - | grep f9d22999-295d-417e-9c8c-e65c2e2d9eb9
```
<img src=https://i.imgur.com/yydSLuo.png>

Kết luận: Khi khởi tạo các bản `backup incremental`, dưới hệ thống Ceph sẽ tạo ra:
- Trong pool `backups`: Không tạo thêm các image. Trong image tương ứng với bản backup sau cùng sẽ tạo ra các bản `snapshot` tương ứng với các bản backup `incremental`.
- Trong pool `volumes_ssd:` Volume được tạo ra có nhiều bản snapshot. Mỗi bản snapshot tương ứng với 1 bản `snapshot` hoặc `backup`, bản `backup` bao gồm cả `backup full` và `backup incremental`.
