## Important note

This is a POC, hence liberties are taken: e.g. no TLS is implemented, Grafana admin user/pass is default, etc.

It is intended to do some Prometheus benchmarking with [Avalanche](https://github.com/open-fresh/avalanche)


## Requirements

This environment has been tested with Vagrant 2.2.6, libvirt 4.0.0, Ansible 2.9.4 and Ubuntu 18.04.4 LTS.

To install Ansible, Vagrant and libvirt:

```
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible

$ wget https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb
$ sudo dpkg -i vagrant_2.2.6_x86_64.deb

# libvirt-dev package needed for building vagrant-libvirt plugin
$ sudo apt-get install qemu-kvm libvirt-bin libvirtd-daemon libvirt-dev
$ sudo apt-get install ruby-libvirt

# After the following step the user needs to login again so that group memberships are reloaded
$ sudo adduser <user> libvirt
Adding user `<user>' to group `libvirt' ...
Adding user <user> to group libvirt
Done.

```

Optionally you can install vagrant-hostmanager plugin for handling the /etc/host file (for your host machine) in order to add automatically the names for the new VMs created.

Adding the plugin:

```
$ vagrant plugin install vagrant-hostmanager
Installing the 'vagrant-hostmanager' plugin. This can take a few minutes...
Installed the plugin 'vagrant-hostmanager (1.8.9)'!

$ vagrant plugin install vagrant-libvirt
Installing the 'vagrant-libvirt' plugin. This can take a few minutes...
Fetching: formatador-0.2.5.gem (100%)
Fetching: excon-0.69.1.gem (100%)
Fetching: fog-core-1.43.0.gem (100%)
Fetching: mini_portile2-2.4.0.gem (100%)
Fetching: nokogiri-1.10.5.gem (100%)
Building native extensions.  This could take a while...
Fetching: fog-json-1.2.0.gem (100%)
Fetching: fog-xml-0.1.3.gem (100%)
Fetching: ruby-libvirt-0.7.1.gem (100%)
Fetching: fog-libvirt-0.7.0.gem (100%)
Fetching: vagrant-libvirt-0.0.45.gem (100%)
Installed the plugin 'vagrant-libvirt (0.0.45)'!

$ vagrant plugin list
vagrant-hostmanager (1.8.9)
vagrant-libvirt (0.0.45, system)

```

The vagrant-hostmanager plugin is configured in this project for using manually. Our proposal is the following behaviour:

```
$ vagrant up
$ vagrant hostmanager (populate /etc/host for host machine)
$ vagrant destroy
$ vagrant hostmanager (erase /etc/host entries for host machine)
```

## Components

The following machines and services will be deployed when you run `vagrant up` (please check the CPU and RAM resources to be sure you do not need to change them):

 - An 'avalanche' machine (2 CPUs, 2 Gib RAM) with:
    - Docker
    - Node exporter
    - Three Avalanche containers generating 5000, 50000 and 100000 time series with no churn (ports 9001, 9002, 9003).

 - Three VMs called 'prom1' (1 CPU, 2 GiB RAM), 'prom2' (1 CPU, 2 GiB RAM) and 'prom3' (1 CPUs, 4 GiB RAM) with:
    - Docker
    - Node exporter
    - Prometheus & Alertmanager (ports 9090 and 9093):
        - 'prom1' scrapes the Avalanche container with 5000 time series.
        - 'prom2' scrapes the Avalanche container with 50000 time series.
        - 'prom3' scrapes the Avalanche container with 100000 time series.
    - Grafana in 'prom1' (port 3000, admin/admin user/pass) configured with the three Prometheis as Datasources and a dashboard to monitor the Prometheis by selecting a variable.

Remember to run `vagrant hostmanager` to populate your `/etc/hosts`. 