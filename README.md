# Setup
- connect to azure
- select azure subscription
- create resource group
- create AKS
- create public ip
- get AKS credentials

```bash
az login
az account list -o table
az account set -s <subscription id>

az group create -n aksdemo -l germanywestcentral

az aks create -g aksdemo -n cbu --node-count 1 -l 'germanywestcentral' --os-sku 'AzureLinux' --node-vm-size 'Standard_D4s_v5' --network-dataplane cilium --network-plugin azure --max-pods 100 --kubernetes-version '1.29' --generate-ssh-keys

az network public-ip create --resource-group aksdemo --name ingresspip --version IPv4 --sku Standard

az aks get-credentials -n 'cbu' -g 'aksdemo'
```

# ArgoCD
Install ArgoCD

```pwsh
helm repo add argocd https://argoproj.github.io/argo-helm
helm repo update
helm upgrade --install argocd argocd/argo-cd -n argocd --create-namespace
```
Create port forwarding:
```pwsh
kubectl port-forward service/argocd-server -n argocd 8080:443
```

Get the password:
```pwsh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d | clip
```

## Connect Repo
- Fork Repo [https://github.com/chrburmeister/gitops-workshop](https://github.com/chrburmeister/gitops-workshop)
- Connect ArgoCD to your repository
