# All commands used in the video

### Create deployment first-deploy
```bash
kubectl create deployment first-deploy --image=nginx -r=3
```

### Create service exposing the deployment
```bash
kubectl expose deployment/first-deploy --port=80 --target-port=80
```

## Tierd app, nginx and nodejs app

### Creating nodejs app deployment
```bash
kubectl create deployment second-deploy --image=letslearnkubernetes/nodejs-helloworld-hostname:2022011001 -r=3
```

### Creating service for the second-deploy
```bash
kubectl expose deployment second-deploy --name=frontend --port=80 --target-port=3000
```

## Nginx proxy 

### Create nginx config-map

```bash
kubectl apply -f nginx-configmap.yaml
```

### Create nginx deployment

```bash
kubectl apply -f nginx-deployment.yaml
```

### Check ig nginx is working

```bash
kubectl port-forward deployment/llk-nginx-deployment 8888:80
```

### Create Service with node port

```bash
kubectl expose deployment llk-nginx-deployment --port=80 --target-port=80 --name=proxy-svc-np --type=NodePort
```

### Get nodeport of service

```
kubectl get svc proxy-svc-np -o jsonpath='{.spec.ports[*].nodePort}'
```
or
```
kubectl get svc proxy-svc-np -o yaml | grep nodePort
```

### Get IP address of nodes

```
kubectl get nodes -o jsonpath={.items[*].status.addresses[?\(@.type==\"InternalIP\"\)].address}
```
or
```
kubectl get nodes -o wide
```

Once you have ipaddress and node port, use curl

```
curl <IP ADDRESS>:<Node Port>
```

### Create eksctl cluster

```
eksctl create cluster -f eksctl-config.yaml
```

### Create external Loadbalancer

```
kubectl expose deployment llk-nginx-deployment --port=80 --target-port=80 --name=nginx-prox-lb --type=LoadBalancer
```
### headless service 

```
kubectl expose deployment first-deploy --port=80 --target-port=80 --name=first-svc-headless --cluster-ip='None'
```
use 
```
kubectl exec -it <pod-name> -- bash 
```
and run insode the pod
```
apt update; apt install dnsutils -y
host first-svc-headless
```

### For multi-port Load balancer service
Look into nginx-svc-lb.yaml for all the changes made

### Custom endpoint for svc
Create service using
```
kubectl apply -f mysql-svc-external-endpoint.yaml
```
