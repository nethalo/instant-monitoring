# Instant Monitoring (Or how to deploy Nagios without worries). Focused on MySQL
Knowing what's going on inside your databases is priceless. And configuring Nagios on a automated basis is better.

Using [Ansible] (http://docs.ansible.com/) playbooks you can easily achieve this. Let's setup Nagios with NRPE monitoring

The **ultimate goal** is to install and configure the [PERCONA monitoring plugins for Nagios] (http://www.percona.com/doc/percona-monitoring-plugins/1.1/nagios/index). The Percona monitoring plugins (or PMP) is a group of scripts created by MySQL experts, with good documentation, support for the newest versions of MySQL and InnoDB, great integration with other Percona software, such as Percona Server and Percona Toolkit. And the best: Easy to install and configure!


### Installing Ansible
Ansible in a nutshell
#### Requirements
* Python (2.4 or greater)
* Python modules: 
	* python-simplejson (if using python 2.4)
	* python-mysqldb (For MySQL specific tasks)
* OpenSSH

#### Installing

##### Mac OS X
The most easy way is using [Homebrew] (http://brew.sh/). Install Brew and then:
```
brew install ansible
```
##### Debian and friends
If you're lucky, you have ansible on apt:
```
apt-get install ansible
```
However, most likely is that Ansible package have an old version (1.5) so it will be better to install from source

##### Red Hat and friends
Using yum
```
yum install ansible
```
##### Installing from source
Ansible can be installed by cloning the git repo. All the info can be found on the Documentation for [installing from source] (http://docs.ansible.com/intro_installation.html#running-from-source)

## Steps

### Launch the VMs
vagrant up monitor server and nodes

### Create a user with sudo grants on the VMs and set the public key (this is just for easy play)
Since i don't like to write the user password e-v-e-r-y time a playbook is executed, i just create a user on all the VMs that will use my ssh public key (Actually, it will use any key that resides in the path "~/.ssh/id\_rsa.pub") and i give sudo with no password to that user. Less risky options available but not in this repository :)

```
ansible-playbook plays/set-keys.yml --ask-pass
```

### Install the Nagios server. 
This machine will be the monitoring box. 
Things installed:
* Nagios core (compiled)
* Nagios plugins (compiled)
* Nagios NRPE plugin
* ...And all the friends that Nagios need to run happy (Apache, php, etc...)

Also, it sets the user and password required to open the Dashboard url. In this case, user/password are: nagiosadmin/nagiosadmin

```
ansible-playbook plays/install-nagios-server.yml
```

### Set the Percona repository on the nodes (And install MySQL)
We need something to be subject of monitoring! In this step, the Percona repository is installed and also the Percona Server will be set up

```
ansible-playbook plays/basic-mysql.yml
```

### Install Nagios NRPE on the nodes.
All the monitored nodes needs to run the NRPE server and the Nagios plugins. It's just a couple of packages available in the repo.
Additionaly, the Percona Nagios Plugins are installed. This guys will perform the MySQL monitoring magic

```
ansible-playbook plays/install-nrpe-nodes.yml
```

### Configure Nagios to check services on the nodes
This step is the reason for all this trouble. This playbook tells the Nagios server the hosts that needs to check and the services on thoses hosts to monitor.

```
ansible-playbook plays/configure-services.yml
```
