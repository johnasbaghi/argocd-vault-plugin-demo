export VAULT_TOKEN=""

envsubst < secret.yaml | kubectl apply -f -

kubectl patch deployment -n argocd argocd-repo-server --patch-file ./patch.yaml

