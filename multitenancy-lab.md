# Managing Kubernetes at Scale

[Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)  
[1. Multi-Cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-Cloud-lab)  
[2. Multi-Cluster lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)  
[3. Multi-Tenancy lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multi-Tenancy-Lab)  
[4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)  
---

## Multi-Tenancy-Lab

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
>   name: project
>   namespace: student001-g4vf9
> spec:
>   workspaceRef:
>     name: student001
>   namespaceName: project
>   placement:
>     clusterSelector:
>       matchLabels:
>         cluster: prod
> ```

Create the project via kubectl.

```bash
kubectl apply -f project.yaml
```

**Validate project creation**

Validate the project was create via the Kommander dashboard from the projects menu.

![Kommander Dashboard - project!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/project.png)


**Project ConfigMaps**

Project ConfigMaps can be created to make sure Kubernetes ConfigMaps are automatically created on all Kubernetes clusters associated with the Project, in the corresponding namespace.

A Project ConfigMap is simply a Kubernetes FederatedConfigMap and can also be created using kubectl:

> ```yaml
> apiVersion: v1
> items:
> - apiVersion: types.kubefed.io/v1beta1
>   kind: FederatedConfigMap
>   metadata:
>     annotations:
>       kommander.mesosphere.io/description: ""
>       kommander.mesosphere.io/display-name: configmap
>     finalizers:
>     - kubefed.io/sync-controller
>     generation: 1
>     name: configmap
>     namespace: project
>   spec:
>     placement:
>       clusterSelector:
>         matchLabels:
>           cluster: prod
>     template:
>       data:
>         config: map
> kind: List
> metadata:
>   resourceVersion: ""
>   selfLink: ""
> ```

Apply the federated configmap via kubectl:

```bash
kubectl apply -f project-configmap.yaml
```

**Project Secrets**

Project Secrets can be created to make sure a Kubernetes Secrets are automatically created on all the Kubernetes clusters associated with the Project, in the corresponding namespace.

A Project Secret is simply a Kubernetes FederatedConfigSecret and can also be created using kubectl:

> ```yaml
> apiVersion: v1
> items:
> - apiVersion: types.kubefed.io/v1beta1
>   kind: FederatedSecret
>   metadata:
>     annotations:
>       kommander.mesosphere.io/description: ""
>       kommander.mesosphere.io/display-name: secret
>     finalizers:
>     - kubefed.io/sync-controller
>     generation: 1
>     name: secret
>     namespace: project
>   spec:
>     placement:
>       clusterSelector:
>         matchLabels:
>           cluster: prod
>     template:
>       data:
>         admin: cGFzc3dvcmQ=
> kind: List
> metadata:
>   resourceVersion: ""
>   selfLink: ""
> ```

Apply the secret via kubectl:

```bash
kubectl apply -f project-secret.yaml
```

**Project Roles**

Project Roles are used to define permissions at the namespace level. You can create a Project Role with several Rules. 

project-role.yaml

> ```yaml
> apiVersion: workspaces.kommander.mesosphere.io/v1alpha1
> kind: ProjectRole
> metadata:
>   name: projectadminrole
>   namespace: project
> spec:
>   rules:
>     - apiGroups: [""] # "" indicates the core API group
>       resources: ["*"]
>       verbs: ["*"]
> ```

Apply the role via kubectl:

```bash
kubectl apply -f project-role.yaml
```

**Project Policy**

Project Policies grant access to a specified Project Role for a specified group of people.  
Before you can create a Project Policy, you need to make sure you have created a Group. A Kommander Group can contain one or several Identity Provider users and/or groups.

project-policy.yaml  
> ```yaml
> apiVersion: workspaces.kommander.mesosphere.io/v1alpha1
> kind: VirtualGroupProjectRoleBinding
> metadata:
>   name: projectpolicy
>   namespace: project
> spec:
>   projectRoleRef:
>     name: projectadminrole
>   virtualGroupRef:
>     name: student001
> ```

When a Project Policy is created, Kommander creates a Kubernetes FederatedRoleBinding on the Kubernetes cluster where Kommander is running. Run the following command to create the policy using kubectl: 
 
```bash
kubectl apply -f project-policy.yaml
```

**Project Platform Services**

Project Platform Services are services that you want to be deployed on all the Kubernetes clusters associated with the Project, in the corresponding namespace.  Kommander can be extended with the AddonRepository resource that point to git repositories containing application addons. For our example, we are using the default example repository that ships with Kommander.  

project-platform-services.yaml
> ```yaml
> apiVersion: types.kubefed.io/v1beta1
> kind: FederatedAddon
> metadata:
>   name: jenkins
>   namespace: ${projectns}
> spec:
>   placement:
>     clusterSelector: {}
>   template:
>     apiVersion: kubeaddons.mesosphere.io/v1beta1
>     kind: Addon
>     metadata:
>       annotations:
>         appversion.kubeaddons.mesosphere.io/jenkins: 1.9.4
>         catalog.kubeaddons.mesosphere.io/addon-revision: 1.9.4-1
>         catalog.kubeaddons.mesosphere.io/origin-repository: https://github.com/mesosphere/kubeaddons-enterprise
>         catalog.kubeaddons.mesosphere.io/origin-repository-version: master
>         values.chart.helm.kubeaddons.mesosphere.io/jenkins: https://raw.githubusercontent.com/helm/charts/master/stable/jenkins/values.yaml
>       labels:
>         kubeaddons.mesosphere.io/name: jenkins
>       name: jenkins
>     spec:
>       chartReference:
>         chart: stable/jenkins
>         values: |
>           ---
>           master:
>             useSecurity: false
>             installPlugins:
>               - prometheus:2.0.6
>               - kubernetes:1.18.2
>               - workflow-job:2.33
>               - workflow-aggregator:2.6
>               - credentials-binding:1.19
>               - git:3.11.0
>             csrf:
>               defaultCrumbIssuer:
>                 enabled: false
>                 proxyCompatability: false
>             serviceType: "ClusterIP"
>             jenkinsUriPrefix: "/jenkins"
>             ingress:
>               enabled: true
>               path: /jenkins
>               annotations:
>                 kubernetes.io/ingress.class: traefik
>         version: 1.9.4 
> ```

Since a project Platform Service is simply a Kubernetes FederatedAddon, it can also be created using kubectl.

```bash
kubectl create -f project-platform-services.yaml
```

**Validate the Project Configuration**

To validate the project configuration, from the projects menu, click the project in the list.  This will take you into the project summary which will contain the platform services, clusters, roles, policies, configmaps, secrets, and quotas.

![Kommander Dashboard - Projects!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/project-complete.png)
 
 
**Validate Jenkins Deployment**
```
echo https://$(kubectl --kubeconfig <studentXXX-kubeconfig.yaml> get svc traefik-kubeaddons -n kubeaddons -ojsonpath={.status.loadBalancer.ingress[*].hostname})/jenkins
``` 
 
---  
[<- Reuturn to the Multi-cluster lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab) -- 
[Continue to the Summary ->](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)

