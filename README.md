# my-kubernet-notes-local
#!!!!!!!!!!!!!!!!!************KUBERNETS*********!!!!!!!!!!!!!!!!!

!!!!!!!!!!!!!!!!!!1QUATIONS k8 SCINARIO
1)if there is container which has own emty volume if any condition container delete show it will be recreate or restart?
Ans:- recreate asnd delete old contaioner

2)if there container whihc has joined emty volume if container delete it will recreate so what will happen ?
ANS:- container will joined persitent volume and fecth data of older volume 
pod will have temroy volume 
and container will have persistent volume .


3)
+++++TASK++++
*student-u1 form shoud show in backend of oxer-html {contact button} --platform k8



***STEPS***platform docker
1.deploy student-ui app [copy link]acessed
2.download oxer-html 
3.configure index.html in oxer-html
4.add student-ui link in contact button above space 
5.vim dockerfile 
6.COPY oxer-html /usr/share/nginx/html/
7.give persmision it and build it.
8.run container 

---------
FOR k8 platform 
1.run tomcat-student image of your dockerhub 
2.download oxer-html and unzipit
3.cd oxer-html and build dockerfile with nginx copy all source without index.html
3.run image for testing image it work or not [shoud show nginx]
4.and push it image to the dockerhub wth specific tag 
5.change sorce code of index.html like paste link from tomcat container link in contact us button and change that you want
6.create configfile from index.html in cm.yaml
7.kubectl create deploy my-deploy --image=[nginx push image(oxer-html) without included index.htm image]
8.vim deploy.yaml 

 apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: my-deploy
  name: my-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-deploy
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: my-deploy
    spec:
      volumes:   #adable data from pod volume for label volume 
        - name: my-config
          configMap:
            name: my-config
      containers:
      - image: abhijitramteke/demo-image
        name: demo-image
        ports:
        - containerPort: 80
         volumeMounts:  #adable data from pod volume yaml
         - mountPath: /usr/share/nginx/html/oxer-html/
           name: my-config
9.this way we created configmap and deployment it will bind then apply configmap and deployment
$$$$ kubectl create deploy my-deploy --image=abhijitramteke345/tomcat-student:01 --port 80 --dry-run=client -o yaml >deploy.yaml

10.kubectl expose deploy my-deploy --type=NodePort --port 80 --target-port=80  [before create deploy you must assgin port 80 to create container niether it wont assgin port and wont access ]
11.access it 
=======================================================================================================


****6)minikube install 
1)kubectl  install 2)docker install 3)minikube install 4)add docker into docker group $ useradd -g docker docker

5)switch user into the docker and start minikube 
***on windows download .exe file
*https://awscli.amazonaws.com/AWSCLIV2.msi [application direct on our pc]& install minikube and start it 
*dockee install desktop
minikubestart:- minikube start --nodes 3 -p (node name)
===================================================================================================================== 
****5)eks cluster creation command on cli .
eksctl create cluster --name my-cluster --node-type t2.micro --nodes 1 --region us-east-1 --nodegroup-name nodes-kubes

STEPS:- ##install kubectl ##create role for instance to acess services from cli like eksfull ,system admi,ec2full. ##install aws cli ##
##install eksctl from doc {run command msut be one by one }##add credential on cli##then run above command.


		#!!!!To delete Cluster!!!!11
#$eksctl delete cluster --name clsuet-name --region us-east-1

					!!!!!!!!Commands!!!!!!!!
$kubectl exec -it (pod name)  [execute container in the pod]
$minikube start --node 3 {minikube name}
====================================================================================================================
			!!!!!!!!!!!!!!!*******K8 ARCHITECTURE**********!!!!!!!!!!!!!!
					
					******POD******

1)Pod:-pod is the smallest unit in kubernets,pod is nothing but the logical layer for container.where we can run multiple container at a once.it recreate the container after the deleted container.
$kubectl run mypod --port 80 --image=nginx 
$kubectl run nginx -n abhiit-ajit --image=nginx
$kubectl expose pod nginx -n abhijit-ajit --type=NodePort --port 80 --target-port=80

