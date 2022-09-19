# azure-aks-namespace-to-nodepool
Azure AKS setup where namespaces are mapped to node pools


## Prepare cluster

```
az group create --name nptest --location westeurope

az aks create -g nptest -n nptest --enable-managed-identity --node-count 1  --generate-ssh-keys

```

## Create nodepools
```
az aks nodepool add --cluster-name nptest --name unit1 --resource-group nptest --labels unit=1

az aks nodepool add --cluster-name nptest --name unit2 --resource-group nptest --labels unit=2

```

## Deploy the namespace admissions

```
kubectl apply -f admission.yml

```

## Deploy app 1

```
kubectl apply -f deployment1.yml

```
Check that the deployments are ready:
```
kubectl get pods -n unit1
```
Check that the Pods run on correct node pool
```
kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name -n unit1
```
Name of the nodes should contain string 'unit1'

Something like this:
```
NODE                            NAME
aks-unit1-31156180-vmss000002   nginx-deployment-unit1-9456bbbf9-bdvgh
aks-unit1-31156180-vmss000001   nginx-deployment-unit1-9456bbbf9-cktzl
aks-unit1-31156180-vmss000000   nginx-deployment-unit1-9456bbbf9-xzg78
```

## Deploy app 2
```
kubectl apply -f deployment2.yml

```
Check that the deployments are ready:
```
kubectl get pods -n unit2
```
Check that the Pods run on correct node pool
```
kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name -n unit2
```
Name of the nodes should contain string 'unit2'