This README is a guide for installing and testing AVP that uses sops (AVP_Type=sops).

# Install

TODO

### Generate a key pair, extract the public key and create a config file for sops

```
brew install age

age-keygen -o key.txt

public_key=$(age-keygen -y key.txt)

cat > ./.sops.yaml << EOF
creation_rules:
  - encrypted_regex: ^(data|stringData)$
    age: $public_key
EOF
```

# Test AVP

### Encrypt sops-secret.yaml using sops

```
sops --encrypt sops-secret.yaml > sops-secret-enc.yaml
```

### Install avp cli

```
brew install argocd-vault-plugin
```

### Generate a Secret manifest with the encrypted value using avp cli

```bash
SOPS_AGE_KEY_FILE=./key.txt AVP_TYPE=sops argocd-vault-plugin generate sops.yaml
apiVersion: v1
data:
  secret: passw0rd
kind: Secret
metadata:
  annotations:
    avp.kubernetes.io/path: sops-secret-enc.yaml
    avp.kubernetes.io/remove-missing: "true"
  name: example-secret
  namespace: default
type: Opaque
```
