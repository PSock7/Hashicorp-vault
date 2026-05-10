Vault command
```
- Vault server -dev : to run dev mode
- vault read sys/config/state/sanitized : view server configuration
- vault status - Check server status
- vault login root : authenticate to vault using the root token
-  Vault secrets list : to list all secret engine
-  vault secrets list -detailed : Get more information about a specific secrets engine
- vault secrets enable -path=prd-secrets -version=2 kv
- vault auth list : list enabled auth methods
- vault auth list -detailed : get more detailled about auth method information
- vault kv put secret/first hello=world : write a single secret
- vault kv put secret/my-secret username=admin password=secret123 : write multiple data for a secret
- vault kv get secret/first : read a secret
- vault kv get -field=hello secret/first : read a data field of a secret 
- vault kv put path key=value : to store a secret
- vault kv list secret/ : list secrets in a path 
- vault kv get path : to retrieve information of secret
- vault kv delete path : ex vault kv delete secret/first delete a secret
- vault token create -ttl=1h : Create a new token
- vault token lookup : View current token information
- vault token capabilities secret/first : View token capabilities: (user)
- vault token create -ttl=1h : create a token with a ttl
```

- Create a secret is JSON Format :
```
vault kv put secret/user - <<EOF
{
  "data": {
    "username": "admin",
    "password": "secret",
    "email": "admin@example.com"
  }
}
EOF
```

```
- vault operator init : to retrieve root token and unseal key
- vault operator init -key-shares=5  -key-threshold=3 :Initialize Vault with 5 key shares and a threshold of 3 (this is the default but practice using the flags)
- vault operator unseal : to unseal vault node
- vault operator seal seal the vault
- vault audit enable file file_path=/var/log/vault/audit.log
- vault audit list --detailed
- vault read sys/config/state/sanitized : View server configuration
- vault kv metadata delete prd-secrets/myapp/config : delete all version of a secret
````
- create a policy
```cat > app-policy.hcl << EOF
path "prd-secrets/data/myapp/*" {
  capabilities = ["read", "list"]
}
path "prd-secrets/metadata/myapp/*" {
  capabilities = ["list", "read"]
}
EOF
```

```vault policy write app-reader app-policy.hcl
- vault policy read app-reader
- vault policy list
```

```# Enable userpass auth method
vault auth enable userpass 

# vault auth method with a custom path
vault auth enable -path=vault-course -description=myapp userpass

# Create a user and attach policy
vault write auth/userpass/users/myapp \
    password="password123" \
    policies="app-reader"

# List users
vault list auth/userpass/users
```

```bash
vault auth list # to list enabled auth method
vault auth disable <path_name> # ex vault auth disable approle
vault auth help # info to use vault auth command
```

```bash
# configure the auth method using auth prefix with approle 
vault write auth/<mount_name_or_path_name_of_the_auth_method>/role/<role_name> # creae a new role called <role_name>
vault write auth/<mount_name_or_path_name>/role/<role_name> \secret_is_ttl=10m token_num_uses=10 token_ttl=20m

# Ex
vault auth enabled -path=bryan -description=bryan auth meth userpass
vault write auth/<mount_name_or_path_name_of_the_auth_method>/users/<user_name>
vault write auth/bryan/users/krausen password=vault policies=bryan
## list users
vault list auth/bryan/users
> Keys
-----
krausen
# read more details/information about the users
vault read auth/bryan/users/krausen
```

- login
```bash
# method 1 to authenticate to vault using vault_token env variable
VAULT_TOKEN=$(vault login -method=userpass \
    username=myapp \
    password=password123 \
    -format=json | jq -r '.auth.client_token')
# method 2 authenticate to vaul using vault login command
vault login <token>
vault login -method=userpass username=my-username

```
