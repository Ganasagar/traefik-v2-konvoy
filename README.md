# traefik-v2-konvoy


### Step 1 
 Follow the offical helm install guides however replace the values files with the one listed here
### Step 2
 If you review the values file and add custom ports as needed for your applications 

### Step 3 
 Install the Traefik 2.0 

### Step 4 
 Validate that you see your custom port being exposed by describing the Traefik pod -- check the entrypoints section.

 ```bash
 kubectl describe pod/<traefik-pod> 
 ```

Output:
```
❯ k describe po traefik2-5dd884d4b7-xcdjn
Name:         traefik2-5dd884d4b7-xcdjn
Namespace:    default
Priority:     0
Node:         ip-10-0-128-182.us-west-2.compute.internal/10.0.128.182
Start Time:   Tue, 13 Jul 2021 17:31:31 -0400
Labels:       app.kubernetes.io/instance=traefik2
              app.kubernetes.io/managed-by=Helm
              app.kubernetes.io/name=traefik
              helm.sh/chart=traefik-10.0.0
              pod-template-hash=5dd884d4b7
Annotations:  cni.projectcalico.org/podIP: 192.168.14.176/32
              cni.projectcalico.org/podIPs: 192.168.14.176/32
Status:       Running
IP:           192.168.14.176
IPs:
  IP:           192.168.14.176
Controlled By:  ReplicaSet/traefik2-5dd884d4b7
Containers:
  traefik2:
    Container ID:  containerd://c2795b8326af3f36771b5fefd34abead842e25d2898d550cbb3fa0a147c47bcc
    Image:         traefik:2.4.9
    Image ID:      docker.io/library/traefik@sha256:be23e1f6e64abd4adf70a61b8b9fa0844e4795a4a7b3055174106a957eddbf32
    Ports:         3000/TCP, 9000/TCP, 8000/TCP, 8443/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP, 0/TCP
    Args:
      --global.checknewversion
      --global.sendanonymoususage
      --entryPoints.custom-tcp.address=:3000/tcp
      --entryPoints.traefik.address=:9000/tcp
      --entryPoints.web.address=:8000/tcp
      --entryPoints.websecure.address=:8443/tcp
      --api.dashboard=true
      --ping=true
      --providers.kubernetescrd
      --providers.kubernetesingress
      --providers.kubernetesingress.ingressclass=traefik-internal
      --log.level=DEBUG
...
....
......
.......      
```
##### NOTE We are exposing port 3000 here with the entrypoint name called `custom-tcp`

### Step 5 

Deploy and Expose your application with the desired port (entryport). 
for example, lets deploy Grafana

```bash
helm repo add grafana https://grafana.github.io/helm-charts
```
```bash
helm repo update
```

```bash
helm install custom-grafana grafana/grafana
```

### Step 6 
Deploy the inggress TCP route by creating the following repo 
```bash
kubectl create -f tcp-route.yaml
```

### Step 7
Hit the Traefik2 LB endpoint and out custom port to access the application 
```bash
http://LB-hostname/IP:3000
```


