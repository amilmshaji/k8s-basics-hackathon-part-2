

# Kubernetes hackathon part-2

## Prerequisites
- Kubectl installation
	- https://kubernetes.io/docs/tasks/tools/
- Minikube installation
	- https://minikube.sigs.k8s.io/docs/start/
- Start the cluster using 
	 ```minikube start```
- Ensure minikube cluster context is set in **kubectl** command


## Task 1
Create a namespace `my-namespace` in your cluster
```
➜  TASK kubectl create namespace my-namespace
namespace/my-namespace created
```

## Task 2
Create a Pod with name `nginx` with images `nginx:latest`, `grafana/grafana-oss` in `my-namespace` using yaml configurations.

Create a YAML for a Pod with multiple containers with container names `first`, `second` for the given images


Copy the YAML content below
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: my-namespace
spec:
  containers:
  - name: first
    image: nginx:latest
    ports:
    - containerPort: 8080
  - name: second
    image: grafana/grafana-oss
    ports:
    - containerPort: 8082
```

List all the pods in the namespace `my-namespace` using `kubectl get pods -n <namespace>` command
```
pod/nginx created
➜  TASK kubectl get pods -n my-namespace
NAME    READY   STATUS              RESTARTS   AGE
nginx   0/2     ContainerCreating   0          72s

```

## Task 3

List all the logs of pod `nginx` running in `my-namespace`  using `kubectl logs` command

Refference: 
https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs

get the logs for container `first` in `nginx` pod

```
➜  TASK  kubectl logs pods/nginx -c first -n my-namespace
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/03/28 11:42:02 [notice] 1#1: using the "epoll" event method
2024/03/28 11:42:02 [notice] 1#1: nginx/1.25.4
2024/03/28 11:42:02 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/03/28 11:42:02 [notice] 1#1: OS: Linux 6.6.12-linuxkit
2024/03/28 11:42:02 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/03/28 11:42:02 [notice] 1#1: start worker processes
2024/03/28 11:42:02 [notice] 1#1: start worker process 29
2024/03/28 11:42:02 [notice] 1#1: start worker process 30
2024/03/28 11:42:02 [notice] 1#1: start worker process 31
2024/03/28 11:42:02 [notice] 1#1: start worker process 32
2024/03/28 11:42:02 [notice] 1#1: start worker process 33
2024/03/28 11:42:02 [notice] 1#1: start worker process 34
2024/03/28 11:42:02 [notice] 1#1: start worker process 35
2024/03/28 11:42:02 [notice] 1#1: start worker process 36

```

get the logs for container `second` in `nginx` pod

```
➜  TASK kubectl logs pods/nginx -c second -n my-namespace
logger=settings t=2024-03-28T11:43:14.238114843Z level=info msg="Starting Grafana" version=10.4.1 commit=d94d597d847c05085542c29dfad6b3f469cc77e1 branch=v10.4.x compiled=2024-03-28T11:43:14Z
logger=settings t=2024-03-28T11:43:14.238335801Z level=info msg="Config loaded from" file=/usr/share/grafana/conf/defaults.ini
logger=settings t=2024-03-28T11:43:14.238349051Z level=info msg="Config loaded from" file=/etc/grafana/grafana.ini
logger=settings t=2024-03-28T11:43:14.238351218Z level=info msg="Config overridden from command line" arg="default.paths.data=/var/lib/grafana"
logger=settings t=2024-03-28T11:43:14.23835276Z level=info msg="Config overridden from command line" arg="default.paths.logs=/var/log/grafana"
logger=settings t=2024-03-28T11:43:14.238354301Z level=info msg="Config overridden from command line" arg="default.paths.plugins=/var/lib/grafana/plugins"
logger=settings t=2024-03-28T11:43:14.238355718Z level=info msg="Config overridden from command line" arg="default.paths.provisioning=/etc/grafana/provisioning"
logger=settings t=2024-03-28T11:43:14.23835751Z level=info msg="Config overridden from command line" arg="default.log.mode=console"
logger=settings t=2024-03-28T11:43:14.238359301Z level=info msg="Config overridden from Environment variable" var="GF_PATHS_DATA=/var/lib/grafana"

