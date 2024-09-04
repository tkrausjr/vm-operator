# vm-operator
We will be deploying the Spring Petclinic application to demonstrate how the VM Operator Service or VM Service works. To do this we will deploy a Virtual Machine object for the mysql component of the application in TKG in the same vSphere Namespace where we have a Kubernetes Cluster. We will then deploy the Front End components as Kubernetes deployments and they will connect back to the mysql Database to store and retrieve data.

## Create a vSphere Namespace for your Application
* Use the vSphere UI to create a Namespace called "petclinic-app"

## Deploy Centos Mysql VM and Virtual Machine Service 
The VM Service leverage the same Kubernetes API to deploy and manage virtual machines that you use to deploy Kubernetes clusters, pods and services. This allows developers to manage virtual machines for application components that are not containerized. 

* git clone https://github.com/tkrausjr/vm-operator.git
* cd vm-operator
* kubectl vsphere login --server 192.168.201.112 --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify --tanzu-kubernetes-cluster-namespace petclinic-app
* kubectl config use-context petclinic-app
* kubectl apply -f ./mysql-vmservice/mysql-vmsvc-vm-nsx.yaml
* kubectl get vm
* kubectl get vm mysql-centosvm -o yaml | grep conditions: -A 12
* kubectl get svc mysql-vmservice

## Deploy the Spring Petclinic Kubernetes objects
Now that we have a mysql database running as a Virtual Machine and managed by the
vmoperator in Tanzu and a Kubernetes Service managed by ALB exposing the mysql port we
are ready to spin up the Petclinic microservices on our Kubernetes cluster (cluster-01) which
is also running in the  petclinic-app vSphere Namespace.

* kubectl vsphere login --server 192.168.201.112 --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify --tanzu-kubernetes-cluster-namespace petclinic-app --tanzu-kubernetes-cluster-name cluster-01
* ls -ahl ./petclinic-k8s/services
* kubectl apply -f ./petclinic-k8s/01-namespace.yaml
* kubectl get namespace
* Create the required Kubernetes objects for the Petclinic application including Secrets, Configmaps, roles, and Kubernetes services for each of the microservice components: 
    * kubectl apply -f ./petclinic-k8s/services/
* Now deploy the actual Kubernetes Deployments and Pods for the Petclinic Application.
    * kubectl apply -f ./petclinic-k8s/deployments/
    * kubectl get deploy,po -n spring-petclinic
* Obtain the EXTERNAL-IP of the Spring Petclinic Frontend Gateway and copy this IP address: 
    * kubectl get svc -n spring-petclinic
* In a Web Browser, navigate to the EXTERNAL-IP from the previous command:
    * http://\<EXTERNAL-IP\>
    * IE. http://192.168.130.15
    * You should see the "Welcome to Petclinic" Web Page
