# XinFin-K8S

Pull  image on your host(s)
```
sudo docker pull xinfinorg/quorum:istanbul-tools-k8s
```

Create genesis as ConfigMap
```
kubectl create -f genesis-configmap.yaml
```

Create nodekeys as secret
```
kubectl create -f secret-minernode.yaml
```

Pre-provision PV for our nodes to store chaindata (hostpath)
```
kubectl create -f geth-persistentvolume.yaml
```

Deploy bootnode
```
kubectl create -f bootnode-deployment.yaml
```
Copy bootnode POD_IP using Dashboard or CLI

```
kubectl proxy
```

Copy bootnode POD_IP address & update enode address of --bootnodes argument in minernode-statefulset.yaml & membernode-statefulset.yaml (Can be improved to automatically fetch bootnode IP)

Launch minernodes (replicas: 2)
```
kubectl create -f minernode-statefulset.yaml
```

Launch membernode (replicas: 1)

```
kubectl create -f membernode-statefulset.yaml
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
kubectl create -f constellation-bootnode-deployment.yaml
```