****kubernetes architecture based on master nodes and worker nodes .
master nodes is responsible  to perform the action on worker nodes.if master nodes fails we can not perform any action .
---master nodes includes control plane components such as 1) kube-api server,2)kube sceduler ,3)controller manager and 4)ETCD database.
---worker nodes include control plane component such as  5)kubelet,6)kube proxy,and 7)container runtime like docker or rocket
1)kube api -server:-kube-api server is a primary mangement component of kubernets,is responsible for orcheastrating  all opertation in the cluster .it is act like a gateaway of cluster .
___whenever user request to access cluster kube api server authoticate request and anayles its permision and perform the action based on permision .

2) kube sceduler:- kube-sceduler identifies the right nodes to place container based on resource requrnments like taints and tollaration,affinity,anti affinity .

3)controller manager:-control manager is engine that watches  state of cluster and maintain desired state .it include node-cotroller ,replication controller 
_node-controller maintain the desired state of node whereas replication controller maintain desired state of pods,
both are responsible to recreate it.

4) ETCD :- etcd databse is a critical part of the kubernets cluster.and etcd databse is Nosql database. that stores the state of the cluster including nodes and workloads such as container ip adress ,in which node container is deployed ,which nodes is empty etc.all the information store in a key/value format.
_we can not directly acess to the ETCD databse only kube api-server can acess ETCD databse .
we have ETCDCTL client through we can acess ETCD database.

*worker nodes component

5)Kublet:-is a service on nodes,which is connect nodes and master ensuring that pods and thier container in desired state ,it is also send report to the master .
_it is known as manager of nodes,and is responsible to perform all activity on nodes.

6)kube-proxy:-_kube proxy is a service that run on worker nodes ,it is responsible to communicate between pod .whenever pod is created it assgin the ip adress .
[to deal with individual host subneting and expose service to the extrenal world .it performs request forwarding to the correct pods across various isolated networks in a cluster.]
7)cotainer runtime interface(CRI):-it is runtime contanarizations .ex.docker .it is responsible to create container by kubernetes master.

!!!!!!!!*****WORKING OF KUBERNETS*******!!!!!!!!!11
_whenever user send request to create pod kubeapi-server accept request and analyse it and check its permision .based on permision and to create pod it call to etcd databse 

---->if i talk about kubernets architecture flow
firstly whenever user try to create pod ,request goes to kube-api server,
-->kube-api accept request and anlyse its permision according to the permission kube-api server authoticate and allow to to acess and create pod,then 
kube-api server send request to the kube scedular 
-->,kube-scedular iditifies write node to place container ,and send reports to the kube-api server
-->then kube-apiserver send request to the kubelet then
---> kubelet send request to the runtime in order to create pod.as soon as created pod, kubelet send reports to the kube-api server then kube-api server call the kube-proxy 
--->kube proxy assgin the ip adress to the pod in order to connect external world,as soon as assgin ip kube-proxy send report to the kube -api then 
---> kube-api server  call the etcd database 
--->,etcd databse store all the information in the database such as container ip adress,in which nodes container is deploied and which nodes is empty.
-then kube-api server call the after kube controller 
-->kube-controller  maintain the desird state of pod as well as nodes.it continiues moniter state of the nodes and pods,and ensure it is in  runinig state.
========================================================================================================
!!!!!!!!!!!!!!!!CONTROLLERS!!!!!!!!!!!!!!!
**define Deployment?
---->Deployemnt is the easiest and most useful resource for deploying our applications  in the kubernet cluster,it is known as controller,it matches current state of cluster to maintain desired state of cluster that we specify in kubernet yaml .it is most usefull for stateless application .
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**define Replicaset?
:-Replicasets purpose is to maintain a stable set of replica pod running at any given time .as such it is often used to garantee the avability of a specified number of identical pods.
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
2)Deployment vs Replicaset vs replication controller
Ans:-Pod>>>Replicaset>>>Dployment>>>service[replicaset help us to recreate container after the deleted and maintain desired state that we sepcify number of container.replicaset is nothing but pod controller ]

###[there are three type of pod cotroller 
1.replication controller:-it moniter  and controll the pod if any error in container it delete and recreate it.we specify selector to controll pod by using label ,we input as a selector and specify which pod you have to controll by maching label.e.g 
selector[replication maintain a desired state of pod it manage and maintain pod in case pod deletedit can mange number of pod  but does not scale automatically of number of pod.]
  team=dev
!!!replication controller does not cotrol multiple label of pod it can conrol only same label pod .
!!!it support roling update
!!!APi version=v1

2.replicaset:-replicaset is also manage and controll container by using labels but the diffrence is that between the replication controller and the replicaset replicaset can cotroll multiple pod label 
!!!it can cotrol multple label of pod.
!!!it does not support roling update
!!Api verion=apps/v1

