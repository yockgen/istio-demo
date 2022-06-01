# istio-demo
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
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown alternatively.


4.2 Access App v1 or v2 in round-robin manner outside k8s cluster via Istio Ingress Gateway

- kubectl get po -l istio=ingressgateway -n istio-system -o json | grep hostIP
  - Getting Ingress Host IP     
- kubectl -n istio-system get service istio-ingressgateway -o json | grep http2     
  - Getting Ingress Port  
-  curl -s "http://{{Ingress Host IP}}:{{Ingress Port}}/"   
   - e.g. curl -s "http://192.168.222.145:31087/"       
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown alternatively.
- Getting Ingress Host IP and Port is varies depend on cluster environment, more info https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/#determining-the-ingress-ip-and-ports
