# Istio Demo
Simple demo App for Istio -Leading Service Mesh

### Prerequisite 
In order to run the demo, user should have already: 
1. Configured a working K8s cluster (cloud, barem-metal or VM)
2. Installed Istio (https://istio.io/latest/docs/setup/getting-started/)

### Step 1: Deploy Sample Application 
kubectl apply -f demo-deployment.yaml

### Step 2: Deploy Istio Gateway and VirtualServices 
kubectl apply -f demo-gateway.yaml

### Step 3: verifying Istio gateway is configured
istioctl analyze

### Step 4: Validating the load balancing is working for this demo

4.1 Access App v1 or v2 in round-robin manner within k8s cluster via Kubernetes service
- kubectl get svc | grep demo-nginx-svc
- curl -s "http://{{cluster ip address}}:9080" 
  - e.g. curl -s "http://10.233.46.10:9080"  
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown randomly.


4.2 Access App v1 or v2 in round-robin manner outside k8s cluster via Istio Ingress Gateway

- export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')  
- export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')  
- curl -s "http://$INGRESS_HOST:$INGRESS_PORT/"   
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown randomly.
- Getting Ingress Host IP and Port is varies depend on cluster environment, more info https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/#determining-the-ingress-ip-and-ports

### Cleanup  
kubectl delete -f demo-gateway.yaml  
kubectl delete -f demo-deployment.yaml   
