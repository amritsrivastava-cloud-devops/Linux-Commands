# Linux Volume Management 
- Infroduction to Linux volumes and AWS EBS
- Physical vs Logical Volumes vs Volume Groups in Linux
- Mounting Volumes in Linux
- Managing Aws ebs on Ec2 instance
- Introduction to LVM (Logical volume manager)
- Using LVM with EBS for dynamic storage management

## Introduction to Linux volumes and AWS EBS
- AWS EBS is used to create volume in aws , we can attach to ec2 instance.

<img width="1746" height="667" alt="image" src="https://github.com/user-attachments/assets/e0d86ff0-1cb3-482e-b512-851c5b245f3e" />

- Command: lsblk
- Command: df -h
<img width="570" height="366" alt="image" src="https://github.com/user-attachments/assets/5799aeeb-1f9f-425e-80c0-b4969cec4211" />

- go to ebs and create volume and attach volume with svdf to so on .
- use lsblk and check
<img width="515" height="199" alt="image" src="https://github.com/user-attachments/assets/974abb15-f958-4d13-b453-17d690414f88" />

- Mount point is still empty

## Physical vs Logical Volumes vs Volume Groups in Linux

Physical volume - 10g , 12 g , 14g 
Volume group = 10gb + 12gb = 22gb
Logical volume = extract from 22gb 

now lvm is managing all 3 physical volume , volume group , logical volume 

## rough

root@ip-172-31-6-109:~# pvs
root@ip-172-31-6-109:~# lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0          7:0    0   74M  1 loop /snap/core22/2163
loop1          7:1    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop2          7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1      259:0    0    8G  0 disk
├─nvme0n1p1  259:1    0    7G  0 part /
├─nvme0n1p14 259:2    0    4M  0 part
├─nvme0n1p15 259:3    0  106M  0 part /boot/efi
└─nvme0n1p16 259:4    0  913M  0 part /boot
nvme1n1      259:5    0   12G  0 disk
nvme2n1      259:6    0   10G  0 disk
nvme3n1      259:7    0   14G  0 disk
root@ip-172-31-6-109:~# lvm
lvm> pvcreate /dev/nvme1n1 /dev/nvme2n1 /dev/nvme3n1
  Physical volume "/dev/nvme1n1" successfully created.
  Physical volume "/dev/nvme2n1" successfully created.
  Physical volume "/dev/nvme3n1" successfully created.
lvm> pvs
  PV           VG Fmt  Attr PSize  PFree
  /dev/nvme1n1    lvm2 ---  12.00g 12.00g
  /dev/nvme2n1    lvm2 ---  10.00g 10.00g
  /dev/nvme3n1    lvm2 ---  14.00g 14.00g
lvm> vgcreate aws1_vg /dev/nvme1n1 /dev/nvme2n1
  Volume group "aws1_vg" successfully created
lvm> vgs
  VG      #PV #LV #SN Attr   VSize  VFree
  aws1_vg   2   0   0 wz--n- 21.99g 21.99g
lvm> lvcreate -L 9G -n aws1_lv aws1_vg
  Logical volume "aws1_lv" created.
lvm> lv
  No such command 'lv'.  Try 'help'.
lvm> pvdisplay
  --- Physical volume ---
  PV Name               /dev/nvme1n1
  VG Name               aws1_vg
  PV Size               12.00 GiB / not usable 4.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              3071
  Free PE               767
  Allocated PE          2304
  PV UUID               gixIzK-F1Z1-pnD4-d9ci-loBs-KPiM-YbyWQ4

  --- Physical volume ---
  PV Name               /dev/nvme2n1
  VG Name               aws1_vg
  PV Size               10.00 GiB / not usable 4.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              2559
  Free PE               2559
  Allocated PE          0
  PV UUID               XpvpLI-p7o5-jPAe-6PI6-7OoM-3FR3-9kj0TL

  "/dev/nvme3n1" is a new physical volume of "14.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/nvme3n1
  VG Name
  PV Size               14.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               mX3GhV-Bn5J-UjVz-ccVC-vXby-T8Fz-CwaQek

lvm> vgdisplay
  --- Volume group ---
  VG Name               aws1_vg
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  2
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               21.99 GiB
  PE Size               4.00 MiB
  Total PE              5630
  Alloc PE / Size       2304 / 9.00 GiB
  Free  PE / Size       3326 / 12.99 GiB
  VG UUID               T2UAip-bfLS-ozjO-GjfG-LD9Q-BUU3-0G557u

lvm> exit
  Exiting.
