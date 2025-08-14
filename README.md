[Install ArgoCD, Vault and MetalLB](./argocd/README.md)

[Install AVP](./avp/README.md)

# Create a secret

```bash
$ brew install vault

$ export VAULT_ADDR=http://172.18.0.9:8200

$ export VAULT_TOKEN=root

$ vault kv put secret/secret1 key1="val1"

$ vault kv get secret/secret1
```

# Test AVP locally

```bash
$ brew install argocd-vault-plugin

$ cat app/secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: some-secret
  annotations:
    avp.kubernetes.io/path: "secret/data/secret1"
stringData:
  SOME_SECRET: <key1>

$ argocd-vault-plugin generate ./app/secret.yaml
apiVersion: v1
kind: Secret
metadata:
  annotations:
    avp.kubernetes.io/path: secret/data/secret1
  name: some-secret
stringData:
  SOME_SECRET: val1
```

# Test AVP by pushing this repo to a Git forge and creating an Application using app as your path, or by running the following

```bash
kubectl apply -n argocd -f app.yaml
```

# Debugging

```
kubectl exec -it -n argocd argocd-repo-server-7fd65c7877-nwwg7 -- /bin/bash

```


