# Managing Kubernetes at Scale

* [Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)
* [Prerequisites](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#Prerequisites)
* [1. Multi-cloud](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)
* [2. Multi-cluster](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)
* [3. Multitenancy](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)
* [4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)

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

[Kommander Dashboard https://kommander.k8s-workshops.com](https://kommander.k8s-workshops.com)