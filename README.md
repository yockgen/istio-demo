# Istio Demo
Simple demo App for Istio - leading service mesh for K8s, the demo consist of two nginx application (called as v1 and v2, to simulate different version of N application), use Istio features to control routing of external traffic to v1 or v2 application (load balancing, only to specific service, traffic shifting etc). 

### Prerequisite 
In order to run the demo, user should have already: 
1. Configured a working K8s cluster (cloud, barem-metal or VM)
2. Installed Istio (https://istio.io/latest/docs/setup/getting-started/)

### Step 1: Deploy Sample Application 
kubectl apply -f demo-deployment.yaml

### Step 2: Deploy Istio Gateway and VirtualServices 
kubectl apply -f demo-destination-rule.yaml
kubectl apply -f demo-gateway.yaml

### Step 3: verifying Istio gateway is configured
istioctl analyze

### Step 4: Validating 

4.1 Access App v1 or v2 in round-robin manner within k8s cluster via Kubernetes service
- export SVC_CLUSTER_IP=$(kubectl get svc demo-nginx-svc -o jsonpath='{.spec.clusterIP}')  
- export SVC_CLUSTER_PORT=$(kubectl get svc demo-nginx-svc -o jsonpath='{.spec.ports[?(@.name=="http")].port}')  
- curl -s "http://$SVC_CLUSTER_IP:$SVC_CLUSTER_PORT" 
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown randomly.


4.2 Access App v1 or v2 in round-robin manner outside k8s cluster via Istio Ingress Gateway

- export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')  
- export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')  
- curl -s "http://$INGRESS_HOST:$INGRESS_PORT/"   
- Calling the curl multiple times, user should noticed that the v1 and v2 application text shown randomly.
- Getting Ingress Host IP and Port is varies depend on cluster environment, more info https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/#determining-the-ingress-ip-and-ports

## To play with Istio features
- Modifying VirtualService in demo-gateway.yaml 
- kubectl apply -f demo-gateway.yaml 
- curl -s "http://$INGRESS_HOST:$INGRESS_PORT/"
   - Through k8s clusterIP IP and port will not see the effects.   

### Route only to V2 app
```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: demo-nginx-vs
spec:
    .....   
    - destination:
        host: demo-nginx-svc
        port:
          number: 9080
        subset: v2
     ....
```
### Route 90% of traffic to V2 app, and 10% to V1 app
```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: demo-nginx-vs
spec:
    .....   
     route:
    - destination:
        host: demo-nginx-svc
        port:
          number: 9080
        subset: v1
      weight: 10
    - destination:
        host: demo-nginx-svc
        port:
          number: 9080
        subset: v2
      weight: 90
     ....
```

## Cleanup  
kubectl delete -f demo-gateway.yaml  
kubectl delete -f demo-deployment.yaml   
