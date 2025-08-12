export VAULT_TOKEN=""

envsubst < secret.yaml | kubectl apply -f -

kubectl apply -f cm.yaml -n argocd

kubectl patch deployment -n argocd argocd-repo-server --patch-file ./patch.yaml