3.deployment:-seeing this depeciency between replication cotroller and replicaset.
it can support roling update and also control multple pod at once with using label. 
----------------------------=============
chat gpt 
ReplicationController, ReplicaSet, and Deployment are all Kubernetes objects used for managing and scaling containerized applications. Here’s a breakdown of their differences:

1. **ReplicationController (RC):**
   - **Purpose:** Ensures a specified number of pod replicas are running at any given time.
   - **Selectors:** Uses selector labels to identify and manage pods.
   - **Updates:** Supports basic scaling and healing (replacing failed pods).
   - **Scaling:** Only supports manual scaling.
   - **Use Case:** Deprecated in favor of ReplicaSet and Deployment but still used in some legacy systems.

2. **ReplicaSet:**
   - **Purpose:** The successor to ReplicationController, providing more powerful pod management capabilities.
   - **Selectors:** Like RC, uses selector labels but with a more flexible set-based selector.
   - **Updates:** Supports rolling updates and can manage updates to pod templates.
   - **Scaling:** Supports manual scaling.
   - **Use Case:** Used for ensuring a specified number of pod replicas are running, managing updates, and ensuring reliability.

3. **Deployment:**
   - **Purpose:** Declarative management of ReplicaSets and pods, providing advanced features for managing applications.
   - **Selectors:** Manages ReplicaSets and pods based on labels but also manages the deployment lifecycle.
   - **Updates:** Supports rolling updates and rollbacks, allowing declarative updates to application configurations.
   - **Scaling:** Supports both manual and automated (based on metrics) scaling.
   - **Use Case:** Preferred way to manage applications in Kubernetes, providing automation for deploying and managing updates to applications.

**Key Differences:**

- **Scope of Management:**
  - **ReplicationController:** Basic pod replication and replacement.
  - **ReplicaSet:** Enhanced pod management with support for more complex selectors and rolling updates.
  - **Deployment:** Full lifecycle management including rolling updates, scaling, and rollback capabilities.

- **Updates and Rollbacks:**
  - **ReplicationController:** Limited support for updates, mainly replacing pods.
  - **ReplicaSet:** Supports rolling updates and managed updates to pod templates.
  - **Deployment:** Provides declarative updates and rollbacks with support for managing ReplicaSets and pods.

- **Recommended Use:**
  - **ReplicationController:** Legacy applications where basic pod replication suffices.
  - **ReplicaSet:** General use where more advanced pod management and updates are needed.
  - **Deployment:** Standard for managing applications in Kubernetes due to its advanced features for deployment, scaling, and updates.

In summary, while ReplicationController is being phased out in favor of ReplicaSet and Deployment, ReplicaSet offers more features for managing pods, and Deployment is the preferred way to manage applications, providing advanced automation and lifecycle management capabilities in Kubernetes.

========================================================================================================
##to how is connected our deployment with service
###With this command we can see exposed yaml file without expose service we run dry of our command and send this dry run expose service into the new yaml file such a way we can see how is write pur yaml file for exposing into service 
##step copy the deployment into yaml file then expose it without run it expose it with dry run and see yaml file then apply it and create service .
***4) kubectl expose deployment nginx-deployment --type=NodePort --name svc --dry-run -o yaml > svc.yaml

***3)deployment expose into service by command line 
$kubectl expose deployment (deployment name) --type=ClusterIP --port 80 --target-port=80