```

## Task 4
Delete the nginx pod
```
kubectl delete -f "nginx_pod.yaml"                                   
pod "nginx" deleted
pod "nginx" deleted
```

## Task 5
Create a replicaset named `nginx-replicaset` for image `nginx:1.42.6` with 3 replicas in the namespace `my-namespace` using YAML configurations

1. Copy the YAML content below
	```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: my-namespace
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
        image: nginx:1.42.6
        ports:
        - containerPort: 80


	```

2. Apply the yaml configurations using `kubectl apply -f <yaml file>`

3. List the replicaset in the namespace `my-namespace` using `kubectl get replicaset -n <namespace>`
	```
TASK kubectl apply -f nginx_replicasets.yaml
deployment.apps/nginx-deployment created

	```

4. List the pods in the namespace `my-namespace` 
	```
➜  TASK kubectl get replicaset -n my-namespace
NAME                         DESIRED   CURRENT   READY   AGE
nginx-deployment-b5cb4c4fc   3         3         0       112s

	```

5. Find out why the pods are failing in the `nginx-replicaset` replicaset 
	```
	<Explain how you found the issue>

	```

6. Fix the issue in the YAML configuration created  and re-apply configuration using `kubectl apply`

	List the pods in the namespace `my-namespace` 
	```
	<Paste output here>

	``

7. Do a `kubectl delete -f <file-name>` and apply it again using `kubectl apply -f <file-name>`

	List the pods in the namespace `my-namespace` 
	```
	<Paste output here>

	```

8. Explain your observations from steps 6,7

	```
	<Type observation here>

	```


9. Increase/dicrease the count of replicas by 1 in the YAML and apply it again 

	List the pods in the namespace `my-namespace` 
	```
	<Paste output here>
	```

10. Delete the `nginx-replicaset` using `kubectl delete replicaset/<replicaset-name> -n <namespace>`

## Task 6
Create a deployment named `nginx-deployment` for image `nginx:1.42.6` with 3 replicas in the namespace `my-namespace` using YAML configurations

1. Copy the YAML content below
	```
	<Paste output here>

	```

2. Apply the yaml configurations using `kubectl apply -f <yaml file>`

3. List the deployments in the namespace `my-namespace` using `kubectl get deployments -n <namespace>`
	```
	<Paste output here>

	```
4. List the replicasets in the namespace `my-namespace` using `kubectl get replicaset -n <namespace>`
	```
	<Paste output here>

	```

5. List the pods in the namespace `my-namespace` 
	```
	<Paste output here>

	```

6. Change the image in YAML to `nginx:1.25.4` and re-apply the yaml using `kubectl apply -f <file-name>`

	```
	<Paste output of kubectl apply here>

	```
	List the pods in the namespace `my-namespace` 
	```
	<Paste output here>

	```
	  List the replicaset in the namespace `my-namespace` 
	```
	<Paste output here>

	```
7. Increase/dicrease the count of replicas by 1 in the YAML and apply it again 

	List the pods in the namespace `my-namespace` 
	```
	<Paste output here>

	```

8. Compare the effort while updating the images in replicaset and deployment
	```
	Your observation and comparison between 
	a replicaset and deployment

	```
9.  Delete the deployment  `nginx-deployment` using kubectl delete command

## Bonus task

Apply the given deployment.yaml file using `kubectl apply -f`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: my-namespace
spec:
  replicas: 1
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
	image: nginx:latest
	ports:
	- containerPort: 80
	readinessProbe:
	  httpGet:
	    path: /
	    port: 82
	livenessProbe:
	  httpGet:
	    path: /
	    port: 82
```

1. List the pods for deployment
	```
	<Paste output here>

	```
2. Findout why the pods are not ready
	```
	<Explaing the steps you have done to do the debugging>
	
	```
3. Fix the issue and explain your understanding on  `readinessProbe` `livenessProbe`
	
	```
	<Updated deployment yaml>

	```
	Understanding on readness and liveness probe
	```
	<Type your understanding here>

	```

## Cleanup task
Delete the namespace `my-namespace` 
