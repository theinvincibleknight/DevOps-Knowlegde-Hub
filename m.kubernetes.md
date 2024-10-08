# Kubernetes

**What is Kubernetes?**  
Also known as K8s, it is an open-source system for automating the deployment, management, and scaling of containerized applications.

**What is a Container?**  
- A way to package an application with all the necessary dependencies and configurations.
- It can be easily shared.
- It makes deployment and development efficient.

**What is Container Orchestration?**  
Container orchestration is a method for managing the deployment, scaling, and operation of containerized applications. Containers package an application and its dependencies together, making it easier to deploy consistently across different environments. However, managing containers at scale—especially when dealing with many instances or across multiple machines—can be complex. Container orchestration tools help automate and streamline this process.

Example: The master guides the players in orchestration. Similarly, Kubernetes guides the containers in containerized applications.

**Kubernetes Architecture**  

When you deploy Kubernetes, you get a cluster.  
Two important parts are: Master (Control Plane) and Worker nodes.

- **Nodes (Minions)** - A node is essentially a server where you install container-based applications.
- **Cluster** - A group of nodes is called a cluster.
- **Master** - This is Kubernetes, used to manage the nodes. The Kubernetes Master will have an API Server that connects to all the worker nodes using an agent called Kubelet.

**What is a Pod?**  
A single instance of a running process in a cluster. It can run one or more containers and share the same resources.

**Components of Kubernetes:**

The Kubernetes Master will have:
1. **API Server** - Provides the interface called Kubectl to interact with the worker nodes.
2. **Scheduler** - Assigns nodes to newly created pods.
3. **ETCD** - A key-value store containing all cluster data.
4. **Controller Manager** - Responsible for managing the state of the cluster.

Worker Nodes will have:
1. **Kubelet** - An agent that ensures containers are running in pods.
2. **Container-based app in an isolated environment (POD)** - Containers run in a pod.
3. **kube-proxy** - Maintains network rules for communication with pods.
4. **container-runtime** - A tool responsible for running containers.

**Features of Kubernetes:**
- Container Orchestration
- Scalability
- Load Balancing
- High Availability
- Rollouts & Rollbacks
- Lifecycle Management
- Declarative Model
- Persistent Storage
- Resilience and Self-Healing

Sample YAML Config file:

```yml
apiVersion: v1
kind: Pod 
metadata:
  name: my-pod 
spec:
  containers:
    - name: my-container 
      image: nginx
      ports:
      - containerPort: 80
```

### Installation of K8s on Linux

