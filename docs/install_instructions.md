# Running the AIO

The AIO can be installed using Vagrant (with Ansible) or Ansible directly to an existing machine.

## Pre-requisites

### Hardware

A server with 16G Memory, 4+ CPUs, 200G disk is required to run the AIO LM environment.

### Software dependencies

#### Ansible

To install the AIO you will need [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) (tested on v2.8.1) on your machine. You may also need sshpass installed if password access is being used to the server.

```
sudo apt-get install sshpass
```

Ensure `python-apt` is installed, this may be needed when using v2.8+

```
sudo apt-get install python-apt
```

#### Vagrant

For a Vagrant install, the following software must be installed on your host machine:

- [Vagrant](https://www.vagrantup.com/)
- [Virtualbox](https://www.virtualbox.org/) (support for libvirt and hyperkit are also provided in the Vagrantfile)

#### Mac OS X Specific

If installing the AIO on a Mac environment using Vagrant, please follow the instructions in [Mac OS X - Hyperkit](hyperkit.md) before continuing.

#### Ansible-Roles

The AIO playbook depends on several ansible-roles, to pull them you must use `ansible-galaxy` provided with Ansible:

```
ansible-galaxy install -r ansible/requirements.yml -p ansible/roles
```

#### Stratoss Lifecycle Manager

**Note:** can be skipped if connecting your AIO to a CICDHub

AIO requires 2 Stratoss LM artifacts:

- Lifecycle Manager helm charts
- Lifecycle Manager docker images

Once you have obtained a version of each, from your usual release cycle, copy each to `lm-artifacts`:

```
/lm-artifacts
  lm-docker-source-2.0.3-207-dist.tgz
  lm-helm-charts-2.0.3-207-dist.tgz
```

Then update the references to these files in `ansible/ansible-variables.yml`:

```
lm_charts_package: ../lm-artifacts/lm-docker-source-2.0.3-207-dist.tgz
lm_docker_package: ../lm-artifacts/lm-helm-charts-2.0.3-207-dist.tgz
```

## Configuration

### Connecting the AIO to a CICDHub

If you have access to a [CICDHub](https://github.com/accanto-systems/lm-cicdhub) environment, you can connect your AIO environment to it by downloading the `aio-variables.yml` from it's Nexus repository (usual path: [http://cicdhub_ip:8002/repository/raw/allinone/aio-variables.yml]). Contact your CICDHub admin for more details.

The potential benefits to connecting to a CICDHub are:

- shared Openldap - your user account only needs to be defined once
- shared lm-artifacts - no need to find LM installation packages, they can be downloaded from the CICDHub

Once an `aio-variables` file has been obtained, modify the `ansible/start-aio.yml` file to include it:

```
vars_files:
    - ansible-variables.yml
    - aio-variables.yml
```

Alternatively, when installing with ansible, include this option on the `ansible-playbook` command:

```
-e @aio-variables.yml
```

Please continue with the remainder of this install guide before installing, there may still variables which require configuring.

### Variables

Before installing the AIO (with Vagrant or Ansible) read through the `ansible/ansible-variables.yml` file and update as required.

You can enable/disable features, configure location of helm charts and docker registries etc. This file includes commented documentation to explain the purpose of each variable.

At minimum it is recommended that you consider updating the following to ensure the defaults will allow you to access your environment correctly (the defaults are correct for a Vagrant install):

```
hostname: "allinone"
advertise_address: 192.168.56.100
flannel_interface: "eth1"
```

In addition it is recommended you consider updating the following passwords to prevent use of the defaults:

```
lm_ldap_manager_password:

lm_admin_secret:
```

**Note:** lm_ldap_manager_password can be skipped if connecting your AIO to a CICDHub

## Install with Vagrant

By default, the AIO environment will attach to a private network with the IP address "192.168.56.100", which is configured by the following line in Vagrantfile:

```
nodeconfig.vm.network 'private_network', ip: '192.168.56.100'
```

If desired, the IP address can be changed to another IP address on a private network (see [Private Network](https://www.vagrantup.com/docs/networking/private_network.html) for more details), or to an IP address on a public network (see [Private Network](https://www.vagrantup.com/docs/networking/public_network.html) for more details), in which case it will allow public access to your AIO environment (be careful to consider the security implications if you are doing this - see the Vagrant documentation above for details). For example, to assign a public network IP address using DHCP:

```
nodeconfig.vm.network "public_network"
```

Or to explicitly set the IP address:

```
nodeconfig.vm.network "public_network", ip: "192.168.0.17"
```

To create the AIO environment with Vagrant, run vagrant up, specifying a provider if necessary (default is virtualbox)

```
vagrant up
```

```
vagrant up --provider=libvirt
```

Note: due to a shortcoming of Hyperkit, Vagrant must be run as root for the Hyperkit plugin.

```
sudo vagrant up --provider=hyperkit
```

## Install AIO with Ansible

In some cases you may prefer to install the AIO environment on an existing server rather than using the included Vagrantfile. This section details how to use Ansible to achieve this, by executing the playbooks on a target inventory.

### Configuring Inventory

Before running the playbook you must modify the `ansible/inventories/allinone/host_vars/allinone.yml` file with details of the target host for the AIO to be installed.

At minimum you will need to configure the address and credentials used for SSH access (values shown are only examples):

```
ansible_host: 192.168.65.2
ansible_ssh_user: vagrant
ansible_ssh_pass: vagrant
ansible_become_pass: vagrant
```

_Note: The AIO playbooks will require access to the `sudo` user_

The inventory may be configured in any way supported by Ansible, meaning you may choose to use SSH passwords or SSH keys and you may choose to store values in plain-text or with ansible-vault. See [Build Your Inventory](https://docs.ansible.com/ansible/latest/network/getting_started/first_inventory.html) and [List of behavioual inventory parameters](https://docs.ansible.com/ansible/latest/intro_inventory.html#list-of-behavioral-inventory-parameters) from the Ansible documentation.

If you have configured access to your target server inventory using `ansible_ssh_pass` then it is common that Ansible will fail if the target host is not included in your known_hosts file.

```
fatal: [aio-host]: FAILED! => {"msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."}
```

This can be avoided in several ways:

- Add the host to your known_hosts file
- Disable Ansible host checking globally: `export ANSIBLE_HOST_KEY_CHECKING=False`
- Disable Ansible host checking in the host_vars for your target host: `ansible_ssh_extra_args: '-o StrictHostKeyChecking=no'`

### Configuring Variables

Revisit the following variables in the `ansible/ansible-variables.yml` file, ensuring they are correct for your target machine:

```
hostname: "allinone"
advertise_address: 192.168.56.100
flannel_interface: "eth1"
```

### Run the Playbook

Run the `start-aio.yml` playbook, referencing the inventory modified earlier.

```
# Standard
ansible-playbook -i ansible/inventories/allinone/inventory ansible/start-aio.yml

# If connecting to a CICDHub and opted to include variables on command line
ansible-playbook -i ansible/inventories/allinone/inventory ansible/start-aio.yml -e @aio-variables.yml
```

## Accessing the environment

When the installation is finished the following services can be accessed as follows:

| **AIO Services**         | Address                                                  |
| ------------------------ | -------------------------------------------------------- |
| **LM-UI**                | <https://YOUR_SERVER:8080>                               |
| **LM-API**               | <https://YOUR_SERVER:8081>                               |
| **Kibana**               | <http://YOUR_SERVER:8083>                                |
| **Portainer**            | <http://YOUR_SERVER:9000>                                |
| **Ansible RM**           | <https://YOUR_SERVER:31081/api/v1.0/resource-manager/ui> |
| **Kubernetes Dashboard** | <http://YOUR_SERVER:31443>                               |
