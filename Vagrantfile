# Note: install vagrant-hostsupdater first 
# 
# vagrant plugin update vagrant-hostsupdater
#
IMAGE_NAME = "geerlingguy/centos7"
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 3072
        v.cpus = 3
    end
      
    config.vm.define "master" do |master|
        config.hostmanager.enabled = true
        config.hostmanager.manage_host = false
        config.hostmanager.manage_guest = true
        config.hostmanager.ignore_private_ip = false
        config.hostmanager.include_offline = true
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.168.50.10"
        master.vm.hostname = "master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.50.10",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            config.hostmanager.enabled = true
            config.hostmanager.manage_host = false
            config.hostmanager.manage_guest = true
            config.hostmanager.ignore_private_ip = false
            config.hostmanager.include_offline = true
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                }
            end
        end
    end
end