*** deployment with service yaml file [this file is created from deployment exposed in command line and output into svc.yaml .
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: abhijitramteke/myhtml-site:02
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: svc
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  type: NodePort
status:
  loadBalancer: {}
========================================================================================================**Replicaset:- 
replicaset enable us to can manage number of pod it will be clone of privious pod and create one by one.
--
============
***deomonset vs replicaset vs statefulset?chtgpt**********
Certainly! Let's clarify the differences between DaemonSet, ReplicaSet, and StatefulSet in Kubernetes:

1. **DaemonSet**:
   - **Purpose**: Ensures that a specific pod runs on all or some nodes in a Kubernetes cluster.
   - **Use Case**: Typically used for system daemons (e.g., logging agents, monitoring agents) that need to run on every node.
   - **Behavior**: Automatically creates pods on nodes, and ensures that if a node is added to the cluster, the pod is also added to it; if a node is removed, the pod is garbage collected.
   - **Example**: Running a logging agent on every node to collect node-specific logs.
------

2. **ReplicaSet**:
   - **Purpose**: Ensures a specified number of pod replicas are running at any given time.
   - **Use Case**: Used to guarantee availability and scalability of pods.
   - **Behavior**: Monitors the number of running pods and creates or deletes pods to match the desired state (specified by the user).
   - **Example**: Running multiple instances of a web server to handle incoming traffic, with automatic scaling up or down based on demand.
-----

3. **StatefulSet**:
   - **Purpose**: Manages the deployment and scaling of a set of pods, while providing guarantees about the ordering and uniqueness of these pods.
   - **Use Case**: Ideal for applications that require stable and unique network identifiers, persistent storage, and ordered deployment and scaling.
   - **Behavior**: Provides stable, unique network identities and persistent storage to each pod. Pods are created sequentially and have stable hostnames that persist across restarts.
   - **Example**: Running a database cluster where each pod represents a database node with its own persistent storage and identity.

**Key Differences**:
- **Pod Management**: 
  - **DaemonSet**: Ensures a pod runs on each node.
  - **ReplicaSet**: Maintains a specified number of identical pods.
  - **StatefulSet**: Manages deployment and scaling of pods with unique identities and stable storage.

- **Pod Behavior**:
  - **DaemonSet**: Typically stateless and can be scheduled on any node.
  - **ReplicaSet**: Stateless, with identical copies that can be replaced without specific order.
  - **StatefulSet**: Stateful, with unique identities, stable storage, and ordered deployment.

- **Use Cases**:
  - **DaemonSet**: Infrastructure-level services (logging, monitoring).
  - **ReplicaSet**: Stateless applications needing scalability.
  - **StatefulSet**: Stateful applications requiring unique identities and stable storage.

Understanding these distinctions helps in choosing the appropriate Kubernetes resource based on the specific requirements of your application or infrastructure service.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
========================================================================================================
**daemon set :-is a service which run single copy of pod on multple nodes to ensure monitering of runnig resourses on different nodes for monitering resuses and collect the data of nodes.
typically use cases 
running a cluster storage daemon on every node
running a logs collection daemon on every node
running a node monitoring daemon on every node.
>---A daemonset is another controller that manages pods like Deployment,and replicaset,and statefulsets .it was created for one particular purpose .
ensures that pod it manages to run on all the cluster nodes.as soon as the  new node joins the cluster,daemonset ensure that run on new nodes . 

---------
****Sidecars containers:- sidecars conatiner are container those are needed main container to run alongside .
---------------[sidecar container run in pod with main container which is integrated with main>volume>sidecar container
which is also log aggrigator ,it is collect logs .we use sidecars container when our aplications needs another aplication to supportive run]

***Deployment:   [chatgpt]
Purpose:

General Use: Designed for stateless applications or microservices where each instance is interchangeable.
Example: Web servers, APIs, front-end services.
Scaling:

Horizontal Scaling: Scales by increasing or decreasing the number of replicas (pods) based on demand.
State Management: Does not manage stateful behavior or provide guarantees about the ordering of deployment.
Pod Identity:

Pod Naming: Pods are typically given non-deterministic names.
Stable Network ID: Each pod gets a stable network identity, but the naming and identity are not closely tied to the pod's ordinal index.
Storage:

Volumes: Supports ephemeral volumes and persistent volumes, but does not manage the lifecycle or ordering of storage.
Updates:

Rolling Updates: Supports rolling updates to ensure zero-downtime updates to the application.
Use Cases:

*****!!!!!!!define Stateless Applications:> Ideal for applications that do not require persistent storage or guaranteed ordering of deployment and termination.
StatefulSet:
Purpose:

***!!!!!! define Stateful Applications:> Designed for stateful applications that require stable, unique network identifiers and stable storage.
Example: Databases (e.g., MySQL, PostgreSQL), key-value stores (e.g., Redis), messaging queues.
Scaling:

Stateful Scaling: Scales by increasing or decreasing the number of replicas (pods), but maintains a stable identity and storage for each pod.
Pod Ordering: Guarantees the ordinal index of each pod, which is maintained across rescheduling and scaling events.
Pod Identity:

Stable Naming: Pods have deterministic, stable names based on their ordinal index.
Stable Network ID: Each pod receives a stable network identity that persists across restarts and rescheduling.
Storage:

Persistent Storage: Manages the lifecycle and ordering of persistent storage for each pod, ensuring data persistence.
Updates:

Ordered Updates: Supports ordered, graceful deployment and scaling operations that respect pod identity and storage.
Use Cases:

Stateful Applications: Suitable for applications that require persistent storage, stable network identifiers, and ordered deployment and scaling operations.
Key Differences:
State Management:

***Diffrence between statefulset and deployment [chat gpt]

Deployment: Focuses on managing stateless applications with interchangeable instances.
StatefulSet: Designed for managing stateful applications with unique identities and persistent storage requirements.
Pod Identity:

Deployment: Pods are typically identified by non-deterministic names and may not have a predictable ordinal index.
StatefulSet: Pods have deterministic, stable names and ordinal indices, ensuring consistent identity and ordering.
Storage Management:

Deployment: Supports both ephemeral and persistent volumes, but does not manage the ordering or identity of storage.
StatefulSet: Manages persistent storage, ensuring that each pod gets the correct volume and maintains data integrity during scaling and updates.
Use Cases:

Deployment: Best suited for applications where instances can be added or removed without specific identity or storage concerns.
StatefulSet: Best suited for applications that require stable identity, persistent storage, and ordered operations, such as databases and stateful services.
In summary, Deployment is ideal for stateless applications where instances can be easily replaced or scaled horizontally, while StatefulSet is designed for stateful applications that require stable identities, persistent storage, and ordered management of pod lifecycle and updates.

	*****DIfrences between Deployment vs statefulset ?own *******************

**statefulset :->>is a kubernet resourses that allows us to manage and deploy stateful application for e.g data base which is requred persistence stoarge to store its data and retrive it .known as stateful application 
 [in words,steateful set allow us to copy data from one pod to another pod and assgin host name of first pod it will be clone of first pod data will pull and push to the host pod  e.g mysql,redis]
-it needed persistence volume to run properly like databse to store its data 
-
**Stateless:-
 :-it is a kubernet resource that allow us to manage and deploy stateless aplication for e.a webserver which serve the content without having any persistence volume like datbase .

:------>deployment and statefulset both work same there are some key difrence stateful set serve additional feature
both  create replicas !
1)>>Deployemnt :-deployment does not provide unic identification to the pod.
>>statefulset:-it provide unic identifuication to the pod.
[this term come because if we deploy our aplication by using deployment if pod deleted and recreate its ip change thats why we use statefulset][in statefulset pod host name are stable so in case pod deleted and recreated it will assgin same pod name ]

