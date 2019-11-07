
#Setup K8s with Vagrant and Virtualbox#

This vagrant template is to setup Kubenetes enviroment with Vagrant and Virtualbox on my Mac.

##Steps##

###Step 1###

* Specify the version for your Kubenetes. 
  See k8s_version in ./kubernetes-setup/master-playbook.yml and ./kubernetes-setup/node-playbook.yml.


###Step 2###

```
# install vagrant-hostmanager first 
vagrant plugin install vagrant-hostmanager

# setup guest hosts
vagrant up

# update /etc/hosts, after hosts are all built
vagrant hostmanager

# make ready for kubectl
scp vagrant@192.168.50.10:/home/vagrant/.kube/config ./admin.conf

```

###Step 3###

scp vagrant@192.168.50.10:/home/vagrant/.kube/config ./admin.conf


##Drawback##



