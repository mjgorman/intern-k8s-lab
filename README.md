# intern-k8s-lab
Small example repo for intern k8s hands on

# Requirements
## Install Docker
https://docs.docker.com/docker-for-mac/install/

## Install kubectl
```bash
brew install kubectl
```

## Install minikube

```bash
brew install minikube
```
# Preparing for the lab

## Start Minikube in VM Mode
```bash
minikube start --vm=true
```

## Verify kubectl is talking to minikube
```bash
kubectl get nodes
```

# Starting the Lab
## Apply deployment of testapp
```bash
kubectl apply -f 1_deployment.yaml
```

## Verify the deployment is up and running
```bash
kubectl get deployments
```

## Apply the service
```bash
kubectl apply -f 2_service-int.yaml
```

## Verify the service
```bash
kubectl get service
```
You'll notice your testapp service in the list, but you don't have an external port, that's because we only created an internal service, it won't be accesible externally.

## Apply the nodeport external service.
```bash
kubectl apply -f 3_service_np.yaml
```
## Verify the service
```bash
kubectl get service
```

## Connect to the service
```bash
minikube service testapp
```
This gets the VM ip for us and then opens the browser on the port we specified in the nodeport service configurations.

Notice the count returned by the Server Hit value, this is already incrementing from the readiness check hitting the container periodically. 

## scale the deployment up
```bash
kubectl scale --replicas=5 deployment/testapp
```
## Check for the pods to become ready
```bash
kubectl get deployments
```
You may need to run this a few times to finally see 5/5 ready, but once you do go back to your browser and refresh, you should start seeing different hostnames, local addresses and server hit counts as your requests hit different pods in the deployment.  

Congratulations, you've started an app and exposed it to the outside world, and scaled it to meet demands of users.

# Advanced! Moving to an Ingress Controller

## Enable the Ingress controller
Typically this would be higher effort with a real cluster, but we're going to cheat a little and use a minikube addon. 
```bash
minikube addons enable ingress
```
This will do the heavy lifting of seting up the ingress controller ourselves, and we can focus on just the ingress rules and results. 

# Create our first ingess rule
```bash
kubectl apply -f 4_ingress.yaml
```

# Access the app via ingress controller
Enter the IP you used previously to access the application via NodePort into your browser but don't enter the port number, you'll use the default of port 80 that all websites use for http connections. You should get a 404 Not Found from Nginx.


Now place that IPs your pod and add it to your /etc/hosts file with a hostname of testapp.local, leaving out the port designation it should look similar to:
```bash
192.168.64.2 testapp.local
```
Now in your browser enter testapp.local to access You're now viewing you application pods through the ingress controller and the rule you created. 

# Experiment
Now is the time to experiment. Use what you've learned to add new services, use ingress rules to route traffic to one vs the other.

Go find another docker container to run, create it as a deployment. Give it an internal service and ingress rule based on a new hostname. Use another entry in your /etc/hosts file to route that hostname to the VM IP and test accessing both running services via different names.  
