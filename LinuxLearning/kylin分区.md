1. boot ext4
2. extend
5. / ext4
6. backup ext4
6. data ext4
7. swap swap

kylingui手动分区逻辑
/dex/sda1 主分区=引导分区，2G , ext4, 挂载/boot
/dex/sda2 是1个分区下塞多个分区，gui中手动新建下一个逻辑分区后会自动创建/dex/sda2, extend ,空挂载的分区
大小为后续逻辑分区的和
/dex/sda5 逻辑分区，存放系统, 200G, ext4, /boot
/dex/sda6 逻辑分区，存放文件, 296G, ext4, /data,其中data 手动输入
/dex/sda7 逻辑分区，放swap, 2G, linux-swap, 空挂载
/dex/sda8 backup

## 查看
sudo apt-get update
sudo apt-get install gparted
sudo gparted

fdisk -l 
lsblk 