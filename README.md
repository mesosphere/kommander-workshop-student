# Managing Kubernetes at Scale

## Introduction

During this workshop, you will learn through the use of customer resource definitions, controllers and APIs how to manage and operate Kubernetes at scale.

* [Introduction](#introduction)
* [Prerequisites](#prerequisites)
* [1. Multi-cloud](#1-Multi-cloud-lab)
* [2. Multi-cluster](#2-Multi-cluster)
* [3. Multitenancy](#3-Multitenancy)
* [4. Summary](#4-Summary)

## Prerequisites

You need either a Linux, MacOS or a Windows client with the kubernetes command line installed and an internet browser.

**Install and Set Up kubectl**
The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs.

[kubectl installation](https://kubernetes.io/docs/tasks/tools/install-kubectl/)





## 1. Multi-cloud-lab


1.1 Creating an AWS Cloud Provider
In this tutorial we are using AWS Provider, but you can use any other supported infrastructure provider.

NOTE: Assuming you logged in with aws-cli

To create a cluster you need to setup the secret with the AWS credentials and a CloudProviderAccount.


**Create Secret**
student###-aws-secret.yaml:
```yaml
kind: Secret
apiVersion: v1
metadata:
  name: aws-credentials
  namespace: student###-#####-#####
data:
  config: >-
    W2RlZmF1bHRdCnJlZ2lvbiA9IHVzLWVhc3QtMQpvdXRwdXQgPSBqc29uCgpbcHJvZmlsZSAxMTA0NjU2NTc3NDFfTWVzb3NwaGVyZS1Qb3dlclVzZXJdCnJvbGVfYXJuID0gYXJuOmF3czppYW06OjM5ODA1MzQ1MTc4Mjpyb2xlL2tvbW1hbmRlci1kZXBsb3llcgpjcmVkZW50aWFsX3NvdXJjZSA9IEVjMkluc3RhbmNlTWV0YWRhdGEK
  profile: Mzk4MDUzNDUxNzgyX01lc29zcGhlcmUtUG93ZXJVc2VyCg==
  type: YXdz
type: kommander.mesosphere.io/aws-credentials
```
We will apply the secret which will be stored in the namespace of the workspace.

`kubectl apply -f student###-aws-secret.yaml`

**Validate Secret**

Ton validate the secret was created in the correct namespace:

`kubectl get secrets -n student###-#####-#####`

To view the contents of the secret we just created:

`kubectl describe secret aws-credentials -n student###-#####-#####`


1.2 **Create a CloudProviderAccount**
student###-cloudprovideraccount.yaml:
```yaml
apiVersion: kommander.mesosphere.io/v1beta1
kind: CloudProviderAccount
metadata:
  name: student000
  namespace: student###-#####-#####
  annotations:
    kommander.mesosphere.io/display-name: student###-aws-credentials
spec:
  provider: aws
  credentialsRef:
    name: aws-credentials
```
We will apply the CloudProviderAccount which will be stored in the namespace of the workspace.

`kubectl apply -f student###-cloudprovideraccount.yaml`

**Validate CloudProviderAccount***

Ton validate the cloudprovideraccount was created in the correct namespace:

`kubectl get cloudprovideraccount -n student###-#####-#####`

To view the contents of the cloudprovideraccount we just created:

`kubectl describe cloudprovideraccount aws-credentials -n student###-#####-#####`

