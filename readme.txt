My notes on getting a minimal PXE server for ESXi to boot from.

Setup ansible:
  virtualenv venv
  source venv/bin/activate
  pip install ansible
  pip freeze > requirements.txt

Spin up a new minimal centos7 vm (with bridged networking).
  hostname:pixie
  ip: 192.168.0.107
# Note: Ensure mikekinney has sudo no password

# TODO: move stuff below into ansible

# scp esxi67 iso to /tmp
mkdir -p /mnt/iso
cd /tmp
mount -o loop VMware-VMvisor-Installer-6.7.0-8169922.x86_64.iso /mnt/iso
mkdir -p /var/lib/tftpboot/images/esxi67
cp -rf /mnt/iso/* /var/lib/tftpboot/images/esxi67
umount /mnt/iso
# If you do not do next step then it cannot find /b0 files
sed -i 's/\///g' /var/lib/tftpboot/images/esxi67/boot.cfg

# scp esxi65 iso to /tmp
mkdir -p /mnt/iso
cd /tmp
mount -o loop VMware-VMvisor-Installer-6.5.0-4564106.x86_64.iso /mnt/iso
mkdir -p /var/lib/tftpboot/images/esxi65
cp -rf /mnt/iso/* /var/lib/tftpboot/images/esxi65
umount /mnt/iso
# If you do not do next step then it cannot find /b0 files
sed -i 's/\///g' /var/lib/tftpboot/images/esxi65/boot.cfg

# scp centos iso to /tmp
cd /tmp
mount -o loop CentOS-7-x86_64-DVD-1708.iso /mnt/iso
mkdir -p /var/lib/tftpboot/images/centos7
cp /mnt/iso/images/pxeboot/vmlinuz /var/lib/tftpboot/images/centos7
cp /mnt/iso/images/pxeboot/initrd.img /var/lib/tftpboot/images/centos7
mkdir -p /var/ftp/pub/centos7
cp -av /mnt/iso/* /var/ftp/pub/centos7
chmod -R 755 /var/ftp/pub/centos7
umount /mnt/iso


Testing
  Be sure to have at least 2gb ram for centos7 (otherwise weird out of disk space error)
Can add "ks=ftp://192.168.0.107/pub/centos7/centos7.ks" for kickstart
