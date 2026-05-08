# Vault
##  What is a secret :
A secret is anything your organization deems (considere) sensitive:
  	* usernames and passwords
  	* API Keys
    * Certificates
	* Encryption Keys
## Introduction to vault
Vault is a tool to manage secrets and protect sensitive data like api keys, certifcats or encryption keys.
Vault provide a single source of secrets for both Humans and Machines, and complete lifecycle management for secrets:
* Eliminates secret sprawl (sprawl=prolifération) =>a prolifération des secrets (ou secret sprawl en anglais) est un terme de cybersécurité qui désigne la dispersion incontrôlée et non sécurisée d'informations confidentielles à travers les systèmes, les applications et les infrastructures d'une organisation.
  
* Securely store any secret
* Provide governance for access to secrets (Policies)

We can put secret in vault and they can just remain there forever.
But if you want vault to generate dynamic secrets for you,for your applications ,rather than using long lived static secrets, vault can manage the lifecycle, meaning it can go generate secrets for you.
It can put a lease on those secrets.
it can handle renewl of that lease.

## How vault works
Vault has multiple ways that we can interact or get into vault as well.: we-ve got the CLI, API and the UI.
* API: the api is generally for machine to machine integrations and authorization.
* UI: is always going to be for human access.
* CLI: is another interface that we can interact with vault and can be either machines or human

If  a human user would like to interact with vaults, they authenticate to vault using a different number or kind of authentification methods like :
* Username & Password
* RoleID & SecretID
* TLS Certificate
* Integrated cloud credentials

Once vault validates the authentification method it generate a token which have a TTL (Time To Live) ex: 1hour, 10mn etc.

## Benefits of Hashicorp Vault
1. Vault can store long live static secrets
2. Vault can dynamically generate secrets upon request. Ex:if we have an application needs AWS cred to go to a thing right. May be it's terraform and it wants to go deploy infrastructure on aws. We don't want to have cred that live 24/7 for a job that take only a few  minutes. A way is to integrate Terraform or jenkins directly to the vault.Vault can generate cred on their behalf and pass those back to the application.
3. Vault has a fully-featured API
4. Vault can act as a root or intermediate CA : meaning vault can actually issue PKI certificates to applications.
So instead of going through that long process of doing things like, hey, I need to generate a CSR,submit the CSR to the CA, get it signed, get the private key.
	### Use Cases
	- Centralize the storage of secrets
	- Migrate to Dynamically Generated secrets
	- secure data with a centralized workflow for Encryption Operations
	- Automate the generation of X509 certificates
	- Migrate to IBA (identity based access)
	
	 #### Migrate to dynamic credentials
	  ![Static vs dynamic Vault](/home/xdev/Downloads/Vault/static-dyn-vault.png)

	#### Encrypt Data
**secrets Engines** :
Generic Secret Engine:KV, Kubernetes,LDAP, PKI Certificates, SSH, TOTP, transit
Cloud secret Engines; Alicoud,aws, azure gcp
Infra secrets Engines: Consul, Databases, Nomad, Rabbitmq
Autres: KMIP,Transform,Key Management, 

### Vault compare versions
We have community, enterprise and vault on HCP version
- Community Version:
    - Dynamic secret
	- ACL Templates
	- init and unseal workflow Sealed = Le coffre est fermé, les données sont chiffrées et inaccessibles.Unsealing = L'action de fournir les clés pour déchiffrer la clé principale et rendre Vault opérationnel.
	- Vault Agent
	- Key Rolling
	- Access control Policies
	- Encryption as a service
	- public cloud (gcp,azure,aws) auto unseal
- Enterprise Version:
     - Disaster Recovery
	 - Namespaces
	 - Replication
	 - Read replicas
	 - HSM Auto-unseal
	 - Sentinel
- HCP Vault Version:
	- Hosted by Hashicorp
	- Fully managed solution
	- scalable
	- pay by the hour
	- ALl Enterprise Features
	- Dev or prod options
	- Reduce Admin Burden
	- Push button deployment
	  
	#### Benefits of each versions
	1. Vault community includes:
		* features and integrations
		*  local HA by way of clustering
		*  Access to almost all secrets engines
		*  Can easily integrate with any application using a fully-featured API
	2. Does not include
		 * No replication capabilites = single datacenter/cloud deploy
		 * No enterprise integration
		 * Limited scalability
	3. Vault Enterprise includes :
		 *Access to all feature and functions that Vault offers
		 *  Replication capabilities to other Vault clusters accross data centers/cloud
		 *  All secrets engines and auth methods
		 *  Can easily integrate with any application using a fully-featured API
		 *  Namespaces for a multi-tenancy solution
		 *  Policy as code using Sentinel
		 *  Easily scale locals reads using performance standbys
		 *  Access to the raft/consul snapshot agent for an automated disaster recovery solution
	4. Does not include
	   * Self managed - not hosed or managed by hashicorp

## Vault Components
