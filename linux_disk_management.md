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