Installation Guide [Official Documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

First, you need to install Kubectl. In this example, we are using a RedHat-based distribution for installation.

Run the following command to update the repository on the RedHat server:

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
EOF
```
Once the repository is updated, you can install kubectl:

```bash
sudo yum install -y kubectl
```
Verify the installation:

```bash
# kubectl version --client
Client Version: v1.28.4
Kustomize Version: 5.0.4-0.20230601165947-6ce0bf390ce3
#
```
The next step is to install **MiniKube** [Official Documentation](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Farm64%2Fstable%2Frpm+package) (Minikube is a local Kubernetes solution that uses a single-node cluster in which both master and worker nodes exist, for practice purposes).

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.aarch64.rpm
sudo rpm -Uvh minikube-latest.aarch64.rpm
```

Verify the installation:

```bash
# minikube start
```

When Minikube is installed, by default, it picks the **docker** driver. However, the **docker** driver should not be used with **root** privileges. Therefore, it gives an error when you run `minikube start`. You can create another user and add it to the docker group to run Minikube.

We already have a user named **paul**. Just switch to that user, add the user **paul** to the **docker** group, and then run Minikube:

```bash
# su - paul
$ sudo usermod -aG docker $USER && newgrp docker
$ id paul
uid=1000 (paul) gid=976(docker) groups=976 (docker), 10(wheel), 1000 (paul) context=unconfined u:unconfined
u: unconfined_r:unconfined_t: 50-s0:cO.c1023
$ systemctl start docker
$ minikube start
$ minikube status
minikube
type: Control Plane 
host: Running 
kubelet: Running 
apiserver: Running 
kubeconfig: Configured
$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

When you deploy Kubernetes, you get a master node (Control Plane) and worker nodes. Using Minikube, we are running both master and worker nodes on one machine locally.

Now that everything is in place, we can run applications in Kubernetes. In Kubernetes, a pod is the smallest unit, and containers run inside the pod.

### Deployment of an App in K8s

We have installed and set up Kubernetes on a MacBook too, using the official documentation. The following example is being deployed from a MacBook.

You can use the pre-existing Docker image from DockerHub for the deployment:

```bash
# kubectl create deployment my-nginx --image=nginx:latest
deployment.apps/my-nginx created
# kubectl get deployments
NAME     READY UP-TO-DATE  AVAILABLE AGE
my-nginx 1/1   1           1         23s
# kubectl get pods
NAME                      READY STATUS  RESTARTS  AGE
my-nginx-86d74cfc8f-ltq54 1/1   running 0         52s
# minikube dashboard
```
`minikube dashboard` opens the dashboard window in browser, where you can visually get the details of the deployments, pods, etc.

Now Nginx is deployed inside the container, which is inside the pod. Nginx usually uses port 80 for access. However, the pod is in an isolated environment, and we cannot directly access the application inside the pod using port 80. Similar to port-binding in Docker, you need to use a service object in Kubernetes to make the container application accessible via the internet.

```bash
# kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
service/my-nginx exposed
# kubectl get services
NAME        TYPE          CLUSTER-IP      EXTERNAL-IP PORT(S)       AGE
kubernetes  ClusterIP     10.96.0.1       <none>      443/TCP       5d18h
my-nginx    LoadBalancer  10.104.234.173  <pending>   80:31132/TCP  125s
# minikube service my-nginx
```

**Commands Overview:**

- **`minikube start/delete`**: Starts or deletes a Minikube cluster.
- **`minikube status`**: Displays the status of the Minikube cluster.
- **`minikube dashboard`**: Opens the Kubernetes dashboard for visual management of resources.

- **`kubectl create deployment my-app --image=link`**: Creates a deployment named `my-app` using the specified Docker image.
- **`kubectl get deployments`**: Lists all deployments in the current namespace.
- **`kubectl get pods`**: Lists all pods in the current namespace.
- **`kubectl delete deployment my-app`**: Deletes the deployment named `my-app`.

- **`kubectl expose deployment my-app --type=LoadBalancer --port=80`**: Exposes the `my-app` deployment as a service with a LoadBalancer on port 80.
- **`minikube service my-app`**: Opens a web browser to access the service named `my-app` in Minikube.
- **`kubectl get services`**: Lists all services in the current namespace.

**Kubernetes Basic Modules:**
1. Create a Kubernetes cluster
2. Deploy an app
3. Explore your app
4. Expose your app publicly
5. Scale up your app
6. Update your app

### Create a Web App Demo Project

First, you need to install Node.js on your system. We will use Visual Studio Code to edit the files. Install it if you don't already have it.

Open VS Code, navigate to the folder where you want to create your project, right-click, and open the Integrated Terminal. Run the following commands to create a basic test React app:

```bash
% node -v
v20.9.0
% npx create-react-app testapp
% cd testapp
$ npm start
```

Once you run `npx create-react-app testapp`, it will create a **testapp** folder and generate all the necessary files to run the test React app.

**Creating Dockerfile**

In the **testapp** folder, you can delete the **node_modules** directory, as we don't want it to be copied while creating an image from the Dockerfile. We will use `RUN npm install` in the Dockerfile to get the node modules.

Now create a new file inside the **testapp** folder, named `Dockerfile`.

Dockerfile:
```Dockerfile
FROM node

WORKDIR /myapp

COPY . .

RUN npm install

EXPOSE 3000

CMD ["npm", "start"]
```

**Creating and Pushing Docker Image**

You can't use a local image for deployment in Kubernetes; you need to push the image to a repository and pull it from there.

Create a repository on Docker Hub, for example, `philippaul/webapp-demo`:

```bash
% cd testapp
% docker ps
% docker build -t philippaul/webapp-demo:02 .
% docker images
REPOSITORY              TAG IMAGE ID      CREATED         SIZE
philippaul/webapp-demo  02  c806ab1654c3  17 seconds ago  1.4GB
% docker login
% docker push philippaul/webapp-demo:02
```

- Navigate to the **testapp** folder where you have all the required files and `Dockerfile`.
- Build an image with the tag matching your repository using `docker build -t <repo_name> .`.
- Log in to Docker Hub using `docker login`.
- Push the image with its name and tag using `docker push <image_name:tag>`.

### Deployment of Our Web App in K8s

Now, using the image we have created, deploy a web app in the Kubernetes cluster:

```bash
% minikube start
% minikube status
% kubectl create deployment my-webapp --image=philippaul/webapp-demo:02
deployment.apps/my-webapp created
% kubectl get deployments
NAME        READY UP-TO-DATE  AVAILABLE AGE
my-webapp   1/1   1           1         23s
% kubectl get pods
NAME                       READY STATUS  RESTARTS  AGE
my-webapp-66d74cfc9f-ltq69 1/1   running 0         52s
% kubectl describe pods
% kubectl logs my-webapp-66d74cfc9f-ltq69
```

- Check the status using `minikube status`. If not started, start it using `minikube start`.
- Create a deployment using `kubectl create deployment <app_name> --image=<image_name:tag>`.
- Check the deployments using `kubectl get deployments`.
- Check the pods using `kubectl get pods`.
- To get detailed information about the pods, use `kubectl describe pods`.
- To get logs from a particular pod, use `kubectl logs <pod_name>`.

**Exposing Port Using Service in K8s**

```bash
% kubectl expose deployment my-webapp --type=LoadBalancer --port=3000
service/my-webapp exposed
% kubectl get services
NAME        TYPE          CLUSTER-IP      EXTERNAL-IP PORT(S)         AGE
my-webapp   LoadBalancer  10.108.38.25    <pending>   3000:30952/TCP  13s
% minikube service my-webapp
```

After you expose the application inside the container to the internet, you will be able to access the web app using `localhost:3000` or the link provided in the output after executing `minikube service <appname>`.

### Updating Our App in K8s Rollout

You have made some changes in the code and want to deploy those updates to the application. First, you need to build a new image and push it to Docker Hub:

```bash
% docker build -t philippaul/webapp-demo:05 .
% docker push philippaul/webapp-demo:05
```

Currently, our application is up and running in a live environment. We want to update the application without downtime. Kubernetes handles this; you just need to point the deployment to the new image.

```bash
% kubectl get deployments
NAME      READY UP-TO-DATE  AVAILABLE AGE
my-webapp 1/1   1           1         171m

% kubectl get pods
NAME                        READY STATUS  RESTARTS  AGE
my-webapp-848c799c9b-ds9td  1/1   Running 0         23m

% kubectl set image deployment my-webapp webapp-demo=philippaul/webapp-demo:05
deployment.apps/my-webapp image updated

% kubectl get pods
NAME                        READY STATUS            RESTARTS  AGE
my-webapp-7c7b89cf99-rdnb4  0/1   ContainerCreating 0         8s
my-webapp-848c799c9b-ds9td  1/1   Running           0         26m

% kubectl get pods
NAME                        READY STATUS        RESTARTS  AGE
my-webapp-7c7b89cf99-rdnb4  1/1   Running       0         42s
my-webapp-848c799c9b-ds9td  1/1   Terminating   0         26m

% kubectl get pods
NAME                        READY STATUS        RESTARTS  AGE
my-webapp-7c7b89cf99-rdnb4  1/1   Running       0         50s
```

- To point the deployment to the new image, use `kubectl set image deployment <deployment_name> <container_name>=<new_image_name:tag>`.
- As soon as you point the deployment to the new image, Kubernetes starts deploying a new pod and terminates the old one. The old pod is not terminated until the new pod is ready, ensuring no downtime. You can monitor this process using `kubectl get pods`.
- This is how you roll out changes in Kubernetes in a live environment.
- You can switch to any version of the pods by pointing the image to the desired version. You just need to have the image versions available in the Docker Hub repository.

### Rollback of Project in K8s

In case you point the deployment to the wrong image or an image that doesn't exist, Kubernetes will update the image, but when it tries to pull the image from the Docker repository and gets stuck in the `ImagePullBackOff` state.

```bash
% kubectl set image deployment my-webapp webapp-demo=philippaul/webapp-demo:05
deployment.apps/my-webapp image updated

% kubectl get pods
NAME                        READY STATUS            RESTARTS  AGE
my-webapp-6d6f889468-w91c9  0/1   ImagePullBackOff  0         8s
my-webapp-7c7b89cf99-rdnb4  1/1   Running           0         3m

% kubectl rollout status deployment my-webapp
Waiting for deployment "my-webapp" rollout to finish: 1 old replica is pending termination...
```

To check the rollout status, use `kubectl rollout status deployment <deployment_name>`. In the example above, it is waiting for the rollout but will not finish since there is no such image to pull and complete the deployment.

To roll back the deployment, use `kubectl rollout undo deployment <deployment_name>`:

```bash
% kubectl rollout undo deployment my-webapp
deployment.apps/my-webapp rolled back

% kubectl get pods
NAME                        READY STATUS     RESTARTS  AGE
my-webapp-7c7b89cf99-rdnb4  1/1   Running    0         8m
```

### Self-Healing in K8s

Example: We are creating a deployment using the image `node-demo-app` and exposing the application on port 3000.

```bash
% kubectl create deployment node-app --image=philippaul/node-demo-app:01
deployment.apps/node-app created
% kubectl get deployments
NAME      READY UP-TO-DATE AVAILABLE AGE
node-app  1/1   1          1         9s
% kubectl get pods
NAME                      READY STATUS  RESTARTS AGE
node-app-795677cc99-pzr2z 1/1   Running 0        14s
% kubectl get services
NAME        TYPE      CLUSTER-IP  EXTERNAL-IP PORT(S)   AGE
kubernetes  ClusterIP 10.96.0.1   <none>      443/TCP   10d
% kubectl expose deployment node-app --type=LoadBalancer --port=3000
service/node-app exposed
% minikube service node-app
```

Now, the application is running on port 3000. If for some reason the application stops, we do not have to manually intervene to restart the pods. Kubernetes will automatically restart the pods to keep the application running.

### Scaling the App in K8s

In the above case, when the application is automatically restarted by Kubernetes, it will be down until the restart is complete. To minimize this downtime, we can scale the application.

To scale the number of instances, you can use:

```bash
% kubectl scale deployment node-app --replicas=4
deployment.apps/node-app scaled
% kubectl get pods
NAME                      READY STATUS  RESTARTS AGE
node-app-795677cc99-p2ggw 1/1   Running 0        22s
node-app-795677cc99-pzr2w 1/1   Running 3        28m
node-app-795677cc99-rxvg4 1/1   Running 0        22s
node-app-795677cc99-k2lbw 1/1   Running 0        22s
%
```

To scale down, use the same command with a different number of replicas:

```bash
% kubectl scale deployment node-app --replicas=2
deployment.apps/node-app scaled
% kubectl get pods
NAME                      READY STATUS      RESTARTS AGE
node-app-795677cc99-p2ggw 1/1   Running     4        6m
node-app-795677cc99-pzr2w 1/1   Running     5        34m
node-app-795677cc99-rxvg4 1/1   Terminating 2        6m
node-app-795677cc99-k2lbw 1/1   Terminating 3        6m
```

### YAML Configuration in K8s

To manage applications in Kubernetes, we have used multiple commands and steps. Instead, you can manage most of them using a configuration file, similar to a Docker Compose file.

We will start with a new deployment. Create a YAML file with any name, in this example, we are creating the file in the same folder where the application files are kept.

For syntax reference, refer to the [Official Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/). On the left side, click on the configuration file you want to create. For example, click on `Deployment` to view its example syntax. Copy the syntax into your editor and make changes as needed.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  # Unique key of the Deployment instance
  name: deployment-example
spec:
  # 3 Pods should exist at all times.
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        # Apply this label to Pods and default
        # the Deployment label selector to this value
        app: nginx
    spec:
      containers:
      - name: nginx
        # Run this image
        image: nginx:1.14
```

**Breakdown of the YAML File:**

1. **apiVersion: apps/v1**
   - Specifies the version of the Kubernetes API used to create the Deployment. `apps/v1` is the stable version for managing deployments.

2. **kind: Deployment**
   - Indicates the type of resource being defined. `Deployment` is a higher-level abstraction in Kubernetes that manages the lifecycle of a set of Pods.

3. **metadata:**
   - **name: deployment-example**
     - The name assigned to the Deployment object. It must be unique within the namespace.

4. **spec:**
   - Describes the desired state of the Deployment and its behavior.
   - **replicas: 3**
     - Defines the number of Pod replicas that should be running at any time. Kubernetes will maintain 3 replicas of the Pod.
   - **selector:**
     - Selects the Pods this Deployment should manage. `matchLabels` specifies that Pods with the label `app: nginx` should be managed by this Deployment.
   - **template:**
     - Defines the Pod template used by the Deployment to create Pods.
     - **metadata:**
       - **labels:**
         - Labels (`app: nginx`) are applied to the Pods created by this Deployment. These labels are used by the Deployment to identify the Pods it should manage.
     - **spec:**
       - **containers:**
         - Specifies the container(s) that will run inside each Pod.
         - **name: nginx**
           - The name of the container. Used within the Pod spec to reference this container.
         - **image: nginx:1.14**
           - Specifies the Docker image to use for the container. Here, it’s using the `nginx` image with the tag `1.14`, running Nginx version 1.14.

**Summary**

- **Deployment**: Manages the deployment of Pods in a Kubernetes cluster.
- **Replicas**: Ensures exactly 3 Pods are running at all times.
- **Selector**: Uses labels to identify which Pods to manage.
- **Pod Template**: Defines the Pods’ configuration, including the container image (`nginx:1.14`) and its name (`nginx`).

Edit the reference deployment config file according to your use case.

**deployment-node-app.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  # Unique key of the Deployment instance
  name: my-node-app
spec:
  # 3 Pods should exist at all times.
  replicas: 3
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        # Apply this label to Pods and default
        # the Deployment label selector to this value
        app: node-app
    spec:
      containers:
      - name: node-app
        # Run this image
        image: philippaul/node-demo-app:01
```

> Note: The app name provided in **template:metadata:labels** should be the same as **selector:matchLabels** app name.

To deploy, use `kubectl apply -f <file_name>`:

```bash
% kubectl apply -f deployment-node-app.yml
deployment.apps/my-node-app created
% kubectl get deployments
NAME         READY UP-TO-DATE AVAILABLE  AGE
my-node-app  2/2   2          2          8s
% kubectl get pods
NAME                         READY STATUS  RESTARTS AGE
my-node-app-795678cc99-gw2gw 1/1   Running 0        22s
my-node-app-795678cc99-krbwg 1/1   Running 0        22s
```

Here, we are directly using the YAML file name **deployment-node-app.yml**, assuming it is in the same folder where we are running the command. If it is in a different folder, you need to provide the path to the file as well.

To make changes, edit the YAML file and run `kubectl apply -f <file_name>`. No need to run multiple commands.

Now, create a service config file. Again, refer to the official documentation. On the left side, click on `Service APIs`, then click on `Service`. Click on the `show example` button to get the reference syntax. Copy and edit it according to your requirements.

```yaml
kind: Service
apiVersion: v1
metadata:
  # Unique key of the Service instance
  name: service-example
spec:
  ports:
    # Accept traffic sent to port 80
    - name: http
      port: 80
      targetPort: 80
  selector:
    # Load balance traffic across Pods matching
    # this label selector
    app: nginx
  # Create an HA proxy in the cloud provider
  # with an External IP address - *Only supported
  # by some cloud providers*
  type: LoadBalancer
```

**Breakdown of the YAML File:**

1. **kind: Service**
   - Indicates the type of resource being defined. `Service` provides network access to a set of Pods and handles load balancing.

2. **apiVersion: v1**
   - Specifies the version of the Kubernetes API used to create the Service. `v1` is a stable version supporting core features.

3. **metadata:**
   - **name: service-example**
     - The name assigned to the Service object. It must be unique within the namespace.

4. **spec:**
   - Specifies the desired configuration and behavior

 of the Service.
   - **ports:**
     - Defines the ports the Service will listen on and how it routes traffic to the Pods.
     - **name: http**
       - Optional name for the port configuration.
     - **port: 80**
       - The port the Service listens on. Port 80 is typically used for HTTP.
     - **targetPort: 80**
       - The port on the Pods that the Service will forward traffic to. Traffic sent to port 80 on the Service will be directed to port 80 on the matching Pods.

   - **selector:**
     - Defines how the Service finds which Pods to route traffic to based on their labels.
     - **app: nginx**
       - The label selector ensures the Service routes traffic to Pods with the label `app: nginx`.

   - **type: LoadBalancer**
     - Specifies the Service type.
     - **LoadBalancer**: Requests an external load balancer from a cloud provider and assigns a public IP to the Service. Traffic is distributed across the Pods that match the selector. Note that this is supported only by some cloud providers.

**Summary**

- **Service**: Provides a stable network endpoint to access Pods, with load balancing options.
- **Ports**: Configures how traffic is handled, specifying that the Service listens on port 80 and forwards traffic to port 80 on the Pods.
- **Selector**: Determines which Pods receive the traffic based on their labels (`app: nginx`).
- **Type: LoadBalancer**: Requests an external load balancer from a cloud provider, assigning a public IP to the Service and balancing incoming traffic across the Pods.

Edit the reference service config file according to your use case.

**service-node-app.yml**

```yaml
kind: Service
apiVersion: v1
metadata:
  # Unique key of the Service instance
  name: service-my-node-app
spec:
  ports:
    # Accept traffic sent to port 80
    - name: http
      port: 8080
      targetPort: 3000
  selector:
    # Load balance traffic across Pods matching
    # this label selector
    app: node-app
  # Create an HA proxy in the cloud provider
  # with an External IP address - *Only supported
  # by some cloud providers*
  type: LoadBalancer
```

When creating a service, it checks for Pods under the app `node-app` created using the deployment config. This service will apply to that app.

To create the service, use the same command `kubectl apply -f <file_name>`:

```bash
% kubectl apply -f service-node-app.yml
service/service-my-node-app created
% kubectl get services
NAME                TYPE          CLUSTER-IP      EXTERNAL-IP PORT(S)         AGE
kubernetes          ClusterIP     10.96.0.1       <none>      443/TCP         5d18h
service-my-node-app LoadBalancer  10.109.128.20   <pending>   8080:31062/TCP  7s
% minikube service service-my-node-app
```

To delete the deployment, use:

```bash
% kubectl delete -f deployment-node-app.yml
deployment.apps "my-node-app" deleted
% kubectl get pods
No resources found in the default namespace.
```

### Single K8s Config File

Instead of using two separate config files for **deployment** and **service**, you can use a single config file. Separate the tasks with `---` and use `kubectl apply` on that single file.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nodedb-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodedb-app
  template:
    metadata:
      labels:
        app: nodedb-app
    spec:
      containers:
      - name: nodedb-app
        image: philippaul/node-mongo-db:02
      - name: mongodb
        image: mongo:latest

---

kind: Service
apiVersion: v1
metadata:
  name: service-my-nodedb-app
spec:
  ports:
    - name: http
      port: 8080
      targetPort: 3000
  selector:
    app: nodedb-app
  type: LoadBalancer
```

### Overview of Multi-Container App

We have a simple multi-container application with a WebUI that takes an email address as input from users and stores it in MongoDB. The Node-based WebUI will run in one container, and MongoDB will run in another container.

To deploy this application, pull the image from Docker. Run MongoDB with port 27017 exposed. Once it's up, run the Node app with port 3000 exposed.

```bash
% docker ps -a
% docker pull philippaul/node-mongo-db:01
Digest: sha256:05028c7ed2f15dbdae19141cdfb8b192f7724b2cd970c449a3d4c06a87ad424d
Status: Downloaded newer image for philippaul/node-mongo-db:01
docker.io/philippaul/node-mongo-db:01
% docker run -d -p 27017:27017 --network my-net --name mongo mongo
379afc64c1c34b041cdaf44713a7b8fa4b784e4e8e2b862841a396698f70758e
% docker run --network my-net -p 3000:3000 --name myapp philippaul/node-mongo-db:01
server is running on port 3000
```

This is how the application works and runs locally using Docker. We can use multi-container deployment in Kubernetes for hosting this application.

### Multiple Containers Deployment

There are two ways to run an application with multiple containers:
- Run multiple containers in the same Pod
- Run each container in a separate Pod

#### Run Multiple Containers in the Same Pod

In this example, we will run both the Node WebUI and MongoDB in a single Pod but in different containers. We will need two images for the two containers. We will use YAML config files for this application deployment.

Edit the deployment config file. You need to provide multiple container names and relevant image names in the `spec: containers` section of the deployment file.

**deployment-nodedb-app.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nodedb-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodedb-app
  template:
    metadata:
      labels:
        app: nodedb-app
    spec:
      containers:
      - name: nodedb-app
        image: philippaul/node-mongo-db:02
      - name: mongodb
        image: mongo:latest
```

Edit the service config file. Update the `selector: app` name and the ports that need to be exposed.

**service-nodedb-app.yml**

```yaml
kind: Service
apiVersion: v1
metadata:
  name: service-my-nodedb-app
spec:
  ports:
    - name: http
      port: 8080
      targetPort: 3000
  selector:
    app: nodedb-app
  type: LoadBalancer
```

Now deploy the application using `kubectl`. Once deployed, you can check the status in the `minikube dashboard`.

```bash
% kubectl get deployments
No resources found in the default namespace.
% kubectl apply -f deployment-nodedb-app.yml
deployment.apps/my-nodedb-app created
% kubectl get deployments
NAME           READY UP-TO-DATE AVAILABLE AGE
my-nodedb-app  2/2   2          2         10s
% kubectl apply -f service-nodedb-app.yml
service/service-my-nodedb-app created
% minikube service service-my-nodedb-app
```

The application should now be up and running. Each time the UI loads and a user enters their email, it is stored in MongoDB. However, since we have two replicas, there are two instances of MongoDB. As a result, each time a user enters their email, it may be stored in either the MongoDB instance of the first container or the MongoDB instance of the second container. With each website refresh, the container may change, leading to uncertainty about which MongoDB instance is storing the data. Therefore, using a shared resource like MongoDB in a separate Pod may provide a more reliable solution.

#### Run Each Container in a Separate Pod

In the previous example, both containers were running in a single Pod, allowing them to communicate with each other easily using localhost. When we run each container in separate Pods, we need to establish the connection between the two containers in separate Pods.

Steps:
1. Modify the Node project (dynamic host and port for database URL)
2. Build the image & push the image with version 03
3. YAML config requirement explanation
4. Create deployment and service config for MongoDB to obtain the service name
5. Create deployment and service config for the Node app and explain how to use environment variables in the config
6. Create a ConfigMap file
7. Run the Node app deployment

- Earlier, in the application, in the **index.js** file, we used the static host **localhost** to connect to MongoDB.

**index.js**

```js
// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/yourDatabaseName', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});
```

Now, to use a dynamic host, you need to make use of variables. We have created two variables **const mongoHost** and **const mongoPort**. As we are using environment variables as a reference, we use **process.env.<variable_name>** as a value.

If we don't pass environment variable names, it will use the default value of **localhost** and port **27017**.

**index.js**

```js
const mongoHost = process.env.MONGO_HOST || 'localhost';
const mongoPort = process.env.MONGO_PORT || '27017';

// Connect to MongoDB
mongoose.connect(`mongodb://${mongoHost}:${mongoPort}/yourDatabaseName`, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});
```

- Build the image and push it to Docker Hub.

```bash
% docker build -t philippaul/node-mongo-db:03 .
% docker push philippaul/node-mongo-db:03
```

- Since we are creating two Pods, we require two deployment config files and two service config files.

[Optional] Create a rough requirement note:

```md
Image we need to use:
- philippaul/node-mongo-db:03  # The latest image we pushed to Docker in the previous step
  - node-app.yml    # Single config file, which includes both deployment & service kinds