2)>>>deployment create and delete  pod parallelly [in one time all pod]
>>wheras statefulset create and delete pod horizontally [one by one]

-->>so in this way if -we use deployment and pod deleted and recreated then  pod does not syncronise data of first pod in that case data will be not updated.
but- if we use statefulset pod save host name of main pod [that pointed with service]syncronise the data as central pod and save host name as soon as if we increase  replicas new pod will clone  data of privious pod and save host name .

========================================================================================================
***Namespace:-there are 4 kind of namespace in kubernet.
--namespace is a logical division in a kubernets which segrigate for leaving pod seperatily,using namespace we can view our partuluar namepsace pod like monitering pod,production pod.
--namespace is isolated space where we run our pod.
[if we want to privacy of our pod we use namespace for protecting our pod from anyone]
-k8 provide us default namespaces.
1.default :->it is for publickly accsisible namespace anyone can create  nad delete the pod from default namespace and it is not recomded for k8

2.kube-system:--kube -system is a namespace that run all controll plane component as pod such as kube-api server,kube-scedular,kube controller ,etcd databse,kube-proxy,kubelet  by using kubeadm cluster.

 3.kube-public :-->if we have to publickly available  any file for anyone throughout the cluster so we use kube-public namespace ,mostly it is reserved for all cluster.

4.kube-node-lease:--->lease objects associated with each  nodes which imorove performnce of node heartbeats as the cluster scales along with that to deploy monitering and security plugins thats why we use this namespace.

$kubectl get namespace or ns [see namespaces]
$kubectl -n kube-system get pod  [see pod in particular namespace] or $kubectl -n kube-system get pod -o wide [full details with ip]

$kubectl -n my-project1 run mypod2 --image=nginx {run pod in particular namespace}
		OR[colleage]
$kubectl create namespace abhijit-ajit --dry-run=client -o yaml > ns.yaml
$kubectl get ns 
$kubectl run nginx --image=nginx -n abhijit-ajit
$kubectl get pod -n abhijit-ajit

