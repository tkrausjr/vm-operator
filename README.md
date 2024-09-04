# vm-operator
We will be deploying the Spring Petclinic application to demonstrate how the VM Operator Service or VM Service works.

## Create a vSphere Namespace for your Application
1. Use the vSphere UI to create a Namespace called "petclinic-app"

## Deploy Centos Mysql VM and Virtual Machine Service 
The VM Service leverage the same Kubernetes API to deploy and manage virtual machines that you use to deploy Kubernetes clusters, pods and services. This allows developers to manage virtual machines for application components that are not containerized. 

1. git clone https://github.com/tkrausjr/vm-operator.git
2. cd vm-operator
3. kubectl vsphere login --server 192.168.201.112 --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify --tanzu-kubernetes-cluster-namespace petclinic-app
4. kubectl config use-context petclinic-app
4. kubectl apply -f ./mysql-vmservice/mysql-vmsvc-vm-nsx.yaml
5. kubectl get vm
6. kubectl get vm mysql-centosvm -o yaml | grep conditions: -A 12
7. kubectl get svc mysql-vmservice

## Deploy the Spring Petclinic Kubernetes objects
Now that we have a mysql database running as a Virtual Machine and managed by the
vmoperator in Tanzu and a Kubernetes Service managed by ALB exposing the mysql port we
are ready to spin up the Petclinic microservices on our Kubernetes cluster (cluster-01) which
is also running in the  petclinic-app vSphere Namespace.

1. kubectl vsphere login --server 192.168.201.112 --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify --tanzu-kubernetes-cluster-namespace petclinic-app --tanzu-kubernetes-cluster-name cluster-01
2. ls -ahl ./petclinic-k8s/services
3. kubectl apply -f ./petclinic-k8s/01-namespace.yaml
4. kubectl get namespace
5. Create the required Kubernetes objects for the Petclinic application including Secrets, Configmaps, roles, and Kubernetes services for each of the microservice components: 
    a. kubectl apply -f ./petclinic-k8s/services/
6. Now deploy the actual Kubernetes Deployments and Pods for the Petclinic Application.
    a. kubectl apply -f ./petclinic-k8s/deployments/
    b. kubectl get deploy,po -n spring-petclinic
7. Obtain the EXTERNAL-IP of the Spring Petclinic Frontend Gateway and copy this IP address: 
    a. kubectl get svc -n spring-petclinic
8. In a Web Browser, navigate to the EXTERNAL-IP from the previous command:
    a. http://<EXTERNAL-IP>
    b. IE. http://192.168.130.15
    c. You should see the "Welcome to Petclinic" Web Page