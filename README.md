[Install ArgoCD, Vault and MetalLB](./argocd/README.md)

[Install AVP](./avp/README.md)

Create a secret:

```bash
export VAULT_ADDR=http://172.18.0.9:8200
export VAULT_TOKEN=root
vault kv put secret/secret1 key1="val1"
vault kv get secret/secret1
```

Test AVP by pushing this repo to a Git forge and creating an Application using app as your path, or by:

```bash
kubectl apply -n argocd -f app.yaml
```



