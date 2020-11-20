# Traefik ingressroute learnings

Clone this repo to use the examples outlined. <br>
Information is based on 
- https://doc.traefik.io/traefik/reference/dynamic-configuration/kubernetes-crd/ 
- https://blog.wescale.fr/2020/03/06/traefik-2-reverse-proxy-dans-kubernetes/

### Create a working Traefik environmet in K8S
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
Apply an Ingressroute with traefik. Make sure the hostname resolves to the IP address of Traefik in order Let's Encrypt to work.
```
kubectl apply -n app-routable-demo -f 06_traefik_ingress_example.yaml
```
### Secure the Traefik management dashboard
```

```