root@ip-172-31-6-109:~# lsblk
NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0               7:0    0   74M  1 loop /snap/core22/2163
loop1               7:1    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop2               7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1           259:0    0    8G  0 disk
├─nvme0n1p1       259:1    0    7G  0 part /
├─nvme0n1p14      259:2    0    4M  0 part
├─nvme0n1p15      259:3    0  106M  0 part /boot/efi
└─nvme0n1p16      259:4    0  913M  0 part /boot
nvme1n1           259:5    0   12G  0 disk
└─aws1_vg-aws1_lv 252:0    0    9G  0 lvm
nvme2n1           259:6    0   10G  0 disk
nvme3n1           259:7    0   14G  0 disk
root@ip-172-31-6-109:~# lvs
  LV      VG      Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  aws1_lv aws1_vg -wi-a----- 9.00g                                              
root@ip-172-31-6-109:~# mkdir /mnt/aws1_lv_mount
root@ip-172-31-6-109:~# mkfs.ext4 /dev/aws1_vg/aws1_lv
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 2359296 4k blocks and 589824 inodes
Filesystem UUID: 10edd792-5a78-4c05-99b1-e397ff3f8f5f
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

root@ip-172-31-6-109:~# mount /dev/aws1_vg/aws1_lv /mnt/aws1_lv_mount/
root@ip-172-31-6-109:~# lsblk
NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0               7:0    0   74M  1 loop /snap/core22/2163
loop1               7:1    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop2               7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1           259:0    0    8G  0 disk
├─nvme0n1p1       259:1    0    7G  0 part /
├─nvme0n1p14      259:2    0    4M  0 part
├─nvme0n1p15      259:3    0  106M  0 part /boot/efi
└─nvme0n1p16      259:4    0  913M  0 part /boot
nvme1n1           259:5    0   12G  0 disk
└─aws1_vg-aws1_lv 252:0    0    9G  0 lvm  /mnt/aws1_lv_mount
nvme2n1           259:6    0   10G  0 disk
nvme3n1           259:7    0   14G  0 disk
root@ip-172-31-6-109:~# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/root                    6.8G  1.8G  5.0G  27% /
tmpfs                        458M     0  458M   0% /dev/shm
tmpfs                        183M  916K  182M   1% /run
tmpfs                        5.0M     0  5.0M   0% /run/lock
efivarfs                     128K  3.6K  120K   3% /sys/firmware/efi/efivars
/dev/nvme0n1p16              881M   89M  730M  11% /boot
/dev/nvme0n1p15              105M  6.2M   99M   6% /boot/efi
tmpfs                         92M   12K   92M   1% /run/user/1000
/dev/mapper/aws1_vg-aws1_lv  8.8G   24K  8.3G   1% /mnt/aws1_lv_mount
root@ip-172-31-6-109:~# lsblk
NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0               7:0    0   74M  1 loop /snap/core22/2163
loop1               7:1    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop2               7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1           259:0    0    8G  0 disk
├─nvme0n1p1       259:1    0    7G  0 part /
├─nvme0n1p14      259:2    0    4M  0 part
├─nvme0n1p15      259:3    0  106M  0 part /boot/efi
└─nvme0n1p16      259:4    0  913M  0 part /boot
nvme1n1           259:5    0   12G  0 disk
└─aws1_vg-aws1_lv 252:0    0    9G  0 lvm  /mnt/aws1_lv_mount
nvme2n1           259:6    0   10G  0 disk
nvme3n1           259:7    0   14G  0 disk
root@ip-172-31-6-109:~# mkdir /mnt/aws1_disk_mount
root@ip-172-31-6-109:~# mkfs -t ext4 /dev/nvme3n1
mke2fs 1.47.0 (5-Feb-2023)
/dev/nvme3n1 contains a LVM2_member file system
Proceed anyway? (y,N) y
Creating filesystem with 3670016 4k blocks and 917504 inodes
Filesystem UUID: f251f347-b32d-405f-8d82-5e6e195b0b9d
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

root@ip-172-31-6-109:~# mount /dev/nvme3n1 /mnt/aws1_disk_mount/
root@ip-172-31-6-109:~# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/root                    6.8G  1.8G  5.0G  27% /
tmpfs                        458M     0  458M   0% /dev/shm
tmpfs                        183M  916K  182M   1% /run
tmpfs                        5.0M     0  5.0M   0% /run/lock
efivarfs                     128K  3.6K  120K   3% /sys/firmware/efi/efivars
/dev/nvme0n1p16              881M   89M  730M  11% /boot
/dev/nvme0n1p15              105M  6.2M   99M   6% /boot/efi
tmpfs                         92M   12K   92M   1% /run/user/1000
/dev/mapper/aws1_vg-aws1_lv  8.8G   24K  8.3G   1% /mnt/aws1_lv_mount
/dev/nvme3n1                  14G   24K   13G   1% /mnt/aws1_disk_mount
root@ip-172-31-6-109:~# lsblk
NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0               7:0    0   74M  1 loop /snap/core22/2163
loop1               7:1    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop2               7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1           259:0    0    8G  0 disk
├─nvme0n1p1       259:1    0    7G  0 part /
├─nvme0n1p14      259:2    0    4M  0 part
├─nvme0n1p15      259:3    0  106M  0 part /boot/efi
└─nvme0n1p16      259:4    0  913M  0 part /boot
nvme1n1           259:5    0   12G  0 disk
└─aws1_vg-aws1_lv 252:0    0    9G  0 lvm  /mnt/aws1_lv_mount
nvme2n1           259:6    0   10G  0 disk
nvme3n1           259:7    0   14G  0 disk /mnt/aws1_disk_mount
root@ip-172-31-6-109:~# lvm
lvm> lvs
  LV      VG      Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  aws1_lv aws1_vg -wi-ao---- 9.00g
