# vagrant-ansible-create-kubernetes-cluster

This project creates single node k8s cluster and deploys single pod mysql and custom [python app](https://github.com/sefacanacar/basic-python-app-for-docker) on VirtualBox environment using Vagrant and Ansible 

** It needs to be edited some parts of the source code like ip addresses, usernames etc **

After editing the sourcecode you can create the cluster via running
```
vagrant up
```

After creating the cluster and successful deployments you may reach the application via browser with the ip of your k8s host 

```
http://<ip_address_of_host:32000>
```
