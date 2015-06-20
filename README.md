# openstack-ansible-lxc
An easy deployment of OpenStack (Kilo) on LXC using Ansible based on (https://github.com/stackforge/os-ansible-deployment)

The Ansible Playbook Installs:

* Keystone
* Swift
* Glance
* Cinder
* Nova
* Neutron
* Heat
* Horizon

```
  ====== ASCII Diagram for AIO infrastructure ======

              ------->[ ETH0 == Public Network ]
              |
              V                        [  *   ] Socket Connections
    [ HOST MACHINE ]                   [ <>v^ ] Network Connections
      *       ^  *
      |       |  |-----------------------------------------------------
      |       |                                                       |
      |       |---------------->[ HAProxy ]                           |
      |                                 ^                             |
      |                                 |                             |
      |                                 V                             |
      |                          (BR-Interfaces)<-----                |
      |                                ^     *      |                 |
      *-[ LXC ]*--*--------------------|-----|------|----|            |
      |           |                    |     |      |  | |            |
      |           |                    |     |      |  | |            |
      |           |                    |     |      |  | |            |
      |           |                    |     |      V  * |            |
      |           *                    |     |   [ Galera x3 ]        |
      |        [ Memcached ]<----------|     |           |            |
      *-------*[ Rsyslog ]<------------|--|  |           *            |
      |        [ Repos Server x3 ]<----|  ---|-->[ RabbitMQ x3 ]      |
      |        [ Horizon ]<------------|  |  |                        |
      |        [ Nova api ec2 ]<-------|--|  |                        |
      |        [ Nova api os ]<--------|->|  |                        |
      |        [ Nova spice console ]<-|  |  |                        |
      |        [ Nova Cert ]<----------|->|  |                        |
      |        [ Cinder api ]<---------|->|  |                        |
      |        [ Glance api ]<---------|->|  |                        |
      |        [ Heat apis ]<----------|->|  | [ Loop back devices ]*-*
      |        [ Heat engine ]<--------|->|  |    \        \          |
      | ------>[ Nova api metadata ]   |  |  |    { LVM }  { XFS x3 } |
      | |      [ Nova conductor ]<-----|  |  |       *         *      |
      | |----->[ Nova scheduler ]------|->|  |       |         |      |
      | |      [ Keystone x3 ]<--------|->|  |       |         |      |
      | | |--->[ Neutron agents ]*-----|--|---------------------------*
      | | |    [ Neutron server ]<-----|->|          |         |      |
      | | | |->[ Swift proxy ]<---------  |          |         |      |
      *-|-|-|-*[ Cinder volume ]*--------------------*         |      |
      | | | |                             |                    |      |
      | | | ---------------------------------------            |      |
      | | --------------------------------------- |            |      |
      | |          -----------------------|     | |            |      |
      | |          |                            | |            |      |
      | |          V                            | |            *      |
      ---->[ Compute ]*[ Neutron linuxbridge ]<-| |->[ Swift storage ]-
```

Make sure you have at least 12GB of Memory to install
 
[Download Ubuntu 14.04.2 Trusty Tahr](http://releases.ubuntu.com/14.04/ubuntu-14.04.2-server-amd64.iso)

1. Install Ubuntu as you normally would
2. After Ubuntu is installed, SSH in as root and follow the commands below

```
apt-get update && apt-get install -y git
```

### Install Ansible
```
sudo apt-get install ansible -y
```
### Clone the source code
```
git clone https://github.com/stackforge/os-ansible-deployment /opt/os-ansible-deployment
```
### Change your directory
```
cd /opt/os-ansible-deployment
```
### Checkout the Kilo Branch (or master for latest code)
```
git checkout kilo
```
or
```
git checkout master
```
### Bootstrap AIO
```
./scripts/bootstrap-aio.sh
```

### Run the script from the root directory of the cloned repository
```
RUN_TEMPEST=no ./scripts/run-aio-build.sh
```
Deployment takes around 60 minutes to complete or 120 minutes if Tempest is enabled. 

To access Horizon, open your browser and go to the host machine's IP address. 
* Username: admin
* Password: (is generated randomly - you can find it in the openrc file)
