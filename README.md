# openstack-ansible-lxc
An easy deployment of OpenStack (Kilo) on LXC using Ansible

Make sure you have at least 12GB of Memory to install
 
[Download Ubuntu 14.04.2 Trusty Tahr](http://releases.ubuntu.com/14.04/ubuntu-14.04.2-server-amd64.iso)

1. Install Ubuntu as you normally would
2. After Ubuntu is installed, SSH in as root and follow the commands below

```
apt-get update && apt-get install -y git
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

### Run the script from the root directory of the cloned repository
```
RUN_TEMPEST=no ./scripts/gate-check-commit.sh
```
Deployment takes around an hour to complete

To access Horizon, open your browser and go to the host machine's IP address. 
Username: admin
Password: secretes
