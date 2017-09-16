# -*- mode: ruby -*-
# vi: set ft=ruby :
# requires plugins:
# - nugrant
# - vagrant-hostmanager
# 192.168.50.30 machine.multiproxy
# 192.168.50.31 web.multiproxy
# 192.168.50.32 app.multiproxy
Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'
  config.vm.box_version = '1611.01'
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.vm.provision :hostmanager
  # ssh
  config.ssh.insert_key = false
  # Loadbalancer machine
  config.vm.define 'lb' do |machine|
    machine.vm.hostname = 'lb'
    machine.vm.network :private_network, ip: '192.168.50.30'
    machine.vm.provider 'virtualbox' do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--name', 'multiproxy-lb']
    end
    machine.hostmanager.aliases = 'mysite.multiproxy'
    # Provisioning
    machine.vm.provision 'ansible_local' do |ansible|
      ansible.compatibility_mode = '2.0'
      ansible.inventory_path = 'ansible/inventory'
      ansible.playbook = 'ansible/site.yml'
      ansible.config_file = 'ansible/ansible.cfg'
      ansible.galaxy_role_file = 'ansible/requirements.yml'
      ansible.galaxy_roles_path = 'ansible/vendor'
      ansible.become         = true
      ansible.verbose        = true
      ansible.install        = true
      ansible.limit          = 'all'
    end
  end
end
