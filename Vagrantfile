# Note: install vagrant-hostsupdater first 
# 
# vagrant plugin update vagrant-hostsupdater
#
IMAGE_NAME = "geerlingguy/centos7"
N = 2

Vagrant.configure("2") do |config|
    # https://medium.com/@Sohjiro/add-public-key-to-vagrant-4bd5424521bf
    config.ssh.insert_key = false # 1
    config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa'] # 2
    config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys" # 3

    # 4
    config.vm.provision "shell", inline: <<-EOC
      sudo sed -i -e "\\#PasswordAuthentication yes# s#PasswordAuthentication yes#PasswordAuthentication no#g" /etc/ssh/sshd_config
      sudo systemctl restart sshd.service
      echo "finished"
    EOC

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