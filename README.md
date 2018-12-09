### Jenkins deployment with Rancher 2 ###


### Introduction ###

Jenkins is currently most popular Continous Integration server, and it is distributed as open source software under MIT X11 lincense. Written in Java, it runs on most major Operating Systems, but most interesting for us in this article is Linux and Kubernetes container orchestration platform. We are going to deploy a Kubernetes cluster with Rancher 2.0 and on top of that we are going to install Jenkins docker image. 

### Setting up the cluster ###
If you don't have a Rancher 2.0-managed cluster already, here is instruction how you can make one with Terraform. Obviousy first we are going to install terraform. Download it from hashicorp site https://www.terraform.io/downloads.html

Then unpack it, move it to your $PATH and add executable bit

```unzip terraform_*```

```sudo mv terraform /usr/local/bin/```

```chmod +x /usr/local/bin/terraform```

Now we can clone repo with terrafrom infrastructure plans

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
Most important part are your access keys, number of instances and size. After that we can initialize terrafrom and run apply


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

This is your Rancher user interface URL. There you will need to enter admin password with is "admin" if you did not change it in tfvars file. 

### Deploy Jenkins ###

![](Deploy.gif)

Next we need to make a deployment for the Jenkins. We use docker image jenkins/jenkins:lts and we open the needed ports. As we stared with one node cluster, NodePort will be combination of node ip and randomized port we will see in the UI, like in image bellow. 

![](docker-image.png)


When we direct the browser to the mentioned socket (the ip of your node which you can get from AWS console of by typing kubectl describe nodes, plust the port), we will be greeted with jenkins interface asking for password. To get the randomized password, we will need to go in Rancher UI to Cluster >> quickstart (this is name of the cluster) and then launch kubectl in top right corner. There we need to see name of the pod




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

You will be prompted to select plugins to install. We go with default plugins for now.


![](2-select-plugins.png)

We see here install progress
![](3-install-process.png)

After install is done we need to crate at least one admin user:
![](4-create-admin.png)

And we will have jenkins operational

![](5-jenkins-operational.png)

### Conclusion ###

Installation of Jenkins on top of Rancher 2.0 cluster is fairly straightforward, from here you can start by creating your integration and deployment projects and pipelines. Jenkins uses Groovy programing language for writting pipelines and it can help you automate all of the build steps for your software projects, using Pipeline As Code methodology. 
