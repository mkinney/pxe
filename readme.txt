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

# TODO: move this into ansible
# scp esxi iso to /tmp
mkdir -p /mnt/iso
cd /tmp
mount -o loop VMware-VMvisor-Installer-6.7.0-8169922.x86_64.iso /mnt/iso
mkdir -p /var/lib/tftpboot/images/esxi67
cp -rf /mnt/iso/* /var/lib/tftpboot/images/esxi67
umount /mnt/iso
sed -i 's/\///g' /var/lib/tftpboot/images/esxi67/boot.cfg
