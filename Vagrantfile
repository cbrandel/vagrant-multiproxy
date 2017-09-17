# -*- mode: ruby -*-
# vi: set ft=ruby :
# requires plugins:
# - vagrant-hostmanager
# - vagrant-bindfs
# 192.168.50.30 machine.multiproxy
# 192.168.50.31 web.multiproxy
# 192.168.50.32 app.multiproxy
# absolute path to Ansible directory on host machine
ANSIBLE_PATH = File.join(__dir__, 'ansible')
# absolute path to Ansible directory on virtual machine
ANSIBLE_PATH_ON_VM = File.join('/home/vagrant/ansible')

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
  # Synced folder
  if Vagrant::Util::Platform.windows? and !Vagrant.has_plugin? 'vagrant-winnfsd'
    config.vm.synced_folder ANSIBLE_PATH, ANSIBLE_PATH_ON_VM, mount_options: ['dmode=755', 'fmode=644']
  elsif !Vagrant.has_plugin? 'vagrant-bindfs'
    fail_with_message "vagrant-bindfs missing, please install the plugin with this command:\nvagrant plugin install vagrant-bindfs"
  else
    config.vm.synced_folder ANSIBLE_PATH, '/vagrant-nfs', type: 'nfs'
    config.bindfs.bind_folder '/vagrant-nfs', ANSIBLE_PATH_ON_VM, o: 'nonempty', p: '0644,a+D'
  end
  # Webserver machine
  config.vm.define 'web' do |machine|
    machine.vm.hostname = 'web'
    machine.vm.network :private_network, ip: '192.168.50.31'
    machine.vm.provider 'virtualbox' do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--name', 'multiproxy-web']
      v.linked_clone = true
    end
    machine.hostmanager.aliases = 'web.dmz'
    config.vm.provision 'shell' do |s|
      ssh_insecure_key = File.read("#{Dir.home}/.vagrant.d/insecure_private_key")
      s.inline = <<-SHELL
        echo '#{ssh_insecure_key}' > /home/vagrant/.ssh/id_rsa
        chown vagrant /home/vagrant/.ssh/id_rsa
        chmod 400 /home/vagrant/.ssh/id_rsa
      SHELL
    end
  end
  # Appserver machine
  config.vm.define 'app' do |machine|
    machine.vm.hostname = 'app'
    machine.vm.network :private_network, ip: '192.168.50.32'
    machine.vm.provider 'virtualbox' do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--name', 'multiproxy-app']
      v.linked_clone = true
    end
    machine.hostmanager.aliases = 'app.dmz'
    config.vm.provision 'shell' do |s|
      ssh_insecure_key = File.read("#{Dir.home}/.vagrant.d/insecure_private_key")
      s.inline = <<-SHELL
        echo '#{ssh_insecure_key}' > /home/vagrant/.ssh/id_rsa
        chown vagrant /home/vagrant/.ssh/id_rsa
        chmod 400 /home/vagrant/.ssh/id_rsa
      SHELL
    end
  end
  # Loadbalancer machine
  config.vm.define 'lb' do |machine|
    machine.vm.hostname = 'lb'
    machine.vm.network :private_network, ip: '192.168.50.30'
    machine.vm.provider 'virtualbox' do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--name', 'multiproxy-lb']
      v.linked_clone = true
    end
    machine.hostmanager.aliases = 'mysite'
    # Provisioning
    config.vm.provision 'shell' do |s|
      ssh_insecure_key = File.read("#{Dir.home}/.vagrant.d/insecure_private_key")
      s.inline = <<-SHELL
        echo '#{ssh_insecure_key}' > /home/vagrant/.ssh/id_rsa
        chown vagrant /home/vagrant/.ssh/id_rsa
        chmod 400 /home/vagrant/.ssh/id_rsa
      SHELL
    end
    provisioning_path = ANSIBLE_PATH_ON_VM
    machine.vm.provision 'ansible_local', run: 'always' do |ansible|
      ansible.compatibility_mode = '2.0'
      ansible.provisioning_path = provisioning_path
      ansible.inventory_path = File.join(provisioning_path, 'inventory')
      ansible.playbook = File.join(provisioning_path, 'site.yml')
      ansible.config_file = File.join(provisioning_path, 'ansible.cfg')
      ansible.galaxy_role_file = File.join(provisioning_path, 'requirements.yml')
      ansible.galaxy_roles_path = File.join(provisioning_path, 'vendor')
      ansible.become         = true
      ansible.verbose        = true
      ansible.install        = true
      ansible.limit          = 'all'
    end
  end

end
