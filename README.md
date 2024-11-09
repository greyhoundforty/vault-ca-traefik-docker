# Overview

Using vault as a CA for Traefik 

[drafts-note](drafts://open?uuid=5D15ED55-7219-46EB-BC0E-4E5F30DB4AE6)

```shell
git clone repo.git
cd repo
```

## Round 2

For this one I will create the vault cluster first and go through the CA config for my local domain.

- create directory structure 
- create vault docker container
- init and unseal vault
- create vault server cert
- create policies/roles/token > [save new token for traefik container](#set-token) 
- [create CA and Intermediate CA](https://developer.hashicorp.com/vault/tutorials/secrets-management/pki-engine) > save files using json commands below 
-  

```
vault write -field=certificate pki/root/generate/internal \
     common_name="homelab.cantina" \
     issuer_name="homelab-cantina-root-ca-2024" \
     ttl=87600h > homelab-cantina-root-ca-2024.crt


CA_ISSUER=$(vault list -format=json pki/issuers/ | jq -r '.[0]')

vault secrets enable -path=pki_int pki

vault secrets tune -max-lease-ttl=43800h pki_int

vault pki issue \
      --issuer_name=homelab-dot-cantina-intermediate \
      /pki/issuer/${CA_ISSUER} \
      /pki_int/ \
      common_name="homelab.cantina Intermediate Authority" \
      o="homelab" \
      ou="cantina" \
      key_type="rsa" \
      key_bits="4096" \
      max_depth_len=1 \
      permitted_dns_domains="homelab.cantina" \
      ttl="43800h"

vault write pki_int/roles/homelab-dot-cantina \
     issuer_ref="$(vault read -field=default pki_int/config/issuers)" \
     allowed_domains="homelab.cantina" \
     allow_subdomains=true \
     max_ttl="720h"


vault write -format=json pki_int/issue/homelab-dot-cantina common_name="test.homelab.cantina" ttl="24h" > cert_data.json

```