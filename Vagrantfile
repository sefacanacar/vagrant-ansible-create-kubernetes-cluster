Vagrant.configure("2") do |config|
   config.vm.define "kubeserver01" do |kubeserver01|
      kubeserver01.vm.box = "ubuntu/focal64"
      kubeserver01.vm.hostname = "kubeserver01"
      kubeserver01.vm.network "public_network", ip: "192.168.1.40", bridge: "en0: Wi-Fi (Wireless)"
      kubeserver01.vm.provision "shell",
         run: "always",
         inline: "ip route add default via 192.168.1.1"
      kubeserver01.vm.provision "ansible", playbook: "/Users/sefa.acar/ansible-vagrant-gitlab/osconfigs/os-based-preqs.yml"
      kubeserver01.vm.provision "ansible", playbook: "/Users/sefa.acar/ansible-vagrant-gitlab/dockerops/getandbuildimage.yml"
      kubeserver01.vm.provision "ansible", playbook: "/Users/sefa.acar/ansible-vagrant-gitlab/kubeconfigs/kubernetes_master_conf.yml"
      kubeserver01.vm.provider "virtualbox" do |vb|
         kubeserver01.vm.synced_folder '.', '/vagrant', disabled: true
         vb.memory = "4096"
      end
   end
end
