# openstack-ansible-lxc
An easy deployment of OpenStack (Kilo) on LXC using Ansible based on (https://github.com/stackforge/os-ansible-deployment)

The Ansible Playbook Installs:

* Keystone
* *Ceilometer
* Swift
* Glance
* Cinder
* Nova
* Neutron
* Heat
* Horizon

Make sure you have at least 12GB of Memory to install (It will run on 8GB of Memory however, performance will be impacted)
 
[Download Ubuntu 14.04.2 Trusty Tahr](http://releases.ubuntu.com/14.04/ubuntu-14.04.2-server-amd64.iso)

1. Install Ubuntu as you normally would
2. After Ubuntu is installed, SSH in as root and follow the commands below

```
apt-get update && apt-get install -y git && apt-get install ansible -y
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

### Bootstrap Ansible
```
./scripts/bootstrap-ansible.sh
```

### Bootstrap AIO
```
./scripts/bootstrap-aio.sh
```

### Run the script from the root directory of the cloned repository
```
RUN_TEMPEST=no ./scripts/run-aio-build.sh
```

Or you can run the the setup-everything.yml playbook

```
cd playbooks
```

```
openstack-ansible setup-everything.yml
```

Deployment takes around 60 minutes to complete or 120 minutes if Tempest is enabled. 

To access Horizon, open your browser and go to the host machine's IP address. 
* Username: admin
* Password: (is generated randomly - you can find it in the openrc file)

```
root@osad2:~# nano openrc
```

##To use the CLI 

```
root@osad2:~# source openrc
```
```
root@osad2:~# nova list
+----+------+--------+------------+-------------+----------+
| ID | Name | Status | Task State | Power State | Networks |
+----+------+--------+------------+-------------+----------+
+----+------+--------+------------+-------------+----------+
```

### Rebuilding the Stack

If you'd like to tear down the stack and restart from a new build there is a play that will assist you in doing just that. Simply change to your playbooks directory and execute the ``lxc-containers-destroy.yml`` play.

Example:

```
.. code-block:: bash
```

Move to the playbooks directory.
  ```
  cd /opt/os-ansible-deployment/playbooks
  ```
  Destroy all of the running containers.
  ```
  openstack-ansible lxc-containers-destroy.yml
  ```
  On the host stop all of the services that run locally and not within a container.
  ```
  for i in $(ls /etc/init | grep -e nova -e swift -e neutron | awk -F'.' '{print $1}'); do service $i stop; done
  ```
  Uninstall the core services that were installed.
  ```
  for i in $(pip freeze | grep -e nova -e neutron -e keystone -e swift); do pip uninstall -y $i; done
  ```
  Remove crusty directories.
  ```
  rm -rf /openstack /etc/neutron /etc/nova /etc/swift /var/log/neutron /var/log/nova /var/log/swift
  ```

### Using the teardown script:
The ``teardown.sh`` script that will destroy everything known within an environment. You should be aware that this script will destroy whole environments and should be used **WITH CAUTION**.

### Note

The system uses a number of variables. You should look a the scripts for a full explanation and description of all of the available variables that you can set. At a minimum you should be aware of the default public interface variable as you may be kicking on a box that does not have an ``eth0`` interface. To set the default public interface run the following.
```
.. code-block:: bash
```
    ```
    export PUBLIC_INTERFACE="<<REPLACE WITH THE NAME OF THE INTERFACE>>" # This is only required if you dont have eth0
    ```

This play will destroy all of your running containers and remove items within the ``/openstack`` directory for the container. After the completion of this play you can rerun the ``cloudserver-aio.sh`` or you can run the plays manually to rebuild the stack.
