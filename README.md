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
