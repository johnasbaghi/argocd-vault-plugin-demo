# Set up ArgoCD, Vault and MetalLB (an external load balancer)

## Install MetalLB

```bash
$ kubectl apply -f metallb-native.yaml

$ kubectl get pods -n metallb-system
NAME                          READY   STATUS    RESTARTS   AGE
controller-865d8c9c64-7lf95   1/1     Running   0          17s
speaker-5j25q                 1/1     Running   0          17s
speaker-f7lbn                 1/1     Running   0          17s
speaker-tgmvm                 1/1     Running   0          17s
speaker-xssnc                 1/1     Running   0          17s

$ docker inspect kind | jq .[].IPAM.Config
[
  {
    "Subnet": "fc00:f853:ccd:e793::/64"
  },
  {
    "Subnet": "172.18.0.0/16",
    "Gateway": "172.18.0.1"
  }
]

$ kubectl apply -f metallb.yaml
```

## Install ArgoCD

```bash
$ kubectl create namespace argocd

# $ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
$ kubectl apply -n argocd -f ./install.yaml

$ k get pods -n argocd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     0/1     Running   0          64s
argocd-applicationset-controller-655cc58ff8-vrlzv   1/1     Running   0          64s
argocd-dex-server-7d9dfb4fb8-hfgb8                  1/1     Running   0          64s
argocd-notifications-controller-6c6848bc4c-tpf9s    1/1     Running   0          64s
argocd-redis-656c79549c-vqktd                       1/1     Running   0          64s
argocd-repo-server-856b768fd9-gkrwj                 0/1     Running   0          64s
argocd-server-99c485944-gftj7                       0/1     Running   0          64s

$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

$ kubectl patch service argocd-server -n argocd --patch '{ "spec": { "type": "LoadBalancer", "loadBalancerIP": "172.18.255.8" } }'

$ kubectl describe svc argocd-server -n argocd

$ kubectl get svc -n argocd
```

## Install Vault in dev mode, with the UI enabled, and exposed via a LoadBalancer service

```bash
$ kubectl create ns vault

# helm repo add hashicorp https://helm.releases.hashicorp.com

# helm pull hashicorp/vault --untar

$ helm install vault \
       --set='server.dev.enabled=true' \
       --set='ui.enabled=true' \
       --set='ui.serviceType=LoadBalancer' \
       --namespace vault \
       ./vault-chart

```

Note: Running Vault in "dev" mode requires no setup, state management
or initialization, but all data will be lost on restart. It's useful
for experimenting with Vault without needing to unseal, store keys,
et. al.

## Access Vault UI using the external IP of the loadbalancer

```bash
$ kubectl get svc -n vault
NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)             AGE
vault                      ClusterIP      10.96.11.74     <none>         8200/TCP,8201/TCP   8h
vault-agent-injector-svc   ClusterIP      10.96.180.83    <none>         443/TCP             8h
vault-internal             ClusterIP      None            <none>         8200/TCP,8201/TCP   8h
vault-ui                   LoadBalancer   10.96.192.117   172.18.255.1   8200:31434/TCP      8h
```

http://172.18.255.1:8200/ui/vault/auth

Note: The default token is root
