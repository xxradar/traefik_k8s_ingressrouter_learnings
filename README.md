# Traefik ingressroute learnings

Clone this repo to use the examples outlined. <br>
Information is based on 
- https://doc.traefik.io/traefik/reference/dynamic-configuration/kubernetes-crd/ 
- https://github.com/xxradar

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
Create a LoadBalancing service
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
Create a user and password to authenticate the dashboard
```
AUTH=$(htpasswd -nb admin Traefik | openssl base64)
```
Store the user and passwork in a K8S secret
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: dashboard-authsecret
  namespace: default

data:
  users: |2
    $AUTH
EOF
```
Next create a Traefik middleware the invoke the authentication
```
kubectl apply -f - <<EOF
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: dashboard-auth
spec:
  basicAuth:
    realm: MyRealm
    headerField: X-WebAuth-User
    secret: dashboard-authsecret
EOF
```
Create a Traefik Middleware to redirect from http to https
```
kubectl apply -f - <<EOF
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: dashboard-redirectscheme
spec:
  redirectScheme:
    scheme: https
    permanent: false
EOF
```
Apply the http ingressroute
```
kubectl apply -f 09_dashboard-http-ingressroute.yaml
```
Apply the https ingressroute
```
kubectl apply -f 10_dashboard_https_ingressroute.yaml
```

### Adding and playing with middlewares
Create a new middleware
```
kubectl apply -f - <<EOF
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: testheader
spec:
  headers:
    customRequestHeaders:
      X-Script-Name: "test"
    customResponseHeaders:
      X-Custom-Response-Header: "value"
EOF
```
