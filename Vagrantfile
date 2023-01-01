# Note: install vagrant-hostsupdater first 
# 
# vagrant plugin update vagrant-hostsupdater
#
IMAGE_NAME = "generic/ubuntu2204"
N = 2

Vagrant.configure("2") do |config|
    config.vm.box_check_update = false
    config.ssh.forward_agent = true

    # https://medium.com/@Sohjiro/add-public-key-to-vagrant-4bd5424521bf
    config.ssh.insert_key = false # 1
    config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa'] # 2
    config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys" # 3

    # 4
    config.vm.provision "shell", inline: <<-EOC
      sudo sed -i -e "\\#PasswordAuthentication yes# s#PasswordAuthentication yes#PasswordAuthentication no#g" /etc/ssh/sshd_config
      sudo systemctl restart sshd
      echo "finished"
    EOC

    config.vm.provider "virtualbox" do |v|
        v.memory = 3072
        v.cpus = 2
        v.customize ["modifyvm", :id, "--vram", "6"]
        v.customize ['modifyvm', :id, '--graphicscontroller', 'vmsvga']
    end
      
    config.vm.define "master" do |master|
        # https://serverfault.com/questions/441431/cant-ssh-into-a-vagrant-virtual-machine
        # Enable ssh forward agent
        config.ssh.forward_agent = true
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "10.0.0.10"
        master.vm.hostname = "master"

        # see https://www.sitepoint.com/vagrantfile-explained-setting-provisioning-shell/
        master.vm.synced_folder "./shared-data/master", "/home/vagrant/shared", create: true, group: "vagrant", owner: "vagrant"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "10.0.0.10",
                host_name: "master"
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            config.ssh.forward_agent = true
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "10.0.0.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            
            node.vm.synced_folder "./shared-data/node-#{i}", "/home/vagrant/shared", create: true, group: "vagrant", owner: "vagrant"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "10.0.0.#{i + 10}",
                    host_name: "node-#{i}"
                }
            end
        end
    end
end