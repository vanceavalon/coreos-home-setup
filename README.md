## coreos-home-setup

**Actual bare-metal CoreOS installation**

* Boot From a liveCD of Linux (CentOS maybe, I used Linux Mint)
* Goto the [CoreOS bare-metal installation section](https://coreos.com/docs/running-coreos/bare-metal/installing-to-disk/) and choose the hyperlink "on Github here"
* Copy the contents of the "on Github here" page and paste into a file called coreos_install.sh:

    `vi coreos_install.sh`

* chmod this .sh file so it can be executable by user:

    `chmod +x coreos_install.sh`

* Use another Linux machine to generate rsa key for login on CoreOS, this will generate a public and private key that will need to be saved where they can be revrieved later:

    `ssh-keygen -t CoreOSBM_rsa -b 2048`

* Generate a new discovery token for the cluster from [https://discovery.etcd.io/new](https://discovery.etcd.io/new). This token will be the same for every node on the cluster.
* Create another file called *cloud-config.yaml*:

    `vi cloud-config.yaml`

* The "cloud-config.yaml" will contain such things as the hostname:

    `#cloud-config`

    `coreos:`

   ` etcd:`

    `addr: "10.x.x.x:4001"`

    `discovery: "https://discovery.etcd.io/'discovery-seed'"`

    `name: coreos0`

    `peer-addr: "10.x.x.x:7001"`

    `units:`

   ``- command: start``

    `name: etcd.service`

    `- command: start`

    `name: fleet.service`

    `hostname: coreos0`

    `ssh_authorized_keys:`

    `- "ssh-rsa 'Place RSA public key here'"`

    `write_files:`

    `- path: /etc/environment`

    `permissions: 0644`

    `content: |`

    `COREOS_PUBLIC_IPV4=10.x.x.x`

    `COREOS_PRIVATE_IPV4=10.x.x.x`


    `# Generate a new token for the cluster from https://discovery.etcd.io/new paste token from https://discovery.etcd.io/new above; remember space after discovery:)`

* Ensure that hard drive SDA is correct with the command

    `ls -lah /dev/sd*`

* Execute the file *coreos_install.sh*

    `sudo ./coreos_install.sh -d /dev/sda -C stable -c cloud-config.yaml`

* Reboot the new coreos server

* Login remotely with the private key generated earlier

    `ssh -i /tmp/CoreOSBM_rsa core@10.x.x.x`

* Repeat this process for additional nodes using the same steps; however, use a different hostname and ipaddress
