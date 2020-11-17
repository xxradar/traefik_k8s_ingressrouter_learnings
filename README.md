# traefik_ingress

This is work in progress.
Updated soon
```
git clone https://github.com/xxradar/traefik_ingress.git
```
Install the CRD
```
kubectl apply -f 00_traefik_crd.yaml
```
Create RBAC, ClusterRole and SA (in default NS)
```
kubectl apply -f 01_traefik_rbac.yaml
```
A generic way to provide certificate storage
```
kubectl apply -f 02_traefik_storage.yaml
```
This currently deploys only one traefik pod with HostNetworking
```
kubectl apply -f 04_traefik_deploy.yaml
```
This is not working because of how DigitalOceans works w/ LB ---- need fix   
```
kubectl apply -f 05_traefik_service.yaml
```
Deploy a sample app
```
git clone https://github.com/xxradar/app_routable_demo.git
./setup 
```
Apply an Ingressroute with traefik
```
kubectl apply -n app-routable-demo -f 06_traefik_ingress_example.yaml
```
