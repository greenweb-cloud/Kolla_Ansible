#  Kolla Ansible

Kolla Ansible is an open-source project that provides a set of Ansible playbooks and roles for deploying and managing containerized applications using OpenStack. It is part of the OpenStack community and is used to build, deploy, and manage containerized OpenStack services.

Kolla Ansible is designed to simplify the deployment and management of containerized applications. It is built on top of Ansible, which is a powerful automation tool that can be used to manage infrastructure and applications. With Kolla Ansible, users can deploy containerized applications quickly and easily, with minimal manual intervention.

Kolla Ansible supports a wide range of containerization technologies, including Docker, Kubernetes, and OpenStack's own containerization tool, Magnum. It also supports a variety of operating systems, including CentOS, Fedora, Ubuntu, and Red Hat Enterprise Linux.

# key features
Some of the key features of Kolla Ansible include:

1. Simplified deployment: Kolla Ansible provides a set of Ansible playbooks and roles that make it easy to deploy containerized applications.

2. Modular architecture: Kolla Ansible is designed to be modular, with each component of an application deployed as a separate container. This allows for greater flexibility and easier management of the application.

3. Compatibility with OpenStack: Kolla Ansible is designed to work with OpenStack, making it easy to deploy and manage containerized OpenStack services.

4. Support for multiple containerization technologies: Kolla Ansible supports a variety of containerization technologies, making it flexible and adaptable to different environments.

Overall, Kolla Ansible is a powerful tool for deploying and managing containerized applications. Its modular architecture, compatibility with OpenStack, and support for multiple containerization technologies make it a popular choice for organizations that want to deploy containerized applications quickly and easily.

## Prerequisites

Before starting the deployment process, you will need the following:

A clean installation of a supported Linux distribution (CentOS 7, CentOS 8, RHEL 7, RHEL 8, or Ubuntu 22.04)
- A user with sudo privileges
- At least 8 GB of RAM and 4 CPUs
- Python 3.6 or higher
- Ansible 2.9 or higher


## Pre-Deploy Steps

### 1. update the packages:
Update ubuntu to get information on the newest versions of packages and their dependencies:

```
 sudo apt update
```

### 2. Install dependencies:
Run the following command to install the dependencies required for Kolla Ansible:

```
sudo apt install git python3-dev libffi-dev gcc libssl-dev
```

### 3. virtual environment
To avoid conflicts with the system site-packages, we will use a virtual environment to install Kolla Ansible and its dependencies.
Use the following command to install the virtual environment package.

Install dependencies for the virtual environment:

```
sudo apt install python3-venv
```


Create a virtual environment and activate it:
```
python3 -m venv ~/kolla-ansible
source ~/kolla-ansible/bin/activate
```
The virtual environment should be activated before running any commands that depend on packages installed in it.



### 4. Install dependency packages using virtual environment
Next we must handle some more dependencies before we go ahead and install Kolla Ansible. Upgrade the pip to the latest version using the command.

```
pip install -U pip
```

Install Ansible. Kolla Ansible requires at least Ansible 2.10 and supports up to 4.
```
pip install 'ansible>=4,<6'
```

Install Docker SDK for Python.
```
apt-get install python3-docker
```


## Install and Setup Kolla Ansible

1. Install kolla-ansible and its dependencies using pip.

```
pip install git+https://opendev.org/openstack/kolla-ansible@master
```

2. Create the /etc/kolla directory.
```
sudo mkdir -p /etc/kolla
sudo chown $USER:$USER /etc/kolla
```

3. Copy globals.yml and passwords.yml to /etc/kolla directory.

```
cp -r ~/kolla-ansible/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
```

4. Copy all-in-one inventory file to the current directory.

Inventory files are basically which are used to tell the kolla-ansible which are our hosts and type of installation we need on these hosts.
```
cp -r ~/kolla-ansible/share/kolla-ansible/ansible/inventory/all-in-one /etc/kolla
```

## Install Ansible Galaxy requirements
Install Ansible Galaxy dependencies:

```
kolla-ansible install-deps
```

## Prepare initial configuration

### Inventory
Kolla Ansible comes with all-in-one and multinode example inventory files. The difference between them is that the former is ready for deploying single node OpenStack on localhost. In this guide we will show the all-in-one installation.

### Kolla passwords
Passwords used in our deployment are stored in /etc/kolla/passwords.yml file. All passwords are blank in this file and have to be filled either manually or by running random password generator:

```
kolla-genpwd
```

### Kolla globals.yml

global.yml file is the most important file. In this file, we mention that which OpenStack projects we want to install. For kolla docker image what base OS do we want. globals.yml file describes our whole deployment. In coming blogs, we will look more closely at this configuration file.

For now, we want only those projects which are most important for OpenStack deployment. Under Neutron -Networking Option heading I changed the network interface according to my interface name.

Sample output:

```
workaround_ansible_issue_8743: yes
kolla_base_distro: "ubuntu"
network_interface: "eth0"
neutron_external_interface: "eth1"
kolla_internal_vip_address: "server_ip"
enable_cinder: "no"
enable_haproxy: "no"
```

## Deployment

When the configuration is done. Firstly, we need to install docker or other important packages.

```
kolla-ansible -i ./etc/kolla/all-in-one bootstrap-servers
```

Do deployment checks. Similarly, like bootstrapping if everything is fine then there would be no error.

```
kolla-ansible -i ./etc/kolla/all-in-one prechecks
```

In the last step we will perform deployment.

```
kolla-ansible -i ./etc/kolla/all-in-one deploy
```

Now install the Openstack client and its dependencies using pip.

```
pip install python-openstackclient
```


##  Post Deployment
Now create “admin-openrc” file that basically contains admin credentials.

```
kolla-ansible post-deploy  /etc/kolla/admin-openrc.sh
```

Now in the next step we will run the deployment.

```
source /etc/kolla/admin-openrc.sh
```


The initialization script will create virtual machine resources such as cirros image, network, subnet, routing, security group, specification, quota, etc.

```
./init-runonce
```

## License

[MIT](https://choosealicense.com/licenses/mit/)
