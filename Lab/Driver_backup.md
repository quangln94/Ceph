# Driver backups
## 1. Chuẩn bị
**Thực hiện tạo volume trên hệ thống Openstack**
```sh
openstack volume create vl01 --size 1
```
**Lấy ID của volume vừa khởi tạo
```sh
openstack volume list
```
<img src=https://i.imgur.com/ZF5ZNpy.png>

ID của volume: f9d22999-295d-417e-9c8c-e65c2e2d9eb9

## 2. 
### 2.1 Thực hiện lần 1
#### 2.1.1 Thực hiện khởi tạo `backup full` từ hệ thống Openstack
```sh
openstack volume backup create --name bk01-vl01 vl01
```

### 2.2 Thực hiện lần 2
#### 2.2.1 Thực hiện khởi tạo `backup full` từ hệ thống Openstack
```sh
openstack volume backup create --name bk02-vl01 vl01
```

### 2.3 Thực hiện lần 3
#### 2.3.1 Thực hiện khởi tạo `backup incremental` từ hệ thống Openstack
```sh
openstack volume backup create --name bk03-incremental-vl01 vl01 --incremental
```
***Bản backup này sẽ là bản `incremental` so với bản `bk02-vl01`

### 2.4 Thực hiện lần 4
#### 2.4.1 Thực hiện khởi tạo `backup incremental` từ hệ thống Openstack
```sh
openstack volume backup create --name bk04-incremental-vl01 vl01 --incremental
```
***Bản backup này sẽ là bản `incremental` so với bản `bk03-incremental-vl01`

### 2.5 Thực hiện lần 5
#### 2.5.1 Thực hiện khởi tạo `backup full` từ hệ thống Openstack
```sh
openstack volume backup create --name bk05-vl01 vl01
```
***Bản backup này sẽ là bản `backup full`***

### 2.6 Thực hiện lần 6
#### 2.6.1 Thực hiện restore `vl01` về bản `backup02` từ hệ thống Openstack
```sh

openstack volume backup restore 
    <backup>
    <volume>

openstack volume backup create --name bk05-vl01 vl01
```

### 2.7 Thực hiện lần 7
#### 2.7.1 Thực hiện khởi tạo `backup incremental` từ hệ thống Openstack
```sh
openstack volume backup create --name bk07-incremental-vl01 vl01 --incremental
```
***Bản backup này sẽ là bản `backup incremental` so với bản `backup05`
