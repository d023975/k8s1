# k8s1

k8s one

<https://www.coursera.org/learn/containerized-applications-on-aws/home/week/1>

<https://www.coursera.org/learn/foundations-of-red-hat-cloud-native-development>
2 <https://www.coursera.org/learn/managing-cloud-native-applications-with-kubernetes>

<https://www.coursera.org/learn/advanced-application-management-with-red-hat-openshift>


Set up on Linux


https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-convert-plugin
- kubectl convert --help




/home/d023975/K8S
https://minikube.sigs.k8s.io/docs/start/

minikube start
minikube dashboard
minikube dashboard --url



https://kubernetes.io/docs/tutorials/hello-minikube/

https://kubernetes.io/docs/tutorials/hello-minikube/#create-a-deployment

kubectl create deployment hello-node --image=registry.k8s.io/echoserver:1.4

kubectl get deployments

kubectl get pods

kubectl get events

kubectl config view

https://kubernetes.io/docs/tutorials/hello-minikube/#create-a-service



ClusterIP: Services are reachable by pods/services in the Cluster




NodePort: Services are reachable by clients on the same LAN/clients who can ping the K8s Host Nodes (and pods/services in the cluster) (Note for security your k8s host nodes should be on a private subnet, thus clients on the internet won't be able to reach this service)


LoadBalancer: Services are reachable by everyone connected to the internet* (Common architecture is L4 LB is publicly accessible on the internet by putting it in a DMZ or giving it both a private and public IP and k8s host nodes are on a private subnet)



Training K8S
https://www.edx.org/course/introduction-to-kubernetes 

d023975  / <passwd>


A monolith has a rather expensive taste in hardware. Being a large, single piece of software which continuously grows, it has to run on a single system which has to satisfy its compute, memory, storage, and networking requirements. The hardware of such capacity is not only complex and extremely pricey, but at times challenging to procure.


During upgrades, patches or migrations of the monolith application downtime is inevitable and maintenance windows have to be planned well in advance as disruptions in service are expected to impact clients. 



Microservices-based architecture is aligned with Event-driven Architecture and Service-Oriented Architecture (SOA) principles, where complex applications are composed of small independent processes which communicate with each other through APIs over a network.





https://www.youtube.com/c/TechWorldwithNana 
Intro K8S
https://www.youtube.com/watch?v=X48VuDVv0do&t=37s

< Notes taken from the extraordinary lessons from Nana >

- High Availability == No downtime
- Scalability
- Disaster recovery - backup and restore

- worker node / node 
  - pod - Abstraction of container - creates a layer on top of a container - App Container
  - DB pod

- pod
    - gets IP address, internal address
    - can die very easily, crash 
      - if new pod is created it gets a new IP address
- Service 
    - can be addressed to pod
    - lifecycle is independent of pod
    - gets an own IP address 

- Public service  / internal service 
  - public service can be reached form outside, internal service can't
  - stable IP address which can be attached to each pod
  - stable endpoint

- create public service to make app accessible to outside 
    - URL of port:  IP:PORT

- https://my-app.com   <--- ingress forwards to service , external secure http endpoint

- ConfigMap
  - add external app config
  - DBURL e.g., <-> environment
  - Connected to pod
  - Don't put credentials in the config map

- Secret - like config map to store secret data to stor it base64 encoded
  - connected to pod

- ConfigMap/secrets can be seen inside the pod a env vars


- Data Storage
  - DB pod
  - DB gets restarted, DB is gone :( 
  - Use volumes <-> attache physical storage to pod 
    - local or remote storage <-> is external , not part of the k8s cluster

- k8s does not manage persistence 

- Pod dies, what happens
  - downtime
  - replicate everything on multiple severs

- replicate the node which is attached to the same service / replica

- Deployment: define blueprint of pod, specify no. of replicas 

- Define deployments on top of pods to easily replicate

- pod dies, service stays alive service forwards to replica

- DBs can't be replicated via a deployment
  - BDs have state
  - Stateful Set needed
  - mysql, mongo, ELK should be created based on stateful sets, not based on deployments !!!

- StatefulSets is not really easy 
    ==> host full DB outside K8S cluster can make it easier

- Master / Slave
- One node has multiple pods
- node
  - 3 processes live on each worker node
    - container runtime (e.g. docker)
    - kubelet service schedules pods, runs pods, assigns resources to the pod
    - Kube proxy forwards requests to pods
      - Communication between nodes
        - SERVICES: load balancer 

  - MASTER NODES
    - manager worker nodes
    - Api Server
      - allow to deploy
      - cluster gateway
      - gets update requests
      - authenticates
      - schedule new pods, deploy 
      - Gate Keeper
    - Scheduler
      - gets request from Api Server
      - can start new pod on worker node
      - decides where to schedule new pods
      - kubelet gets request from scheduler
    - Controller Manager
      - pods die on any node 
      - detect death of node 
      - detect state changes
      - tries to recover by sending a request to the scheduler
    - etcd
      - key value store of the cluster / cluster brain !
      - cluster config 
      - cluster state
      
- K8s cluster is made of multiple master nodes
    - etcd is a distributed storage across all master nodes


- Small Cluster could consist of
  - two master nodes
  - three worker nodes

- Create new master/node server
  - get new bare server
  - install all the master/worker node processes
  - Add it to the cluster

- Minikube / kubectl

- Minikube
  - One Node cluster
  - All runs on one node master + worker
  - Docker preinstalled
  - creates a Virtual Box, K8s cluster runs inside 

- kubectl
  - command line client
  - interacts with Api Server

minikube start

d023975@rb-linux-laptop:~$ kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   22d   v1.24.3

d023975@rb-linux-laptop:~$ minikube status

d023975@rb-linux-laptop:~$ kubectl version


Basic kubectl commands

- kubectl get nodes
- kubectl get pods
- kubectl get services

kubectl create deployment NAME --image= ...

kubectl create deployment nginx-deployment --image=nginx    // takes nginx from docker hub

kubectl get deployments ->  shows deployment
kubectl get pod -> gets the pod


kubectl get replicaset 

// CHECK out the naming

Deployment manages replicaset, replicaset manages pod

kubectl edit deployment nginx-depl  // edit deployment
-- get config file of deployment
-- scroll down to image name // you can change the e.g. version
:wq

kubectl get pod  ---> you see the pod change !!!!

kubectl get replicaset  ---> see replicaset change


Debug pods
- kubectl logs <podname>
  - container must run
- kubectl describe pod <podname>
- kubectl exec -it <podname> -- bin/bash  /// terminal of container as root


kubectl get deployment
kubectl get pod
kubectl delete deployment <deployment-name>

kubectl create deployment simplification
  - use k8s config files
  - tell kubectl to use it
  - kubectl apply -f <config-file.yaml>

touch niginx-deploy.yaml
  - create basic nginx deployment file
  - name
  - no. of replicas
  - template
  - spec

kubectl apply -f <file-name.yaml>

local config can be edited now ---> adjust replicas, increase no.  of replicas an apply again:
kubectl apply -f <file-name.yaml>

kubectl get deployment/pod

https://gitlab.com/nanuchi/youtube-tutorial-series/-/blob/master/basic-kubectl-commands/cli-commands.md

Syntax of config file
=====================
- apiVersion
- kind: Deployment/Service
- metadata
  - name
- spec
- status (autogenerated by k8s), k8s compares status and spec and updates
  - see file nginx-deployment-result.yaml 

- etcd hols the status info 

Where to store config files
- store them with the code

see - nginx-deployment.yaml  / nginx-service.yaml

More about config files
- metadata contains labels, key value pair
- spec part selectors
 
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml

kubectl get pods
kubectl get service

kubectl describe service nginx-service // provides detailed config info

kubectl get pod -o wide // get more info about pods, e.g. IP addresses

kubectl get deployment nginx-deployment -o yaml // get status details from etcd
      --> see ./nginx-deployment-result.yaml


Deletion
- kubectl delete -f nginx-deployment.yaml
- kubectl delete -f nginx-service.yaml



Demo Project
=============

Create mongo db pod
- create internal service

Create mongo express
- need db conn, credentials
- use deployment.yaml for env vars and secrets
- needs external service for being accessed from the outside

Browser -> ext services -> expr pod -> mongo db service -> mongo db bod

kubectl get all, default minikube

create mongo db deployment - see here : demo-project/mongo.yaml
- docker hub search image config on docker hub
  - how to use image - section, get env_vars from here

- create secret for user / password
  - see  demo-project/mongo-secret.yaml
    - type: Opaque --> basic key value type
    - values base64 encoded
      - echo -n 'plaintextvalue' | base64
      - put it into mongo secret file

- cd demo-project
- kubectl apply -f mongo-secret.yaml
- kubectl get secret
- reference secret in deployment file then see demo-project/mongo.yaml

- kubectl apply -f mongo.yaml

- kubectl get all
- kubectl get pod
- kubectl get pod --watch

Create internal service for mongo db
- see also demo-project/mongo.yaml , Service section
- kubectl apply -f mongo.yaml to create service

- kubectl get service // to check
- kubectl describe service mongodb-service
- kubectl get pod -o wide
- kubectl get all | grep mongodb

create mongo express - see demo-project/mongo-express.yaml
- check mongo-express on docker hub
- check how to use, check ports, check env vars
- express needs to point to DB address internal service
- need credentials
- same secret retrieval as for DB file
- ME_CONFIG_MONGODB_SERVER taken from config map , centralized config
  - config map file see here demo-project/mongo-configmap.yaml
    - data : key value pairs

- Config mapp MUST be applied to cluster
  - kubectl apply -f mongo-configmap.yaml
  - kubectl apply -f mongo-express.yaml
  - kubectl get pod


  Access mongo express from external browser create service see here demo-project/mongo-express.yaml
  -  type: LoadBalancer   <- makes service external
  - nodePort -> external port range 30000 - 320000

  - kubectl apply -f mongo-express.yaml
  - kubectl get service
    - see types , cluster IP - default ,  Load Balancer - external service
        - service gets external IP 
            - minikube you see it a <pending>

    - minikube service mongo-express-service <- assign public ip address
        --> URL is provided then to access


Kubernetes Namespaces
=====================
- What is a namespace - organize resources, you can have multiple resources in a cluster
- namespace: virtual cluster inside k8s cluster
- default namespace
- kubectl get namespace
  - kubernetes-dashboard  // minikube
  - kube-system // not for our use - do not touch this
    - system processes
  - kube-public 
    - publicly accessible data -->kubectl cluster info
  - kube-node-lease
    - holds heartbeat info 
  - default
    - used by default

- create namespaces
  - kubectl create namespace <name>
  - kubectl get namespace
  - creation of namespace via configuration files
  
- Use cases
  - default namespace only ends up in chaos
  - group resources into namespaces
    - db namespace
    - monitoring namespace
    - elk namespace
    - nginx namespaces
  - two teams
    - deployments with identical names with default namespace overwrite other deployments
    - group by team
  - one cluster
    - staging 
    - development 
    - staging and development can then use common resources
  - blue green for applications
    - production blue
    - production green
    - use share resources 
  - limit resources
    - two teams same cluster, each has their own namespace 
      - project a
      - project b

  - limit resources by namespace 


- characteristics of name space
  - namespace a resource can't access other namespaces
  - configmap of namespace a can't be used in namespace b, configmap has to be copied
  - namespace must have own configmap
  - namespace must have own secrets
  - service can shared cross namespaces
  - configmap data contains then the key value pairs <namespace>.<service-name>
  - No namespace scope !!
    - volume 
    - node
    - list them kubectl api-resources --namespaced=false / true
    
  - create / get resources in specific namespaces
    - kubectl apply -f mysql-configmap.yaml --namespace=my-namespace
    - put inside the configmap file a namespace into the meta data
    - kubectl get configmap -n my-namespace
    - put it into the metadata

- Change namespaces
  - use tool kubens
    - sudo snap install kubectx 
  - kubens - lists namespaces
  - kubens my-namespace  // switches active namespace   
  - https://github.com/ahmetb/kubectx#installation


  K8s Ingress
  ============

  - pod <-> service
  - service becomes external service 
  - final product should have a domain name
  - use ingress and internal service
  - request -> ingress -> service -> pod
  - external service of type load balancer like this

        apiVersion: v1
        kind: Service
        metadata:
          name: mongo-express-service
        spec:
          selector:
            app: mongo-express
            # makes service external
          type: LoadBalancer  
          ports:
            - protocol: TCP
              port: 8081
              targetPort: 8081
              # external port
              nodePort: 30000

 - Ingress is different see here : kubernetes-ingress/dashboard-ingress.yaml
  - see routing rules

How to configure ingress in a cluster
  - you need implementation fo ingress -> ingress controller 
  - install ingress controller
    - pod or set of pods which run on node in k8s cluster
    - evaluate rules , manage redirection
    - entrypoint into the cluster
    - evaluates to which ingress component in the cluster requests are forwarded 
    - which implementation you can choose
      - https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
      - https://kubernetes.github.io/ingress-nginx/deploy/baremetal/
      - k8s nginx ingress controller

Cloud Service Provider, AWS, linode
  - cluster config would have cloud load balancer
  - browser ->  cloud load balancer -> redirect to ingress controller
    => few implementation effort


Bare Metal
  - configure entrypoints youself
            - https://kubernetes.github.io/ingress-nginx/deploy/baremetal/
  - external proxy server which can be software or hardware
    - gets public IP, open IP , ports which can be accessed externally
    - forwards request to ingress controller which forwards


Install ingress controller on minikube
- minikube addons enable ingress 
- kubectl get pod -n kube-system
    ===> shows you the running ingress controller amongst other stuff

After Ingress installation create ingress rules

kubectl get ns
kubernetes-dashboard should become externally available

kubectl get all -n kubernetes-dashboard
  - internal service + pod is there ==> create ingress rule
    - kubernetes-ingress/dashboard-ingress.yaml
      - create rules 

  - how to resolve DNS <-> IP ??


kubectl apply -f dashboard-ingress.yaml
kubectl get ingress -n kubernetes-dashboard

modify etc/hosts

define mapping

192.168.64.5   dashboard.com


 Ingress default backend
 - kubectl describe ingress dashboard-ingress -n kubernetes-dashboard
  - output Default backend: default-http-backend:80
      - handles unmapped requests 
          - e.g. 404 page not found 


routing rules for ingress:
  - paths -> multiple paths in ingress file
  - subdomains -> multiple hosts names in ingress file  


Configure SSL
- define tls in the spec section  :

spec:
  tls:
  - hosts:
    - myapp.com
    secretname: myapp-secret-tls
  rules: 
    - hosts

  referenced secret contains the tls certificate 


  secret-config

  apiVersion: v1
  kind: Secret
  metadata:
    name: myapp-secret-tls
    # must be in the same namespace as ingress component
    namespace: default
  data:
    # keys must be this way
    tls.crt: base64 encoded cert
    tls.key: base64 encoded key
  type: kubernetes.io/tls


HELM
======
 https://hub.helm.sh
 https://github.com/helm/charts 
 https://helm.sh/docs/intro/install/

 - k8s package manager

 - package manager for k8s, apt, yarn, homebrew
 - package k8s yaml files and distribute

 - kubernetes cluster
    - app pod
    - app service
    - deploy ELK into cluster
    - needed:
      - stateful set
      - configMap
      - K8S user with permissions
      - secret
      - services

ELK deployment may be standard
  - create once
  - package up somewhere
  - deploy the bundle of yaml files


helm chart
- bundle of yaml files
- push/pull from repo
- provide / use them 

helm install chart name <--  install many configs

sharing helm charts

cluster - deployment needed


helm search <keyword>

helm hub

public registries / private registries

helm is templating engine
  - multiple micro services
  - pretty similar up to docker name, app name, docker tags
  - without helm multiple config yaml files
  - if they differ by a few lines you can define blue prints for common services
      - create template file with
        {{ .Values.name }}
          defined in values.yaml

values.yaml
------------
name: my-app
container:
  name: my-app-container
  image: my-app-image
  port: 9001


====> one yaml for multiple services and replace values

- help for CI / CD, use template yaml files

- Other use case
  - deploy same set of apps across different k8s clusters
    - dev cluster
    - staging cluster
    - development cluster
    - production cluster


- https://github.com/helm/examples

  - https://github.com/helm/examples/blob/main/charts/hello-world/Chart.yaml
  - https://github.com/helm/examples/blob/main/charts/hello-world/values.yaml
  - charts directory for dependencies on other charts
  - https://github.com/helm/examples/tree/main/charts/hello-world/templates

helm install 
  - templates files are filled from values and being deployed

value injection into helm templates
- helm install --values=my-values.yaml <chartname>  // to override default values 

- my-values does not need to have all values
  - default and values will be merged into .Values object 

- helm install --set version=2.0.0


Release management with helm:
  - helm 2 , helm client and server == Tiller <- has to run in k8s cluster
    - Tiller allows release management
    - Tiller stores a history of chart executions
    - rollback is possible
    - Tiller has to much permissions ===>
  - helm 3 - tiller have been removed


Kubernetes Volumes
===================
see kubernetes-volumes folder

- configure persistency independent of pod life cycle
- storage must be available on all nodes 
- storage must be highly available even when cluster crashes
- directory

- persistent volume
  - create in yaml file , see here : kubernetes-volumes/persistent-volumes.yaml
    - storage capacity
    - must point to the storage
  - where does the storage come from
  - type of storage
  - multiple storage plugins can be configured for k8s cluster
  - spec section of config kubernetes-volumes/persistent-volumes.yaml describes the persistent storage --> spec section

- spec specifics depending on type - fsType

- Types of volumes --> see official k8s documentation

- persistent volumes are NOT namespaces

- local vs. remote volumes
  - local volumes, being tied to specific node
  - do not survive when cluster crashes

- persistent volume creation
  - need to be already in the cluster on the beginning
  - admin sets up cluster
  - k8s user deploys apps in cluster
  - admin needs to configures the storages
    - creates persistent volume components 
  - apps have to be configured to use persistent volumes via pwc: PersistentVolumeClaim

- Persistent Volume claim
  - kubernetes-volumes/persistent-volume-claims.yaml
  - whatever persistent volume satisfies the claim is used 
  - pods point in the volume section to the claim - see : kubernetes-volumes/pods-with-volume.yaml - volumes section 
  - claims must exist in the same name space as the pods

- pods finds volume though claim , -> volume is then mounted into pod and then into the contained container(s)

- Other volume types
  - Configmap
  - secrets
  - managed by k8s

- pod can use multiple volumes simultaneously 

Storage Class
  - provisions persistent volumes dynamically , see kubernetes-volumes/storage-class.yaml
  - storage backend ahs been define in persistent volume component kubernetes-volumes/persistent-volumes.yaml
  - use provisioner: kubernetes.io/aws-ebs  <- allows dynamic provisioning of storage
  - requested by pvc, see storageClassName: storage-class-name 


Stateful Set
=================

- used for stateful app
  - all databases
  - every app the stores data

- stateless apps are deployed using deployments

- stateful apps are deployed using StatefulSet

- mysql DB 
  - one pod that handles java app  requests, three pods
  - scale also the mysql
  - can't be created, deleted randomly because they differ from each other
  - stateful set maintains a sticky identity
  - pods need their own identity
  - single mysql is used for reading writing
  - 2nd DB <-> data inconsistency
  - just one pod is allowed to update for DBs - the MASTER, others are slaves
  - different db pods , different physical storages
  - synchronization is needed 
  - clustered DB setup allows sync, master changes, slaves update
  - new pod, clones first, then synchronizes
  - need to keep data when pods die in persistent storage
  - configure persistent volumes for stateful sets
  - stable pod IDs allow to correctly attach right persistent storage
  - stateful set pods have fixed names:  statefulset-name.ordinal
    - ordinal 0 is master
  - each pod gets its own DNS endpoint from the service, podname.service-name-<number>
      - pod restart, ip address can change but DNS name is kept
  - Configure stateful and data synch need to configured
  - management and backups need to be configured


  Kubernetes Services
  ====================

  - pods have dynamic ips
  - services have stable ips , point to pods
  - services provides load balancing to replicated pods

  - ClusterIP Services
    - default type
    - micro service app, pod with ms container
    - + log collecting container
    - both containers have different port
    - kubectl get pod -o wide (ip address info on pods)
    - pods get ips from node ip range
    - two container in on port same ip, different ports
    - having two worker nodes how to forwar from Ingress to pods
    - ingress --> Service --> pod(s)
    - service , gets IP + port, internal service, ingress forwards to service
    - service identifies pods by selectors , pods are labelled
    - service find pod ports by port mappings, targetPort

    - Multi Port Service have 2 B NAMED !!!

  - Headless Service
    - selectively communicate with pod from service, between pods
    - use case: stateful apps might need to require this
      - communicate wit the master instance only with write access
      - new cloned worker node must connect to previous node for replication
    - address pods directly via IP is needed in this case
    - Pod IPs can be gotten via DNS look up  , normally the service IP is returned 
      - to achieve this clusterIP: None has 2 B set
    - clusterIP: None 
    - kubectl get svc
    - often ClusterIP + Headless Service is used in parallel


    - Service type
      - ClusterIP
        - internal
      - NodePort
        - external IP, no ingress, IP exposed to external via nodePort - attribute - For development stuff
        - not secure
      - LoadBalancer
        - service becomes accessible through cloud providers LoadBalancer

  