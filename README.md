# ansible-vagrant-gitlab
This small project creates two Linux virtual machines running gitlab server and gitlab runner on them seperately. During VM provisioning with Vagrant, also two different playbooks are running.

VirtualBox, Vagrant and Ansible is needed to be installed on your environment that you run the project

RUN: ansible-playbbok main.yml

In Vagrantfile, I give alias each VM to provison them in one Vagrantfile:
   
    .....
    config.vm.define "gitlabserver" do |gitlabserver|
    .....
    config.vm.define "gitlabrunner" do |gitlabrunner|
    .....
    
and make all the definitions inside those aliases.

Beside VirtualBox Private Network, I add BRIDGE NETWORK interface and default route to make the VMs reachable via SSH
    
    .....
    gitlabrunner.vm.network "public_network", ip: "<ipaddr>", bridge: "<bridge-device-name>"
      gitlabrunner.vm.provision "shell",
         run: "always",
         inline: "ip route add default via <default-router-ip>
    .....
    gitlabserver.vm.network "public_network", ip: "<ipaddr>", bridge: "<bridge-device-name>"
      gitlabserver.vm.provision "shell",
         run: "always",
         inline: "ip route add default via <default-router-ip>"
    .....

With the playbook named os-level-works.yml which is running with Vagrant, I create local user and give sudo rights, and install some packages

With the playbook named install_gitlab_ce.yml also is running with Vagrant, downloads and installs pre-req packages, and installs the gitlabserver-ce as told at https://about.gitlab.com/install/?version=ce#centos-7

With the playbook named install_gitlab_runner.yml also is running with Vagrant, downloads and installs pre-req packages, and installs gitlab-runner and registers it to the gitlab server as told at https://docs.gitlab.com/runner/install/linux-manually.html

With the main.yml beside creating the VMs,
    
also adding provision trigger to existing project. 
    
    - name: "add trigger to project"
        shell: 'curl --request POST --header "PRIVATE-TOKEN: <private-token-of-gitlab-admin-user>" --form description="<description>" "http://<gitlabserver-url>/api/v4/projects/<project-id>/triggers" -o /tmp/output'

Above job creates new trigger to existing project and writes the json output to a file. Because of the token value is needed for webhook definition, json parsing and assigning the token value to a variable is done with the following job
    
    - name: Get Token
        set_fact:
          json: "{{ lookup('file','/tmp/output') | from_json }}"

      - name: Assign Token Value to Variable
        set_fact:
          hookToken: "{{ json.token }}"

And lastly webhook definition for the same existing project. With this config; whenever a push is made to the project, gitlab-runner will be triggered.
    
    - name: "add webhook to project"
        shell: 'curl --request POST --header "PRIVATE-TOKEN: <private-token-of-gitlab-admin-user>"  --form description="<description>" "http://<gitlabserver-url>/api/v4/projects/<project-id>/hooks?enable_ssl_verification=false&push_events=true&id=1&url=http://<gitlabserver-url>/api/v4/projects/<project-id>/ref/<branch-or-tag>>/trigger/pipeline?token={{ hookToken }}"'
  
  
# vagrant-ansible-create-kubernetes-cluster
