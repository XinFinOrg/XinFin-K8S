# XinFin-K8S

Prerequisite
- Kubernetes Cluster (e.g 1 Master, 2 Workers) - [Tutorial - Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-1-10-cluster-using-kubeadm-on-ubuntu-16-04)
- Docker

Pull  image on worker node(s)
```
sudo docker pull xinfinorg/quorum:istanbul-tools-k8s
```

#### Clone repository & execute the following:

Create genesis as ConfigMap
```
kubectl create -f XinFin-K8S/genesis-configmap.yaml
```

Create nodekeys as secret (base64 encoded)
```
kubectl create -f XinFin-K8S/secret-minernode.yaml
```

Pre-provision volumes for our nodes to store chaindata (hostpath)
```
kubectl create -f XinFin-K8S/geth-persistentvolume.yaml
```

Deploy bootnode
```
kubectl create -f XinFin-K8S/bootnode-deployment.yaml
```
Copy bootnode POD_IP using Dashboard or CLI

```
kubectl proxy
```

Copy bootnode POD_IP address & update enode address of --bootnodes argument in minernode-statefulset.yaml & membernode-statefulset.yaml (Can be improved to automatically fetch bootnode IP)

Launch minernodes (replicas: 2)
```
kubectl create -f XinFin-K8S/minernode-statefulset.yaml
```

Launch membernode (replicas: 1)

```
kubectl create -f XinFin-K8S/membernode-statefulset.yaml
```

We have defined 4 validators in extradata field so we need to scale the minernode(s) to start mining.


# Scaling 
Minernode
```
kubectl scale statefulset minernode --replicas=4
```
Membernode
```
kubectl scale statefulset membernode --replicas=2
```

Deploy constellation-bootnode which can be used for autodiscovery of other constellation nodes.
```
kubectl create -f XinFin-K8S/constellation-bootnode-deployment.yaml
```

# Accessing Geth console 
```
kubectl port-forward minernode-0 8545:8545
```

```
geth attach http://localhost:8545
```

To stop the network use "delete" instead of "create". 
(Please note launching a clean network would require manual cleanup of content in /data directory on worker hosts)


