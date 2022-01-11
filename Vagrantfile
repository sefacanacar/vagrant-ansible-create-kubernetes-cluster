Vagrant.configure("2") do |config|
   config.vm.define "kubeserver01" do |kubeserver01|
      kubeserver01.vm.box = "ubuntu/focal64"
      kubeserver01.vm.hostname = "kubeserver01"
      kubeserver01.vm.network "public_network", ip: "ip", bridge: "bridge-device"
      kubeserver01.vm.provision "shell",
         run: "always",
         inline: "ip route add default via <gw>"
      kubeserver01.vm.provision "ansible", playbook: "<path>/osconfigs/os-based-preqs.yml"
      kubeserver01.vm.provision "ansible", playbook: "<path>/dockerops/getandbuildimage.yml"
      kubeserver01.vm.provision "ansible", playbook: "<path>/kubeconfigs/kubernetes_master_conf.yml"
      kubeserver01.vm.provider "virtualbox" do |vb|
         kubeserver01.vm.synced_folder '.', '/vagrant', disabled: true
         vb.memory = "4096"
      end
   end
end
