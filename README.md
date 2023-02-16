# NTFS_Remount
Managed to mount NTFS Volumes RW conveniently. 
1. Enable fstab on Mac OSX:  `sudo vifs`
2. Enable fingerprint authentication for sudo: <br>
`把 /etc/pam.d/sudo 备份为 /etc/pam.d/sudo.bak，`<br>`然后在 /etc/pam.d/sudo 的第二行前面加入 auth sufficient pam_tid.so 这个字符串。`<br>
`修改该文件的目的是在 sudo 程序的认证过程前面插入 Touch ID 验证的模块`
3. Install mount_ntfs as Mac OS 13 has removed it. <br>
`brew tap gromgit/homebrew-fuse`<br>
`brew install macfuse`<br>
`brew install ntfs-3g-mac`<br>
Allow the plug-in in security settings after installation. Then reboot. 

4. Save below as a terminal script. Once NTFS volume is mounted as RO mode, launch it to remount as RW volume. 
```
#!/bin/zsh

IFS_OLD=$IFS
IFS=$'\n'

vlms=`mount | grep "ntfs" | grep read-only`

for i in `echo $vlms`; do 
    j=${${i%[ ]on[ ]/*}#*://}
    device_name=${j%/*}
    device_label=${j#*/}
    echo 
    echo "device name" = $device_name
    echo "device_label" = $device_label
    answer="N"
    echo -n "Do you want to remount below volume as Read-Write mode?(y/N)" 
    read answer
    case $answer in 
    Y | y) 
             sudo echo "" 
             sudo umount -v /dev/$device_name 
             sudo /usr/local/sbin/mount_ntfs -o rw /dev/$device_name "/Volumes/$device_label" 
             ;;
    esac

done


IFS=$IFS_OLD
```