lvm> lgs
  No such command 'lgs'.  Try 'help'.
lvm> lvg
  No such command 'lvg'.  Try 'help'.
lvm> exit
  Exiting.
root@ip-172-31-6-109:~# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/root                    6.8G  1.8G  5.0G  27% /
tmpfs                        458M     0  458M   0% /dev/shm
tmpfs                        183M  916K  182M   1% /run
tmpfs                        5.0M     0  5.0M   0% /run/lock
efivarfs                     128K  3.6K  120K   3% /sys/firmware/efi/efivars
/dev/nvme0n1p16              881M   89M  730M  11% /boot
/dev/nvme0n1p15              105M  6.2M   99M   6% /boot/efi
tmpfs                         92M   12K   92M   1% /run/user/1000
/dev/mapper/aws1_vg-aws1_lv  8.8G   24K  8.3G   1% /mnt/aws1_lv_mount
/dev/nvme3n1                  14G   24K   13G   1% /mnt/aws1_disk_mount
root@ip-172-31-6-109:~# lvextend -L +5G /dev/aws1_vg/aws1_lv
  Size of logical volume aws1_vg/aws1_lv changed from 9.00 GiB (2304 extents) to 14.00 GiB (3584 extents).
  Logical volume aws1_vg/aws1_lv successfully resized.
root@ip-172-31-6-109:~# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/root                    6.8G  1.8G  5.0G  27% /
tmpfs                        458M     0  458M   0% /dev/shm
tmpfs                        183M  916K  182M   1% /run
tmpfs                        5.0M     0  5.0M   0% /run/lock
efivarfs                     128K  3.6K  120K   3% /sys/firmware/efi/efivars
/dev/nvme0n1p16              881M   89M  730M  11% /boot
/dev/nvme0n1p15              105M  6.2M   99M   6% /boot/efi
tmpfs                         92M   12K   92M   1% /run/user/1000
/dev/mapper/aws1_vg-aws1_lv  8.8G   24K  8.3G   1% /mnt/aws1_lv_mount
/dev/nvme3n1                  14G   24K   13G   1% /mnt/aws1_disk_mount
root@ip-172-31-6-109:~# lsblk
NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0               7:0    0   74M  1 loop /snap/core22/2163
loop1               7:1    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop2               7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1           259:0    0    8G  0 disk
├─nvme0n1p1       259:1    0    7G  0 part /
├─nvme0n1p14      259:2    0    4M  0 part
├─nvme0n1p15      259:3    0  106M  0 part /boot/efi
└─nvme0n1p16      259:4    0  913M  0 part /boot
nvme1n1           259:5    0   12G  0 disk
└─aws1_vg-aws1_lv 252:0    0   14G  0 lvm  /mnt/aws1_lv_mount
nvme2n1           259:6    0   10G  0 disk
└─aws1_vg-aws1_lv 252:0    0   14G  0 lvm  /mnt/aws1_lv_mount
nvme3n1           259:7    0   14G  0 disk /mnt/aws1_disk_mount
root@ip-172-31-6-109:~# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/root                    6.8G  1.8G  5.0G  27% /
tmpfs                        458M     0  458M   0% /dev/shm
tmpfs                        183M  916K  182M   1% /run
tmpfs                        5.0M     0  5.0M   0% /run/lock
efivarfs                     128K  3.6K  120K   3% /sys/firmware/efi/efivars
/dev/nvme0n1p16              881M   89M  730M  11% /boot
/dev/nvme0n1p15              105M  6.2M   99M   6% /boot/efi
tmpfs                         92M   12K   92M   1% /run/user/1000
/dev/mapper/aws1_vg-aws1_lv  8.8G   24K  8.3G   1% /mnt/aws1_lv_mount
/dev/nvme3n1                  14G   24K   13G   1% /mnt/aws1_disk_mount
root@ip-172-31-6-109:~#
