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

### Step 3: Validating - test the load balancing is working

3.1 Access App v1 or v2 in round-robin manner with k8s cluster
- kubectl get svc | grep demo-nginx-svc
- curl -s "http://{{cluster ip address}}:9080" (e.g. curl -s "http://10.233.46.10:9080")  
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown alternatively.


3.2 test virtual svc - from nodeport   
kubectl get po -l istio=ingressgateway -n istio-system -o json | grep hostIP   
kubectl -n istio-system get service istio-ingressgateway -o json | grep http2   

//out from cluster   
curl -s "http://192.168.222.145:31087/"     
