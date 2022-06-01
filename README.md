# istio-demo
Simple demo App for Istio

### Prerequisite 
In order to run the demo, user should have already: 
1. Configured a working K8s cluster (cloud, barem-metal or VM)
2. Installed Istio (https://istio.io/latest/docs/setup/getting-started/)

### Step 1: Deploy Sample Application 
kubectl apply -f demo-deployment.yaml

### Step 2: Deploy Istio Gateway and VirtualServices 
kubectl apply -f demo-deployment.yaml

