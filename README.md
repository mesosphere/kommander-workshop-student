# Managing Kubernetes at Scale

[Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)  
[1. Multi-Cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)  
[2. Multi-Cluster lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)  
[3. Multi-Tenancy lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)  
[4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)  
---


## Introduction

During this workshop, you will learn through the use of customer resource definitions, controllers and APIs how to manage and operate Kubernetes at scale.

## Prerequisites

Please go to the Google Drive links below and put your name in the Student Sign Up Sheet sheet next to a student number.  This will be your student number for your lab today.  

[Google Drive Share - Contains Student Sign Up Sheet](https://docs.google.com/spreadsheets/d/1fw2VjKo67SOcO5nvjAwhTpgH8rKX7xKMQF11V-twK60/edit?usp=sharing)

[Google Drive Share - Contains Student Labs](https://drive.google.com/drive/folders/1Uq6NuWlVomDGX41DjV8Wcda0JRZikQSF?usp=sharing)

[Static Website - Contains Student Labs](http://students.k8s-workshops.com/)


You need either a Linux, MacOS or a Windows client with the kubernetes command line installed and an internet browser.

**Install and Set Up kubectl**
The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs.

[kubectl Installation](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

**Install kubectl on Linux**

Install kubectl binary with curl on Linux
Download the latest release and move the binary in to your PATH with the following commands:

```bash
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"```
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

Test to ensure the version you installed is up-to-date:

```bash
kubectl version --client
```

**Connect to the Kommander dashboard**

[Kommander Dashboard https://kommander.k8s-workshops.com/ops/portal/kommander/ui/#/](https://kommander.k8s-workshops.com/ops/portal/kommander/ui/#/)

Login with your provided credentials:

student number: student###  
password: password  

**Client tokens**

To configure kubectl to access the Kubernetes cluster, obtain a token from the web landing page, by selecting Generate Kubectl Token. Select an identity provider and cluster. As many of the backends provide single-sign on (SSO), you may already be signed in. Otherwise, you will be redirected to your identity provider’s web page to login. 

![Kommander Dashboard - Generate tokens!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/generate.png)

Once you have signed in, a page will show the commands required to configure kubectl to access the Konvoy cluster.

![Kommander Dashboard - tokens!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/token.png)



When the token expires, it is necessary to repeat the above process to obtain a fresh token. When refreshing a token, only the kubectl config set-credentials command needs to be executed with the new token.

### Reconnect to the Kommander Dashboard
Kommander Dashboard https://kommander.k8s-workshops.comops/portal/kommander/ui/#/



**Using kubectl**

One of the most common ways to perform administrative tasks and interact with a Kubernetes cluster is through the kubectl command line interface. With kubectl, you can run commands against native Kubernetes clusters to retrieve information about key cluster activities and to control specific cluster-level components and operations.

Validate you are able to connect to the management cluster.  

Windows  
```bash
setupENV.bat
```  
Mac/Linux  
```bash
chmod +x setupENV.sh
./setupENV.sh
``` 
Verify your API connection:  
```bash
kubectl get pods
```
You should recieve an output that says:  
> ```bash
> No resources found in student001-g4vf9 namespace.
> ```

---  
[Continue to the Multi-Cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)  
