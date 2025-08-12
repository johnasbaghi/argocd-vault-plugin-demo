### Install the AVP sidecar using the token authentication method:

```bash
export VAULT_TOKEN=""

envsubst < secret.yaml | kubectl apply -f -

kubectl apply -f cm.yaml -n argocd

kubectl patch deployment -n argocd argocd-repo-server --patch-file ./patch.yaml
```

### Uninstall the AVP sidecar using the following patch

```bash
kubectl patch deployment -n argocd argocd-repo-server --patch-file ./remove-patch.yaml
```
