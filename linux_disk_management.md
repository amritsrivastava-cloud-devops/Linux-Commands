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

- Physical volume - 10g , 12 g , 14g 
- Volume group = 10gb + 12gb = 22gb
- Logical volume = extract from 22gb 

- now lvm is managing all 3 physical volume , volume group , logical volume 

## Mounting Volumes in Linux

#### Creating physical volume 
<img width="402" height="200" alt="image" src="https://github.com/user-attachments/assets/d3fbe4d0-2a1c-4f47-a7e2-00640041d2f2" />

#### Creating volume group
<img width="355" height="104" alt="image" src="https://github.com/user-attachments/assets/4eb65d50-f4e3-4857-bd7d-e00ccadd9858" />

#### Creating logical volume
<img width="279" height="46" alt="image" src="https://github.com/user-attachments/assets/1ba7b19a-0d8b-49c5-b049-111a364e3703" />

## Introduction to LVM (Logical volume manager)

#### In lvm we can use following commands 
- pvs (display physical volume)
- pvdisplay (physical volume description)
- vgdisplay (volume group description)
-  created logical volume 
- lvs (list all logical volumes)
<img width="598" height="66" alt="image" src="https://github.com/user-attachments/assets/5091dceb-bb96-474b-a06c-9194f417ec4d" />

#### Create a mount point in /mnt directory for logical volume mount 
- Format logical volume first 
- Command :mkdir /mnt/aws1_lv_mount
- Command :mkfs.ext4 /dev/aws1_vg/aws1_lv
- Command :mount /dev/aws1_vg/aws1_lv /mnt/aws1_lv_mount/
<img width="496" height="288" alt="image" src="https://github.com/user-attachments/assets/a15a9f36-439e-4f15-bd36-81b353a2b370" />

#### List down using lsblk command 

<img width="566" height="533" alt="image" src="https://github.com/user-attachments/assets/92b6185d-2fb7-4f38-b67f-786e2535d8f6" />

## Managing Aws ebs on Ec2 instance

- Now Create mount for physical volume directly without any parition or volume groups . 
- Create a dir in /mnt 
- Format physical volume 
- Create mount 
- Command: mkdir /mnt/aws1_disk_mount
- Command: mkfs -t ext4 /dev/nvme3n1
- Command: mount /dev/nvme3n1 /mnt/aws1_disk_mount/

<img width="565" height="330" alt="image" src="https://github.com/user-attachments/assets/fc16733f-1746-4f8e-9bac-208a47ccd0c6" />

- Check using df -h , lsblk command 
<img width="553" height="521" alt="image" src="https://github.com/user-attachments/assets/2c6349d5-ae69-448d-90eb-0260ca3f1552" />

## Using LVM with EBS for dynamic storage management
#### Extend extra GB for existing logical volume 
- Command :lvextend -L +5G /dev/aws1_vg/aws1_lv
<img width="722" height="308" alt="image" src="https://github.com/user-attachments/assets/33e5b6f1-aa01-4ceb-afb1-8a2c7bbc2506" />
<img width="547" height="306" alt="image" src="https://github.com/user-attachments/assets/a437f6b3-df09-4972-965c-c541bd30a86e" />
<img width="537" height="251" alt="image" src="https://github.com/user-attachments/assets/42980043-3e36-4326-ad76-a822d2949b62" />

#### Unmount logic volume 
- Command : unmount /mnt/aws1_lv_mount