- mongo             # Default image of MongoDB
  - mongo-db.yml    # Single config file, which includes both deployment & service kinds
```

- Create deployment and service config for MongoDB so we will have a service name. We need the `service-name:port` to establish the connection between POD1 (Node) and POD2 (MongoDB).

**mongo-db.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-app
  template:
    metadata:
      labels:
        app: mongo-app
    spec:
      containers:
      - name: mongo-app
        image: mongo:latest

---

apiVersion: v1
kind: Service
metadata:
  name: service-mongodb
spec:
  selector:
    app: mongo-app
  ports:
    - name: tcp
      port: 27017
      targetPort: 27017
  # type: LoadBalancer
```

Once you create the config file, apply it using `kubectl apply`.

```bash
% kubectl apply -f mongo-db.yml
deployment.apps/mongo-app unchanged
service/service-mongodb created
% kubectl get all
```

- Now create deployment and service config for the Node app.

**node-app.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: philippaul/node-mongo-db:03

---

apiVersion: v1
kind: Service
metadata:
  name: service-node-app
spec:
  selector:
    app: node-app
  ports:
    - name: http
      port: 8080
      targetPort: 3000
  type: LoadBalancer
```

- ConfigMap in K8s

In the project code, for the Database URL, we used environment variables `MONGO_HOST` and `MONGO_PORT` in the **index.js** file for DB hostname and port. The values for these will be: Service Name `service-mongodb` as the hostname and port `27017`.

To provide these values to the project, we need to create a ConfigMap file. We will create this file in the same folder where our other project and config files are present.

**mongo-config.yml**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-config
data:
  MONGO_HOST: "service-mongodb"
  MONGO_PORT: "27017"
```

