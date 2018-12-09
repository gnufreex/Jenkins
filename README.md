### Jenkins deployment with Rancher 2 ###


### Introduction ###

Jenkins is currently most popular Continous Integration server, and it is distributed as open source software under MIT X11 lincense. Written in Java, it runs on most major Operating Systems, but most interesting for us in this article is Linux and Kubernetes container orchestration platform. We are going to deploy a Kubernetes cluster with Rancher 2.0 and on top of that we are going to install Jenkins docker image. 

### Setting up the cluster ###
Making Jenkins work with rancher 2.0 is fairly staightforward process. We need to clone quickstart repository 

```git clone https://github.com/rancher/quickstart```

change dir into it 

```
cd quickstart/aws
```

We need to use example tfwars as a template
```mv terraform.tfvars.example terraform.tfvars```


change the variables accordingly 
```
nano terraform.tfvars
```


```
tarraform init

terraform apply
```
The terraform apply command will give output similar to this 
```
Outputs:

rancher-url = [
    https://54.188.5.4
]
```

There you will need to enter admin password with is "admin" if you did not change it in tfvars file. 

![](Deploy.gif)

Next we need to make a deployment for the Jenkins. We use docker image jenkins/jenkins:lts and we open the needed ports. As we stared with one node cluster, NodePort will be combination of node ip and randomized port we will see in the UI, like in image bellow. 

![](docker-image.png)


When we direct the browser to the mentioned socket, we will be greeted with jenkins interface asking for password. To get the randomized password, we will need to go in Rancher UI to Cluster >> quickstart (this is name of the cluster) and then launch kubectl in top right corner. There we need to see name of the pod




```
 kubectl get pod
 NAME                       READY     STATUS    RESTARTS   AGE
 jenkins-7d97547648-drps7   1/1       Running   0          49m 

```
With a pod name in mind, run exec command to get password

```
kubectl exec jenkins-7d97547648-drps7 cat /var/jenkins_home/secrets/initialAdminPassword
9128cffec397498a81ec998a26513c57
```



This is the initial password you can enter in the jenkins prompt to start installation. 

![](1-initial-pass.png)




![](2-select-plugins.png)
![](3-install-process.png)
![](4-create-admin.png)
![](5-jenkins-operational.png)
