
# Extend trên CentOS7 + Ubuntu16 1Disk không cần reboot server:
```
yum install -y parted #Centos
```
```
apt-get install -y parted #Ubuntu
```
******************************************
**TH1: CentOS7**

- Thường thì sẽ 1 disk sda    /dev/sda
**Lệnh xem:**    
```
fdisk -l hoặc lsblk -l
```
```
 Primary partition:
    /dev/sda 
    /dev/sda1     dành cho boot 
    /dev/sda2     dành cho phân vùng /
```
- Tiến hành extend vào sda2:
```
fdisk -l /dev/sda | grep ^/dev 
```
```
# parted /dev/sda 
# resizepart 2 100%      | mình extend partition 2 (sda2) 100%
# quit
```
Check 
```
fdisk -l 
```
hoặc 
```
pvscan
```
xem dung lượng partition sda2 đã được tăng lên chưa.

- Tiếp tục dùng lệnh sau để exten vào volume group /
```
# pvresize /dev/sda2
# lvresize --extents +100%FREE --resizefs /dev/mapper/centos-root
```
- Check lại: 
```
df -h #Kiểm tra dung lượng
```
******************************************
**TH2: Ubuntu** </br>
Thường thì sẽ 1 disk sda    /dev/sda </br>
**Lệnh xem:**
```
fdisk -l 
```
hoặc 
```
lsblk -l
```
```
Extended partition:
/dev/sda 
/dev/sda1     dành cho boot 
/dev/sda2     dành cho phân vùng /
/dev/sda5     Logical partition của /sda2       
```
- Tiến hành extend vào Extended sda2 và Logical sda5
```
fdisk -l /dev/sda | grep ^/dev 		
```
```
#parted /dev/sda 

#resizepart 2 100%      | mình exten  partition 2 (sda2) 100%

#resizepart 5 100%      | mình exten  Logical 5 (sda5) 100%

#quit
```
```
fdisk -l
```
hoặc
```
pvscan
```
xem dung lượng partition sda2 và sda5 đã được tăng lên chưa.

- Tiếp tục dùng lệnh sau để exten vào volume group /
```
pvresize /dev/sda2
```
```
pvresize /dev/sda5
```
```
lvresize --extents +100%FREE --resizefs /dev/mapper/ubuntu--vg-root
```
```
lvresize --extents +100%FREE --resizefs /dev/mapper/ubuntu--vg-ubuntu--lv
```

******************************************
**GrowPart**

Trường hợp này có thể áp dụng exten 1 disk trên cả CentOS6,7 + Ubuntu16,14
</br>
Tuy nhiên Centos6 phải reboot lại.

- GrowPart
**CentOS / RHEL:**
```
yum install cloud-utils-growpart #Install
```
**Ubuntu / Debian:**
```
apt-get install cloud-guest-utils #Install
```
- Dùng growpart resize partition
```
growpart /dev/[Device Name] [Partition Numner]
```
```
growpart /dev/sda 2       #exten vào sda2
```
```
fdisk -l
```
hoặc 
```
pvscan
```
xem dung lượng partition sda2 đã được tăng lên chưa.
</br>
Tiếp tục dùng lệnh sau để exten vào volume group /
```
pvresize /dev/sda2
```
```
lvresize --extents +100%FREE --resizefs /dev/mapper/cl-root
```

******************************************

```
pvresize /dev/sda5
```
```
lvextend /dev/ubuntu-vg/root /dev/sda5
```
```
resize2fs /dev/ubuntu-vg/root      #CentOS6
```
```
xfs_growfs /dev/Mega/root          #CentOS7 
```

******************************************

extend disk centos6 </br>
https://www.rootusers.com/how-to-increase-the-size-of-a-linux-lvm-by-expanding-the-virtual-machine-disk/
https://www.rootusers.com/how-to-increase-the-size-of-a-linux-lvm-by-adding-a-new-disk/