Now you need to update the **spec: containers** details in the `node-app.yml` config file with **ConfigMapKeyRef** values for environment variables.

**node-app.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: philippaul/node-mongo-db:03
        env:
          - name: MONGO_HOST
            valueFrom:
              configMapKeyRef:
                name: mongo-config     # Name of ConfigMap
                key: MONGO_HOST
          - name: MONGO_PORT
            valueFrom:
              configMapKeyRef:
                name: mongo-config
                key: MONGO_PORT

---

apiVersion: v1
kind: Service
metadata:
  name: service-node-app
spec:
  selector:
    app: node-app
  ports:
    - name: http
      port: 8080
      targetPort: 3000
  type: LoadBalancer
```

Before running the **node-app.yml**, apply the **mongo-config.yml** file.

```bash
% kubectl apply -f mongo-config.yml
configmap/mongo-config created
% kubectl apply -f node-app.yml
deployment.apps/node-app created
service/service-node-app created
% minikube service service-node-app
```

Now both the **mongodb** and **node-app** Pods are running. You should be able to access the WebUI using the provided URL.

### Kubernetes Volumes and Data

In the example above, if we update the version of the MongoDB image, the application will still run, but the new MongoDB image will not have the old data stored in the database. Additionally, if the container is auto-restarted by Kubernetes due to issues, the old data will be lost.

This occurs because data is stored within the container. Each time it boots up with a new image, the old data is lost. By creating a volume inside the Pod and storing the data there instead of within the container, we can also share that volume with multiple containers inside the Pod. However, this might not be a suitable solution if the Pod itself restarts.

To maintain data even when using replicas and multiple Pods, we need to create a volume at the node level, outside the Pod. This allows us to share the volume with multiple Pods. A better option is to use cloud-based volumes.

In our example, we will create a volume at the node level and share it with the Pods.

**Structure:**
Physical Storage > Persistent Volume (PV) > Persistent Volume Claim (PVC) > App

To create a Persistent Volume, first, you need to create a config file. Refer to the [Official Documentation](https://kubernetes.io/docs/concepts/storage/volumes/) for more information.

**host-pv.yml**

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/
    type: DirectoryOrCreate
```

