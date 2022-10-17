# create the k3d cluster

```
k3d cluster create local-k8s --servers 1 --agents 3 --k3s-arg "--disable=traefik@server:0" --wait
kubectl get nodes

```


# Deploy the Load Balancer:

docker ps
docker network list

```
NETWORK ID     NAME                                        DRIVER    SCOPE
cf43b886dac9   3deployby-docker-compose_default            bridge    local
c100cb8dd5c3   bridge                                      bridge    local
f0d8a94d41e1   deploy-django-with-docker-compose_default   bridge    local
226648dc6fda   djangodockercomposedeploy_default           bridge    local
f5a59f125dfe   host                                        host      local
14a2ccdad8ad   k3d-k3s-default                             bridge    local
31b2b9719ebd   kind                                        bridge    local
fcfa5f8cf5b2   none                                        null      local


```

### This is the  network list

k3d-k3s-default



```
docker network inspect k3d-k3s-default
sudo apt  install jq
docker network inspect k3d-k3s-default | jq

```




   "Subnet": "172.22.0.0/16",
          "Gateway": "172.22.0.1"
        }




```
cidr_block=$(docker network inspect k3d-k3s-default | jq '.[0].IPAM.Config[0].Subnet' | tr -d '"')
echo $cidr_block
sudo apt install sipcalc
sipcalc $cidr_block

```

#  create env-variaable

ingress_range=172.22.255.0-172.22.255.254


# deploy metallb
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml

# configure metallb ingress address range


```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - $ingress_range
EOF


```


```
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=LoadBalancer


```




































