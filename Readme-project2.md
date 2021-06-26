Hi

In this project I have been tasked to install kubernetes cluster using vagrant and configuration management tool ansible. After that I intructed to set mentric server and kubernetes dashboard. Let's begin.


***Installing Vagrant***

Vagrant is insfrastructure provisioing tool which is handy to setup any light weight lab environment easily. For my lab I installed Vagrant on a Ubuntu 18.04 server. For installing vagrant follow the below instruction:

curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vagrant

As a vagrant provider I have used Oracle virtual box. So I installed also it in my base system.

 
***Creating a Vagrantfile***

Now create a directory named DevOps and inside it create another directory named Project-2 and created a Vagrantfile there. Now add the below line of code there. 

<Check the vagrant file in the project-2/kubernetes-setup>

***Now Create an Ansible playbook for Kubernetes master***

Create a directory named kubernetes-setup in the same directory as the Vagrantfile. Create two files named master-playbook.yml and node-playbook.yml in the directory kubernetes-setup.

In the file master-playbook.yml, add the code below.

<Check the master-plybook.yml file in the folder>


***Create the Ansible playbook for Kubernetes node***

Create a file named node-playbook.yml in the directory kubernetes-setup.

Add the code below into node-playbook.yml

<Check the node-playbook.yml file in the project-2//kubernetes-setup>



***Upon completing the Vagrantfile and playbooks follow the below steps***

Go to the directory Devops/Project-2/ and then run "vagrant up"

Your 1 master 2 worker node kubernetes cluster will be installed. Now we will install kubernetes dashboard and metric server. 

***Dashboard Installaion***
ssh into your masternode by runnig this command vagrant ssh k8s-master and then run the below snippet.

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml

After that run "kubectl proxy" command from the node you want to view the dashboash
You can see the Dashboard by this link: 
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/



***Metric server installation***

For this ssh into the master node and run this command. "git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git"
This will download all the files needed for metric server. 
After that go to the directory named Kubernetes-metrics-server and run "kubectl apply -f ."

This install all the necessary kubernetes objects for metric server. Now create some pods and wait for sometime to load the metric server. 
After a while run "kubectl top pods" to view the resource consumption of your pod and "kubectl top nodes" for node resource consumption. 


Thanks!
