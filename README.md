# Managing Kubernetes at Scale

[Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)  
[1. Multi-cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)  
[2. Multi-cluster lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)  
[3. Multitenancy lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)  
[4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)  
---


## Introduction

During this workshop, you will learn through the use of customer resource definitions, controllers and APIs how to manage and operate Kubernetes at scale.

## Prerequisites

You need either a Linux, MacOS or a Windows client with the kubernetes command line installed and an internet browser.

**Install and Set Up kubectl**
The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs.

[kubectl installation](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

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

[Kommander Dashboard https://kommander.k8s-workshops.comops/portal/kommander/ui/#/](https://kommander.k8s-workshops.com/ops/portal/kommander/ui/#/)

Login with your provided credentials:

student number: student###  
password: password  

**Client tokens**

To configure kubectl to access the Kubernetes cluster, obtain a token from the web landing page, by selecting Generate Kubectl Token. Select an identity provider and cluster. As many of the backends provide single-sign on (SSO), you may already be signed in. Otherwise, you will be redirected to your identity provider’s web page to login. Once you have signed in, a page will show the commands required to configure kubectl to access the Konvoy cluster.

![Kommander Dashboard - Generate tokens!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/tokens.png)



When the token expires, it is necessary to repeat the above process to obtain a fresh token. When refreshing a token, only the kubectl config set-credentials command needs to be executed with the new token.






**Configuring environment variables**

These environment variables will be used in the labs today.

```bash
export STUDENT=student001
export WORKSPACENS=$(kubectl get ws $student | awk {'print $3'} | grep student | sed 's/[",]//g')
```

**Using kubectl**

One of the most common ways to perform administrative tasks and interact with a Kubernetes cluster is through the kubectl command line interface. With kubectl, you can run commands against native Kubernetes clusters to retrieve information about key cluster activities and to control specific cluster-level components and operations.

Validate you are able to connect to the management cluster.
```bash
kubectl config set-context --current --namespace=WORKSPACENS
```


```bash
kubectl get pods
```


---  
[Continue to the Multi-cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)  
