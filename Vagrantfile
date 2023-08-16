# Base Image
BOX_IMAGE = "ubuntu/jammy64"
BOX_VERSION = "20220423.0.0"

# max number of worker nodes : Ex) N = 3
N = 2

# Version : Ex) k8s_V = '1.22.7'
k8s_V = '1.27.0'
cni_N = 'calico'

Vagrant.configure("2") do |config|
#-----Manager Node
    config.vm.define "k8s-m" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.box_version = BOX_VERSION
      subconfig.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--groups", "/#{cni_N}-Lab"]
        v.name = "#{cni_N}-k8s-m"
        v.memory = 4096
        v.cpus = 2 
        v.linked_clone = true
      end
      subconfig.vm.hostname = "k8s-m"
      subconfig.vm.synced_folder "./", "/vagrant", disabled: true
      subconfig.vm.network "private_network", ip: "192.168.10.10"
      subconfig.vm.network "forwarded_port", guest: 22, host: 50010, auto_correct: true, id: "ssh"
      subconfig.vm.provision "shell", path: "https://raw.githubusercontent.com/dpcalfola/kubernetes_vagrant_config/main/init_cfg.sh", args: [ N, k8s_V ]
      subconfig.vm.provision "shell", path: "https://raw.githubusercontent.com/dpcalfola/kubernetes_vagrant_config/main/master.sh", args: [ cni_N ]
    end

#-----Worker Node Subnet1
  (1..N).each do |i|
    config.vm.define "k8s-w#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.box_version = BOX_VERSION
      subconfig.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--groups", "/#{cni_N}-Lab"]
        v.name = "#{cni_N}-k8s-w#{i}"
        v.memory = 1024
        v.cpus = 1
        v.linked_clone = true
      end
      subconfig.vm.hostname = "k8s-w#{i}"
      subconfig.vm.synced_folder "./", "/vagrant", disabled: true
      subconfig.vm.network "private_network", ip: "192.168.10.10#{i}"
      subconfig.vm.network "forwarded_port", guest: 22, host: "5001#{i}", auto_correct: true, id: "ssh"
      subconfig.vm.provision "shell", path: "https://raw.githubusercontent.com/dpcalfola/kubernetes_vagrant_config/main/init_cfg.sh", args: [ N, k8s_V ]
      subconfig.vm.provision "shell", path: "https://raw.githubusercontent.com/dpcalfola/kubernetes_vagrant_config/main/worker.sh"
    end
  end



#-----Client PC Subnet
  config.vm.define "k8s-pc" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.box_version = BOX_VERSION
    subconfig.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--groups", "/Ingress-Lab"]
      v.name = "Ingress-k8s-pc"
      v.memory = 512
      v.cpus = 1
      v.linked_clone = true
    end
    subconfig.vm.hostname = "k8s-pc"
    subconfig.vm.synced_folder "./", "/vagrant", disabled: true
    subconfig.vm.network "private_network", ip: "192.168.10.150"
    subconfig.vm.network "forwarded_port", guest: 22, host: 50150, auto_correct: true, id: "ssh"
    subconfig.vm.provision "shell", path: "https://raw.githubusercontent.com/Beas-github/test/master/T2/client_pc.sh", args: N
  end
end
