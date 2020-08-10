# Managing Kubernetes at Scale

[Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)  
[Prerequisites](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#Prerequisites)  
[1. Multi-cloud](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)  
[2. Multi-cluster](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)  
[3. Multitenancy](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)  
[4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)  
---

Multitenancy-Lab

![Kommander architecture!](https://docs.d2iq.com/ksphere/kommander/1.1/img/Kommander_architecture.png "Kommander_architecture")  

**Workspaces**

Workspaces give you the flexibility to represent your organization in a way that makes sense for your team and configuration.

For example, you can create separate workspaces for each department, product, or business function. Each workspace manages their own clusters and role-based permissions, while retaining an overall organization-level view of all clusters in operation.

At the top level, Global, IT administrators manage all clusters across all workspaces.

At the Workspace level, devops administrators manage multiple clusters within a workspace.

At the Projects level, devops administrators or developers manage configuration and services across multiple clusters.


**Projects**

Projects empower teams to deploy their configurations and services to clusters in a consistent way. Projects allow central IT or a business unit to share their Kubernetes clusters among several teams. Using Projects, Kommander is leveraging Kubernetes Cluster Federation (aka KubeFed) to coordinate the configuration of multiple Kubernetes clusters. When a Project is created, Kommander creates a federated namespace that is propagated to the Kubernetes clusters associated with this Project. Kommander allows a user to manually or dynamically (using labels) select the Kubernetes clusters associated with a Project. Projects support the management of configmaps, secrets, services, quotas, and role-based access control by leveraging federated resources.

**Creating a Project**

When you create a Project, you need to specify a Project Name, a Namespace Name (optional) and a way to allow Kommander to determine which Kubernetes clusters will be part of this project.

As mentioned above, a Project Namespace corresponds to a Kubernetes Federated Namespaces.

To determine which Kubernetes clusters will be part of this project, you can either select manually existing clusters or define labels that Kommander will use to dynamically add clusters. The latter is recommended because it will allow you to deploy additional Kubernetes clusters later and to have them automatically associated with Projects based on their labels.  This is what we will use.

project.yaml  
>```yaml
> apiVersion: workspaces.kommander.mesosphere.io/v1alpha1
> kind: Project
> metadata:
>   name: project-STUDENT000
>   namespace: STUDENT000-00000-00000
> spec:
>   workspaceRef:
>     name: STUDENT000
> placement:
>     namespaceName: project-STUDENT000
>     clusterSelector:
>       matchLabels:
>         cluster: prod
> ```

Create the project via kubectl.

```bash
kubectl apply -f project.yaml
```

**Project ConfigMaps**

Project ConfigMaps can be created to make sure Kubernetes ConfigMaps are automatically created on all Kubernetes clusters associated with the Project, in the corresponding namespace.

A Project ConfigMap is simply a Kubernetes FederatedConfigMap and can also be created using kubectl:

> ```yaml
> apiVersion: types.kubefed.io/v1beta1
> kind: FederatedConfigMap
> metadata:
>   annotations:
>     kommander.mesosphere.io/display-name: cm1
>   generateName: cm1-
>   namespace: ${projectns}
> spec:
>   placement:
>     clusterSelector: {}
>   template:
>     data:
>       key: value
> ```

Apply the federated configmap via kubectl:

```bash
kubectl apply -f configmap.yaml
```

Then, if you run the following command on a Kubernetes cluster associated with the Project, you’ll see a Kubernetes ConfigMap Object, in the corresponding namespace:

```bash
kubectl -n ${projectns} get configmap
```

**Project Secrets**

Project Secrets can be created to make sure a Kubernetes Secrets are automatically created on all the Kubernetes clusters associated with the Project, in the corresponding namespace.

A Project Secret is simply a Kubernetes FederatedConfigSecret and can also be created using kubectl:

> ```yaml
> apiVersion: types.kubefed.io/v1beta1
> kind: FederatedSecret
> metadata:
>   annotations:
>     kommander.mesosphere.io/display-name: secret1
>   generateName: secret1-
>   namespace: ${projectns}
> spec:
>   placement:
>     clusterSelector:
>       matchLabels:
>         cluster: prod
>   template:
>     data:
>       key: dmFsdWU=
> ```

Then, if you run the following command on a Kubernetes cluster associated with the Project, you’ll see a Kubernetes Secret Object, in the corresponding namespace:

```bash
kubectl -n ${projectns} get secret
```

**Project Roles**

Project Roles are used to define permissions at the namespace level. You can create a Project Role with several Rules. 

project-role.yaml

> ```yaml
> apiVersion: workspaces.kommander.mesosphere.io/v1alpha1
> kind: ProjectRole
> metadata:
>   name: projectrole
>   namespace: STUDENT000
> spec:
>   rules:
>     - apiGroups: [""] # "" indicates the core API group
>       resources: ["*"]
>       verbs: ["*"]
> ```

When a Project Role is created, Kommander creates a Kubernetes FederatedRole on the Kubernetes cluster where Kommander is running:

```bash
kubectl -n ${projectns} get federatedroles.types.kubefed.io projectrole -o yaml
```

Then, if you run the following command on a Kubernetes cluster associated with the Project, you’ll see a Kubernetes Role object in the corresponding namespace:

```bash
kubectl -n ${projectns} get role projectrole -o yaml  
```

**Project Policy**

Project Policies grant access to a specified Project Role for a specified group of people.  
Before you can create a Project Policy, you need to make sure you have created a Group. A Kommander Group can contain one or several Identity Provider users and/or groups.

project-policy.yaml  
> ```yaml
> apiVersion: workspaces.kommander.mesosphere.io/v1alpha1
> kind: VirtualGroupProjectRoleBinding
> metadata:
>   generateName: projectpolicy-
>   namespace: ${projectns}
> spec:
>   projectRoleRef:
>     name: ${projectrole}
>   virtualGroupRef:
>     name: ${virtualgroup}
> ```

When a Project Policy is created, Kommander creates a Kubernetes FederatedRoleBinding on the Kubernetes cluster where Kommander is running. Run the following command to create the policy using kubectl:  
```bash
kubectl apply -f project-policy.yaml
```



---  
[Continue to the Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)