- To check the existing storage class:
```bash
% kubectl get sc
NAME               PROVISIONER              RECLAIMPOLICY VOLUMEBINDINGMODE ALLOWVOLUMEEXPANSION AGE
standard (default) k8s.io/minikube-hostpath Delete        Immediate         false               31d
%
```
- **accessModes:** Determines how you want to use the storage: ReadWriteOnce, ReadOnlyMany, or ReadWriteMany. Choose based on your use case. [Reference](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).

- **hostPath:** Defines where you want to keep this storage and what type of storage you are reserving. Choose based on your use case. [Reference](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#types-of-persistent-volumes).

Once the config to create a Persistent Volume (PV) is ready, create the Persistent Volume Claim (PVC) config file.

**host-pvc.yml**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
spec:
  volumeName: host-pv     # Name of the Persistent Volume
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi      # Request to claim the storage size
```

Now, edit the **mongo-db.yml** config file to add the persistent volume to store the data.

**mongo-db.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-app
  template:
    metadata:
      labels:
        app: mongo-app
    spec:
      containers:
        - name: mongo-app
          image: mongo:latest
          volumeMounts:
            - mountPath: /data/db     # Location where MongoDB stores data
              name: mongo-vol         # Specify which volume to use
      volumes:
        - name: mongo-vol
          persistentVolumeClaim:
            claimName: host-pvc   # Name of the PVC

---

apiVersion: v1
kind: Service
metadata:
  name: service-mongodb
spec:
  selector:
    app: mongo-app
  ports:
    - name: tcp
      port: 27017
      targetPort: 27017
```

Basically, we are mounting `mountPath: /data/db` with the persistent volume `mongo-vol` to store the data.

Now apply all the config files one by one.
```bash
% kubectl apply -f host-pv.yml
persistentvolume/host-pv created
% kubectl get pv
NAME     CAPACITY  ACCESS MODES   RECLAIM POLICY  STATUS     STORAGECLASS REASON AGE
host-pv  1Gi       RWO            Retain          Available  standard            9s
% kubectl apply -f host-pvc.yml
persistentvolumeclaim/host-pvc created
% kubectl get pvc
NAME      STATUS  VOLUME  CAPACITY  ACCESS MODES  STORAGECLASS  AGE
host-pvc  Bound   host-pv 1Gi       RWO           standard      12s
% kubectl apply -f mongo-db.yml
deployment.apps/mongo-app configured 
service/service-mongodb unchanged
```
To check if this persistent volume is working, access the WebUI of the application and enter some email IDs. These will be stored in MongoDB at the `/data/db` path, which is mounted to the persistent volume `mongo-vol`.

**Testing 1:** Now, if we stop the Pods or delete the MongoDB deployment and redeploy it, it should start with a new MongoDB image but should restore the data from the persistent volume.

```bash
% kubectl get deployments
NAME      READY UP-TO-DATE AVAILABLE AGE
mongo-app 1/1   1          1         2d5h
node-app  2/2   2          2         19d
% kubectl delete deployment mongo-app
deployment.apps "mongo-app" deleted
% kubectl apply -f mongo-db.yml
deployment.apps/mongo-app created 
service/service-mongodb unchanged
```
All the data should now be restored from the persistent volumes.

**Testing 2:** If we bring down the entire Minikube cluster and start it again, the application should still be able to restore the data from the persistent volume.

```bash
% minikube status
minikube
type: Control Plane 
host: Running
kubelet: Running 
apiserver: Running 
kubeconfig: Configured
% minikube stop
Stopping node "minikube" ...
Powering off "minikube" via SSH ... 
node stopped.
% # Now, the dashboard and application should be completely down as the whole cluster is stopped.
% minikube start
% minikube service service-node-app                   # Service config in node-app.yml
```
The Kubernetes dashboard should now be accessible, and the application should be up and running with all the old data restored from the persistent volume.