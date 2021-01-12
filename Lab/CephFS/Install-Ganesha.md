# Ganesha
## 1. Ganesha là gì
## 2. Cài đặt Ganesha trên CentOS8
Thực hiện cài đặt Ganesha thông qua Repo của Ceph
**Khởi tạo Repo cho Ganesha**
```sh
cat << EOF >> /etc/yum.repos.d/nfs_ganesha_stable.repo.haha
[nfs_ganesha_stable]
name=nfs-ganesha stable repo
baseurl=http://download.ceph.com/nfs-ganesha/rpm-V3.3-stable/octopus/el$releasever/$basearch
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[nfs_ganesha_stable_noarch]
name=nfs-ganesha stable noarch repo
baseurl=http://download.ceph.com/nfs-ganesha/rpm-V3.3-stable/octopus/el$releasever/noarch
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc
EOF
```
```sh
dnf update -y
dnf install nfs-ganesha
```
**Cấu hình Ganesha sử dụng backend là Ceph**
```sh
$ cp /etc/ganesha/ganesha.conf /etc/ganesha/ganesha.conf.org
$ vim /etc/ganesha/ganesha.conf
# create new
NFS_CORE_PARAM {
    # disable NLM
    Enable_NLM = false;
    # disable RQUOTA (not suported on CephFS)
    Enable_RQUOTA = false;
    # NFS protocol
    Protocols = 4;
}
EXPORT_DEFAULTS {
    # default access mode
    Access_Type = RW;
}
EXPORT {
    # uniq ID
    Export_Id = 101;
    # mount path of CephFS
    Path = "/";
    FSAL {
        name = CEPH;
        # hostname or IP address of this Node
        hostname="10.0.0.51";
    }
    # setting for root Squash
    Squash="No_root_squash";
    # NFSv4 Pseudo path
    Pseudo="/vfs_ceph";
    # allowed security options
    SecType = "sys";
}
LOG {
    # default log level
    Default_Log_Level = WARN;
}
```
**Khởi động Ganesha**
```sh
systemctl enable nfs-ganesha
systemctl start nfs-ganesha
```
