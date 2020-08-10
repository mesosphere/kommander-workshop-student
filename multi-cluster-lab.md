# Managing Kubernetes at Scale

[Introduction](https://github.com/mesosphere/kommander-workshop-student/blob/master/README.md#introduction)  
[1. Multi-cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab)  
[2. Multi-cluster lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cluster-lab.md#Multi-Cluster-Lab)  
[3. Multitenancy lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)  
[4. Summary](https://github.com/mesosphere/kommander-workshop-student/blob/master/summary.md#Summary)  
---

## Multi-Cluster-

Kommander provides management and insights for multiple Kubernetes clusters, and supports both Konvoy or non-Konvoy clusters. Kommander includes essential enterprise-grade capabilities such as:

- Operational dashboard
- Centralized monitoring, logging and alerting
- Cluster configuration manager
- Services catalog
- Integration with KUDO operations
- Governance and policy administration
- Centralized authorization

### Creating or Connecting Clusters

Kommander allows you to monitor and manage very large numbers of clusters and so we make it easy to either connect existing clusters or create new clusters whose lifecycle is managed by Konvoy.

#### Types

There are several types of clusters to be aware of in the Clusters tab.

- Attached: A cluster that was not created with Kommander. Attached clusters’ lifecycle cannot be managed.
- Managed: A Konvoy cluster that was created with Kommander. Managed clusters’ lifecycle can be managed.
- Management: The Konvoy cluster that hosts Kommander.

#### Creating a cluster in the Workspace

Creating a cluster

To create a cluster using KCL means you create a KonvoyCluster CRD in the Workspace namespace. You need to reference the CloudProviderAccount with the AWS credentials you created in the previous step.

cluster.yaml
> ```yaml
> apiVersion: kommander.mesosphere.io/v1beta1
> kind: KonvoyCluster
> metadata:
>   name: STUDENT000
>   labels:
>     cluster: prod
> spec:
>   cluster:
>     addons:
>     - addonsList:
>       - enabled: true
>         name: kibana
>       - enabled: true
>         name: reloader
>       - enabled: true
>         name: velero
>       - enabled: true
>         name: dashboard
>       - enabled: true
>         name: dex-k8s-authenticator
>       - enabled: true
>         name: elasticsearch
>       - enabled: true
>         name: traefik
>       - enabled: true
>        name: defaultstorageclass-protection
>       - enabled: true
>         name: fluentbit
>       - enabled: true
>         name: opsportal
>       - enabled: true
>         name: elasticsearchexporter
>       - enabled: false
>         name: gatekeeper
>       - enabled: false
>         name: localvolumeprovisioner
>         values: |
>           # Multiple storage classes can be defined here. This allows to, e.g.,
>           # distinguish between different disk types.
>           # For each entry a storage class '$name' and
>           # a host folder '/mnt/$dirName' will be created. Volumes mounted to this
>           # folder are made available in the storage class.
>           storageclasses:
>             - name: localvolumeprovisioner
>               dirName: disks
>               isDefault: false
>               reclaimPolicy: Delete
>               volumeBindingMode: WaitForFirstConsumer
>       - enabled: true
>         name: prometheus
>       - enabled: true
>         name: external-dns
>         values: |
>           aws:
>             region:
>           domainFilters: []
>       - enabled: false
>         name: flagger
>       - enabled: false
>         name: istio
>       - enabled: false
>         name: kudo
>     - enabled: true
>         name: prometheusadapter
>       - enabled: true
>         name: traefik-forward-auth
>       - enabled: true
>         name: cert-manager
>       - enabled: false
>         name: awsebsprovisioner
>         values: |
>           storageclass:
>             isDefault: false
>       - enabled: true
>         name: dex
>       - enabled: true
>         name: konvoyconfig
>       - enabled: false
>         name: nvidia
>       - enabled: true
>         name: awsebscsiprovisioner
>       - enabled: true
>         name: kube-oidc-proxy
>       configRepository: https://github.com/mesosphere/kubernetes-base-addons
>       configVersion: stable-1.17-2.0.2
>     - configRepository: https://github.com/mesosphere/kubeaddons-dispatch
>       configVersion: stable-1.17-1.2.2
>       addonsList:
>       - name: dispatch
>         enabled: false
>     - configRepository: https://github.com/mesosphere/kubeaddons-kommander
>       configVersion: stable-1.17-1.1.0
>       addonsList:
>       - name: kommander
>         enabled: false
>     containerNetworking:
>       calico:
>         encapsulation: ipip
>         version: v3.13.4
>     containerRuntime:
>       containerd:
>         version: 1.3.4
>     kubernetes:
>       admissionPlugins:
>         enabled:
>         - NodeRestriction
>         - AlwaysPullImages
>       cloudProvider:
>         provider: aws
>       networking:
>         podSubnet: 192.168.0.0/16
>         serviceSubnet: 10.0.0.0/18
>       version: 1.17.8
>     nodePools:
>     - name: worker
>       count: 4
>       machine:
>         imageID: ami-0affd4508a5d2481b
>         rootVolumeSize: 80
>         rootVolumeType: gp2
>         imagefsVolumeEnabled: true
>         imagefsVolumeSize: 160
>         imagefsVolumeType: gp2
>         imagefsVolumeDevice: xvdb
>         type: m5.2xlarge
>     - name: control-plane
>       controlPlane: true
>       count: 3
>       machine:
>         imageID: ami-0affd4508a5d2481b
>         rootVolumeSize: 80
>         rootVolumeType: io1
>         rootVolumeIOPS: 1000
>         imagefsVolumeEnabled: true
>         imagefsVolumeSize: 160
>         imagefsVolumeType: gp2
>         imagefsVolumeDevice: xvdb
>         type: m5.xlarge
>     version: v1.5.0
>   provisioner:
>     provider: aws
>     aws:
>       region: us-east-1
>       availabilityZones:
>         - us-east-1b
>       tags:
>         owner: sales-se@d2iq.com
>         expiration: 12h
>     version: v1.5.0
>   cloudProviderAccountRef:
>     name: aws-credentials
> ```

Deploy the cluster using kubectl.

```bash
kubectl apply -f cluster.yaml
```
**Validate that the cluster is deploying via the Kommander Dashboard**

From the dashboard page click the cluster page to validate the cluster is being deployed.  
![Kommander Dashboard - Clusters!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/dashboard-clusters.png)

To view the build logs, click on the "View Logs" link in the cluster card.  
![Kommander Dashboard - Kibana!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/kibana-dashboard.png)

The kubeconfig to access the new cluster is stored in a secret and can be downloaded via the cluster details menu on the cluster's card in the dashboard.
![Kommander Dashboard - kubeconfig!](https://github.com/mesosphere/kommander-workshop-student/blob/master/images/kubeconfig.png)

Test the connection to the newly deployed cluster.

```bash
kubectl get nodes --kubeconfig student001.kubeconfig
```

>```bash
> $ kubectl get nodes --kubeconfig ~/Downloads/student001-kubeconfig.yaml
> NAME                           STATUS   ROLES    AGE   VERSION
> ip-10-0-130-233.ec2.internal   Ready    <none>   20m   v1.17.8
> ip-10-0-131-46.ec2.internal    Ready    <none>   20m   v1.17.8
> ip-10-0-135-185.ec2.internal   Ready    <none>   20m   v1.17.8
> ip-10-0-138-221.ec2.internal   Ready    <none>   20m   v1.17.8
> ip-10-0-194-199.ec2.internal   Ready    master   23m   v1.17.8
> ip-10-0-197-53.ec2.internal    Ready    master   23m   v1.17.8
> ip-10-0-200-218.ec2.internal   Ready    master   23m   v1.17.8
> ```
---  
[<- Return to the Multi-cloud lab](https://github.com/mesosphere/kommander-workshop-student/blob/master/multi-cloud-lab.md#Multi-cloud-lab) --  
[Continue to the Multitenancy lab ->](https://github.com/mesosphere/kommander-workshop-student/blob/master/multitenancy-lab.md#Multitenancy-Lab)