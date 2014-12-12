(https://www.youtube.com/watch?v=vy6hWsOuCh8)
(https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/)
(https://coreos.com/docs/)
(https://coreos.com/docs/quickstart/)

Boot From a liveCD of Linux (CentOS maybe)
In webbrowser goto Coreos.com
choose download CoreOS and choose the hyperlink “on Github here”
copy the contents of this link and paste into a file on the desktop called coreos_install.sh 
(vim coreos_install.sh)
chmod this .sh file so it can be executable 
(chmod +x coreos_install.sh)
Use another Linux machine to generate rsa key for login on CoreOS, this will generate a public and private key that need to be saved where the machine can get them later
(ssh-keygen -t rsa -b 2048)
Create another file cloud-config.yaml
(vim cloud-config.yaml)
this file will contain (the ssh-rsa key will be from the public rsa get generated; cat CoreOSBM_rsa and copy and paste that key without the logon info):
#cloud-config
 
coreos: 
  etcd: 
    addr: "$private_ipv4:4001"
    discovery: "https://discovery.etcd.io/76e9d18c0b25dce46cd482757d14af37"
    name: coreos0
    peer-addr: "$private_ipv4:7001"
  units: 
    - 
      command: start
      name: etcd.service
    - 
      command: start
      name: fleet.service
hostname: coreos0
ssh_authorized_keys: 
  - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDNQYI1x21AkfX4T9WynkXyP36Hf7NWjAPlgoQByWHA4O6XiXitHTeCgZsPPKqQaNdYU7Dq3mX3sg24IU8X5j2WY/vMDTOMiBaebSOulZDULfpHI+RSb9iczWmzRngh4DbCwEZZU999HaQ2VcGqO4juul5hUW+fdQZvzqOL2J0Jj833AiYoQssOVIdPxGkEZejncCat/A6PpwLKh6p/Yq8fC/1YbJLAcTeWJmCDIU9oY6K1MEiNkcibwRTkr/khKI0fAPWuJBkW5D12JJWfT14aHqVL3I8Ypk4b3dZb+wH/RCd2nJpEy22coFLvUnQgoLmfiDOtUASdSJAE5Wcpet4p"


check harddrive with
(ls -lah /dev/sd*)
execute the file coreos_install.sh
(sudo ./coreos_install.sh -d /dev/sda -C stable -c cloud-config.yaml)
reboot the new coreos server
login remotely with
(ssh -i /tmp/CoreOSBM_rsa core@172.16.x.x)

The discovery token is unique;however, the same for machines in the same cluster. This is done with the discovery portion of the cloud-config.yaml
