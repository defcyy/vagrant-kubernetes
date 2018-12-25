# -*- mode: ruby -*-
# vi: set ft=ruby :

KUBE_TOKEN = "c5hg11.vrs9h8yf1ol489bp"
NODE_COUNT = 2

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"

  config.vm.provider "vmware_desktop" do |v|
    v.vmx["memsize"] = "2048"
    v.vmx["numvcpus"] = "2"
  end

  master_ip = "192.168.33.100"
  config.vm.define "master", primary: true do |master|
    master.vm.network "private_network", ip: master_ip
    master.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/master.yaml"
      ansible.groups = {
        "master" => master_ip
      }
      ansible.extra_vars = {
        "master_ip" => master_ip,
        "kube_token" => KUBE_TOKEN,
        "hostname" => "master"
      }
    end
  end

  (1..NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |node|
      node_ip = "192.168.33.10#{i}"
      node.vm.network :private_network, ip: node_ip
      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/node.yaml"
        ansible.groups = {
          "hosts" => node_ip
        }
        ansible.raw_arguments = ["-i", node_ip]
        ansible.extra_vars = {
          "master_ip" => master_ip,
          "kube_token" => KUBE_TOKEN,
          "hostname" => "node#{i}"
        }
      end
    end

  end
end
