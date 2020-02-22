# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Configure hostmanager to update /etc/hosts
  # https://github.com/devopsgroup-io/vagrant-hostmanager
  config.hostmanager.enabled = false
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = false
  config.hostmanager.ignore_private_ip = false

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"

  # Configure vagrant to use the insecure key in $HOME/.vagrant.d/insecure_private_key
  config.ssh.insert_key = false

  # Set libvirt as provider
  config.vm.provider "libvirt"
  
  config.vm.define "avalanche" do |avalanche|
    avalanche.vm.hostname = "avalanche"

    avalanche.vm.provider "libvirt" do |v|
      v.memory = 2048
      v.cpus = 2
    end

  end

  config.vm.define "prom1" do |prom1|
    prom1.vm.hostname = "prom1"

    prom1.vm.provider "libvirt" do |v|
      v.memory = 2048
      v.cpus = 1
    end

#    prom1.vm.provision :ansible do |ansible|
#      ansible.verbose = "vv"
#      ansible.playbook = "provisioning/site.yml"
#      ansible.groups = {
#              "metrics" => ["prom1"]
#            }
#      ansible.limit = "all"
#    end
  end

  config.vm.define "prom2" do |prom2|
    prom2.vm.hostname = "prom2"

    prom2.vm.provider "libvirt" do |v|
      v.memory = 2048
      v.cpus = 1
    end
  end

  config.vm.define "prom3" do |prom3|
    prom3.vm.hostname = "prom3"

    prom3.vm.provider "libvirt" do |v|
      v.memory = 4096
      v.cpus = 1
    end

    # Ansible provisioning, only launched from the last machine

    prom3.vm.provision :ansible do |ansible|
      ansible.verbose = "vv"
      ansible.playbook = "provisioning/site.yml"
      ansible.groups = {
              "metrics" => ["prom1", "prom2", "prom3"]
            }
      ansible.limit = "all"
    end
  end

end
