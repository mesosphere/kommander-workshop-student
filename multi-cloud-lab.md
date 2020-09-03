# Managing Kubernetes at Scale

[Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)  
[1. Multi-Cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-Cloud-lab)  
[2. Multi-Cluster lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)  
[3. Multi-Tenancy lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)  
[4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)  
---

## Multi-Cloud-lab

Infrastructure providers like AWS, Azure, and Google provide the infrastructure for your Konvoy clusters. To automate their provisioning, Kommander needs authentication keys for your preferred infrastructure provider. You may have many accounts for a single infrastructure provider.

In order, to provision new clusters and manage them, Kommander needs infrastructure provider credentials.  Infrastructure Provider credentials are configured in each workspace, so you must configure the objects in the namespace of the workspace.


To configure an infrastructure provider we will have to complete two steps:
 - Create an secret in the workspace namespace.
 - Create a cloudprovideraccount in the workspace namespace that references the secret created in step one.


1. **Create Secret**

You will see the data in this object is base64 encoded.  For this lab we are using AWS user roles.  The Role authentication method can only be used if your management cluster is running in AWS.


aws-secret.yaml:
> ```yaml
> kind: Secret
> apiVersion: v1
> metadata:
>   name: aws-credentials
>   namespace: student###-#####-#####
> data:
>   config: >-
>     W2RlZmF1bHRdCnJlZ2lvbiA9IHVzLWVhc3QtMQpvdXRwdXQgPSBqc29uCgpbcHJvZmlsZSAxMTA0NjU2NTc3NDFfTWVzb3NwaGVyZS1Qb3dlclVzZXJdCnJvbGVfYXJuID0gYXJuOmF3czppYW06OjM5ODA1MzQ1MTc4Mjpyb2xlL2tvbW1hbmRlci1kZXBsb3llcgpjcmVkZW50aWFsX3NvdXJjZSA9IEVjMkluc3RhbmNlTWV0YWRhdGEK
>   profile: Mzk4MDUzNDUxNzgyX01lc29zcGhlcmUtUG93ZXJVc2VyCg==
>   type: YXdz
> type: kommander.mesosphere.io/aws-credentials
> ```

We will apply the secret which will be stored in the namespace of the workspace.

```bash
kubectl apply -f aws-secret.yaml
```

2. **Create a CloudProviderAccount**

The CloudProviderAccount object contains the 

cloudprovider.yaml:
> ```yaml
> apiVersion: kommander.mesosphere.io/v1beta1
> kind: CloudProviderAccount
> metadata:
>   name: aws-credentials
>   namespace: STUDENT000-00000-00000
>   annotations:
>     kommander.mesosphere.io/display-name: STUDENT000 Cloud Provider Account
> spec:
>   provider: aws
>   credentialsRef:
>     name: aws-credentials
> ```

We will apply the CloudProviderAccount which will be stored in the namespace of the workspace.

```bash
kubectl apply -f cloudprovider.yaml
```

**Validate and Modify Infrastructure Providers***

Infrastructure Provider credentials are configured in each workspace, so you must first select a workspace, students only have access to their own workspaces. Then, navigate to the infrastructure Providers option under Administration.

![Infrastructure Provider](https://docs.d2iq.com/ksphere/kommander/1.1/img/empty-infrastructure-providers.png)

> Before deleting an infrastructure provider, Kommander verifies if any existing managed clusters were created using this provider. The infrastructure provider cannot be deleted until all clusters, created with the infrastructure provider, have been deleted. This ensures Kommander has access to your infrastructure provider to remove all resources created for a managed cluster.  

--- 
[<- Return to the Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction) -- 
[Continue to the Multi-Cluster lab ->](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)
