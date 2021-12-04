AMI Builder Pipeline
https://console.aws.amazon.com/imagebuilder/home?region=us-east-1#/pipelines

Pipeline created with JRE.  Need JDK.
sudo amazon-linux-extras install java-openjdk11
alternatives --config java     # requires config and selection


Manual Steps then innit?
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html
## Check the name of the device - note that /dev/ prefix is omitted from output
[root@ip-10-31-1-181 ~]# lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1       259:0    0   8G  0 disk 
├─nvme0n1p1   259:2    0   8G  0 part /
└─nvme0n1p128 259:3    0  10M  0 part /boot/efi
nvme1n1       259:1    0  20G  0 disk 

## Make a filesystem that can be mounted
[root@ip-10-31-1-181 ~]# mkfs -t xfs /dev/nvme1n1
meta-data=/dev/nvme1n1           isize=512    agcount=4, agsize=1310720 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=0
data     =                       bsize=4096   blocks=5242880, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

## View that it is now a filesystem
[root@ip-10-31-1-181 ~]# file -s /dev/nvme1n1
/dev/nvme1n1: SGI XFS filesystem data (blksz 4096, inosz 512, v2 dirs)

## Make a directory to mount to
[root@ip-10-31-1-181 ~]# mkdir /xtf

## Mount to it
[root@ip-10-31-1-181 ~]# mount /dev/nvme1n1 /xtf

## See it's now mounted
[root@ip-10-31-1-181 ~]# lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1       259:0    0   8G  0 disk 
├─nvme0n1p1   259:2    0   8G  0 part /
└─nvme0n1p128 259:3    0  10M  0 part /boot/efi
nvme1n1       259:1    0  20G  0 disk /xtf

#### Copy the content down then yah?
[root@ip-10-31-1-181 xtf]# aws s3 cp s3://us-east-1-brainflutter-xtf-fam/xtf/xtf.tar.gz xtf.tar.gz

## Then extract
tar -xvf xtf.tar.gz

## and create a link to that directory from webapps in tomcat
ln -s /xtf/xtf $CATALINA_HOME/webapps/xtf