=====================================================================================================================
8)Configmap:-configmap is an api object which is used to store  non-confidental data in key-value pairs.

!!!!CREATION OFCONFIGMAP!!!
$ vim index.html [add data anything ["hello"]
$ kubectl create configmap (myconfigmap) --from-file=index.html --dry-run=client -o yaml > cm.yaml
[we create configmap and pick data drom index.html with no run this command and send this output via yaml to the cm.yaml ]

$vim cm.yaml
 [we can see yaml cm where data pic from index.html without keeping in dockerfile we send additionally data to the container via configmap which isnon-confidential]

vim cm.yaml
###This created file by create configmap command 
apiVersion: v1
data:
  index.html: |
    Hii This is Abhijit Ramteke Khapri
    Welcomes you
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-config
---
###=====>>This file copy from configmap yaml with pod serach configmap volume mount and add container port if not there

apiVersion: v1
kind: Pod
metadata:
  name: cm-pod
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
      - containerPort: 80
      volumeMounts:
      - name: my-config
        mountPath: /usr/share/nginx/html/
  volumes:
    - name: my-config
      configMap:
        name: my-config

$$kubectl apply -f cm.yaml
$$kubectl get cm
$kubectl get pod -o wide or describe cm 
$kubectl port-forward pod/(my-pod name) :8080   [forward port]
$ kubectl exec -it pod-name curl localhost  or hit it on browser [curl http://ipor localhost]

====================================================================================================================
9)secret:-secret is a an api object in a kuberenet which is used to store confidential data such as token,password or a key.such a information we put pod specification or in image container.using a secret means we dont need to include this information wjicle creating application code we can addtionaly add this information by using secret.there is less risk to use secret being exposed of workflow of durig viewing pod ,eding pod and creating . 
$ vim index.html [username =abhijit password=12345}
$kubectl create secret generic my-secret --from-file=index.html --dry-run=client -o yaml > secret.yaml
$vim secret.yaml

apiVersion: v1
data:
  index.html: dXNlcm5hbWU6QWJoaWppdApwYXNzd29yZDogMTIzNDUK
kind: Secret
metadata:
  creationTimestamp: null
  name: nginx-secret
---
apiVersion: v1
kind: Pod
metadata:
  name: secret-file
spec:
  volumes:
    - name: nginx-secret
      secret: 
        secretName: nginx-secret
  containers: 
    - name: my-pod
      image: nginx
      ports:
      - containerPort: 80
      volumeMounts:
        - name: nginx-secret
          readOnly: true
          mountPath: /usr/share/nginx/html/
$kuectl apply -f secret.yaml
$kubectl describe pod (secret-file)
$kubectl exec -t (secret-file) curl localhost
$kubectl port-forward pod/(my-pod name) :8080   [forward port]

there are three type of secret
1)generic 2)docker registry and 3)tls.
=====================================================================================================================
11)Resource limit:-

=======================================================================================================
12)resourcequota:we applied on namespace for should not be  get affect on other pod .
$kubectl create resourcequota my-quota -n abhijit-ajit --hard=cpu=1,memory=2 --dry-run=client -o yaml > resource.yaml
$ls > $kubectl apply -f resourcequota.yam  
or $kubectl create deployment my-deploy --image=nginx --replicas=15 -n abhijit-ajit[for check]or generate in output method
$kubectl create deployment my-deploy --image=nginx --replicas=15 -n abhijit-ajit --dry-run=client -o yaml > deployment.yaml
========================================================================================================
13)Sceduller:-sceduled helps us to sceduled our pod in our particular nodes with using labels ,node lelector 
fistr we must asgin labels.


			!!!!!Label!!!!
1>labels assgin :-kubectl run mypod --image=nginx -color=red --dry-run=client --o yaml >pod.yaml
--kubectl apply -f mypod.yaml 
--kubectl get pod --selector=color=red
10)Label:-label is api object which is used to sceduled the pod on specific node.
1.taint & tolaration
2.node scedular
3.node affinity  and anti affinity	



1)taints and toolaration
------Tolerations are applied to pods. Tolerations allow the scheduler to schedule pods with matching taints. Tolerations allow scheduling but don't guarantee scheduling: the scheduler also evaluates other parameters as part of its function.

---Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints
--we applied taints to the node.taints allow to sceduler to scedule pod only which has  mathiching  toolaration .
--we use this concept for do not scedular specific pod on specific node.

$kubectl taint node node01 abhijit=ajit:NoSchedule  [we applied taint to the node01]



$kubectl create label node master (node=master)
&$kubectl create label node node1 (n0de=worker)
or $kubectl label nodes (node name)  disktype=ssd  & to unlabel it $kubectl label node (node name) disktype-
$kubectl get nodes --show-labels

apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    env: test
spec:
  containers:
  - name: mycontainer
    image: nginx
    ports:
    - containerPort: 80
  nodeSelector:   #nodeselector
    disktype: ssd

--we use node slector according to our aaplication resource requrnment.

2)node infinity:-we secuduled pod on specific node with multiple action of our condition.


========================================================================================================
14) Diffrenece between statfulset and demonset ?
demonset:-manage the pod in multiple nodes all pod together for same pod if we delete the demonset it will remove all pod automatically[own]

========================================================================================================
15)ingress :ingress is aapi object which expose https and http route from out side the cluster
-it support path base and host based routing ,load balancing ssl termination 
there are multiple  type of ingress
1.nginx controller 2.citrix ingress controller3.voyager 4,istio ingress5.contour controller6.traefik7.apache APIsix8HA proxy ingess
9.wallaarm
--we deploy with ingess types in according to ouir requrnment 
---if we choose ingress controller for deploying aplication we need create resource file and we specifyes conditions and action on it.
request------>path--->send on  
=======================================================================================================
				!!!!!!!!!111 Service!!!!!!!!!!!!!11
:-Service is a method for  exposing a  network aplication that is runing on the one or more pod in the cluster. 
-Service is a static IP address or a permanent IP address that can be attached to the Pod.
-We need Service because the life cycles of Service and the Pod are not connected, so even if the Pod dies, the Service and its IP address will stay ,so we don’t have to change that endpoint every time the Pod dies.

---there are 4 types of service in the kubernet


1)clusterip service:- is the most common Service as well as it is the Default type of Service, meaning when you create a Service and not specify a type it will automatically take ClusterIP as a type.cluster ip allow us to communicate between internal resources.

2)NodePort service:- is a Service that is accessible on a static Port on each Worker Node in the cluster. The NodePort Service makes the external traffic accessible on static or fixed port on each worker Node which is different from the ClusterIP Service which was only accessible within the Cluster (no external traffic can directly address the ClusterIP Service
the browser request will come directly to the worker node at the Port that the service specification defines and the Port that NodePort service type exposes is defined in the NodePort attribute. The NodePort value has a predefined range between 30,000 and 32,767.

3)LoadBalancer service:-type allows the Service to become accessible externally through a Cloud Provider’s Load Balancer functionality. LoadBalancer Service is an extension of NodePort Service. 
Whenever we create a LoadBalancer Service, NodePort and ClusterIP Services are created automatically by Kubernetes to which the external Load Balancer of the cloud platform will route the traffic to.

4)headless service:-Headless Service is a type of Service that allows the client to directly communicate with the Pods. It is a useful tool for building distributed applications. Headless Services are used in deploying Stateful Application in Kubernetes.

apiVersion: v1
kind: Pod
metadata:
  name: my-site
  labels:
    myapp: abhijit
spec:
  containers:
  - name: my-container
    image: abhijitramteke/myhtml-site:03
    ports:  
      - containerPort: 80   #while expose service we add more space in this fileld
        name: my-port    #container name should be in numeric (-) not space 
---
apiVersion: v1
kind: Service
metadata:
  name: my-svc
spec:
  type: NodePort
  selector:
    myapp: abhijit
  ports:
  - name: myport-svc
    protocol: TCP
    port: 80
    targetPort: my-port  

				!!!!!!!!!!deployment service!!!!!!!!!!!!!!!!11
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
				

					!!!!!!!!!!!!service for deployement!!!!!!!!!!!!
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
=======================================================================================================
			!!!!!!!!!!!!11 Static pods!!!!!!!!!1
**define static pods ?
:-static pods managed by kubelet daemon on specific nodes without having control plane componet.
we create directory on the worker node to store pod yaml file ,kubelet check yaml file  in that directory in every time ,if file deleted it remove those pods.and such a way all control plance compenet run as staic pod in kube-system namespace.
[-Static pod enable us to run pod by using kubelet,we dont need any componet to run the pod but we can create only pod .]
