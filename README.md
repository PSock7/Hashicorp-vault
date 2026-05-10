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
### Storage Backend
Storage backend ins wgere vault will actually store all of its data in encrypted way.
There are a bunch of different options for storage.
If you are running in enterprise, you are probably running raft or consul as the backend.
If you are running open source, there is a wealth of supported backend that you can use.
Storage backend configure the location for storage of vault data.
storage is defnied in the main vault config file with the desired parameters.
All data is encrypted in transit tls and at rest using aes256
Some support backend support high availability: Ex : if you deploy a signle vault node you can use any backend storage but if you want to cluster vaults so you have HA you need a storage backend that support HA.
Other SB have better tools for management and data protection. Consul has snapshot
### Secrets Engines
SE are the heart of everything that vault does. SE either sotre,generate or encrypt data.
SE is a vault component that are responsible for managing secrets for your organization.It can sotre generate or encrypt data.
Many secrets engines can connect to tother platforms to generate dynamics cred on demand.
Multiple secrets engines can be enabled and used as need even multiple secrets engines of the same type
Secret engines are enabled and isolated at a path and all interaction are done directly with the path.
### Authentication Methods
Vault has auth methods and  a wealth of differents auths method that you can use to authenticate to vault to retrieve a token, to access secrets engines...
Auth methods is a vault component that perfom authentication and manage identites.
responsible for assigning identity and policies to a user.
multiple auth methods can be enabled depending on your use case
the fundamental goal of all auth methods is to obtain a token and each token has an associated policy and TTL
### Audit Devices
audit devives to ensure that everytthing is properly logged
keeps detailed log of all requests nd responses to vault
is formatted using json
sensitive info ais hashed before logging
can(and should) have more than one audit device enabled because bault prioritize safety over availability. it mean by that iif you have a single audit device enabled by default there are no audit devices enabled but you should enable one if you have a signle audit device.
Vault requires at least one audit device to write the log before completing the vault request -if enabled
prioritizer safety over availability. So if vault cannot write a log for a request it will not respond to the client
## Vault Path
Everything in vault is path-based
the path prefix tells vault which component a request should be routed
secret engines, auth methods, and audit devices are mount at a specific path
Paths available are dependent on the features enabled in vault such as auth methods and secrets engines.
System backend is a default backend in vault which is mounted at the **/sys** endpoint

Vault component  can be enabled at any path you would like using the **--path** flag 
each component does have a defaultpath you can use as well
Vault has a **few system reserved path** whiech you cannot use or remove:
- auth/ endpoint for auth method conf
- cubbyhole/ endpoint used by cybbuhole SE
- identity/ endpoint for conf vault identity
- secret/  endpoint used by kv v2 sE if running in dev mode
- sys/ endpoint for confi vault

## Vault Protection
### How does vault Protect data ?
VAult store all data in a storage backend. the data is not store in plain text it is encrypted.  
### How this encryption work ?
When data is written to the storage backend, vault uses an **encryption key** to ecrypt the data before it's stored the ecryption key is not enough. To protect ithe encryption key, vault uses a **root key**.
A ***root key*** encrypt the encryption key and add a critical layer of protection for us.
But even then, vault doesn't just keep that root key lying around.
During the unseal process, vault retrieves and reconstructs that root key using what's called unseal keys. Now these are typically managed using what's called **Shamir's secret sharing algorithm**
Now, once that root key is available, vault is going to use it to decrypt the encryption key.
And that encryption key is then held securely in memory.
Now this design ensures that all data in the storage back end always remains encrypted at rest.
The encryption key is going to be protected by the root key.The root key itself is never stored in plain text.Now, each vault node can encrypt and decrypt data in memory after unsealing.
#### Recap
Root key is responsible for encrypting and decrypting the encryption key which is the key vault uses to securely store your data at rest.

- The root key is going to be generated during vault init or rekey operation. When you are using the traditition unseal mechanism, the root key is never actually writtend to storage instead, it is split up into shards(fragment) or keys shards using shamir's secret sharing algorithm.
[Au démarrage, Vault n'a donc aucune idée de ce qu'est la clé racine.
2. La solution : Le partage de secret de Shamir (Shamir's Secret Sharing)
Pour résoudre ce problème, le mécanisme traditionnel s'appuie sur un concept cryptographique appelé Shamir's Secret Sharing.
Lors de la toute première initialisation de Vault (vault init), la clé racine est générée en mémoire, puis immédiatement divisée en plusieurs fragments (appelés Unseal Keys ou Key Shares).
Lors de cette création, vous définissez deux paramètres cruciaux :
Key Shares (n) : Le nombre total de fragments créés (par exemple, 5 clés).
Key Threshold (t) : Le nombre minimum (le seuil) de fragments nécessaires pour reconstituer la clé racine (par exemple, 3 clés sur 5).
Ces fragments sont ensuite distribués à différentes personnes de confiance au sein de votre organisation (les administrateurs de sécurité).
3. Le processus de déverrouillage manuel
Si le serveur redémarre (suite à une mise à jour ou un crash), il repasse en mode "scellé". Pour le déverrouiller, une seule personne ne suffit pas. Il faut que plusieurs administrateurs interviennent :
L'administrateur A saisit son fragment de clé. (Vault reste scellé, il a 1 clé sur 3).
L'administrateur B saisit le sien. (Vault reste scellé, il a 2 clés sur 3).
L'administrateur C saisit le sien. (Le seuil de 3 est atteint !).
Une fois le seuil atteint, Vault utilise ces fragments pour recalculer la clé racine à la volée, uniquement dans sa mémoire vive (RAM). Vault est alors "descellé" et parfaitement opérationnel, sans que la clé racine n'ait jamais été écrite sur le disque dur.
Pourquoi l'appelle-t-on "Traditionnel" ?
On précise "traditionnel" (ou manuel) par opposition aux méthodes plus récentes appelées "Auto-Unseal" (déverrouillage automatique).]
Auto-unseal: vault store key in encrypted format and it saved on the path core/aster within the storage backend and it is protected by a KMS service like key vault, KMS, AWS which handles unseling automatically for you.
- Encryption Key: Now that we know more about the root key, the encryption key is going to be that key that's used toencrypt and decrypt the actual data that's written to the storage back end.
Now it's going to be protected by that root key.
And it is stored in an encrypted format alongside of the vault data that it is protecting.
and this is typically in a structure that's referred to as a keyring.
Now one of the strengths of vault design is that the encryption keys can be easily rotated.

## Seal and Unseal
Vault starts in a sealed state, meaning it knows where to access the data and how, but can not decrypt it
Almost no operations are possible when vault is in a sealed state (only status check and unsealing are possible)
unsealing vault means you provide enough of those unsealed keys or key shards (fragment) to reconstruct the root key to decrypt the ecryption key and read the data
after unsealing the encryption key is stored in memory.Now, one important thing to note is this happens per node.So if you have multiple vault nodes within a cluster, each one of those nodes must be individually
unsealed after startup, even though they'll share the state and operate together as a cluster.
Sealing vault means that vaults essentially purges that encryption key from memory, and effectively
it locks itself down.Once it is sealed, vault cannot perform any operations until it is unsealed again using the required unseal keys. We can manually seal vault through the UI, CLI or API
### When would i seal vault
key shard are exposed
deteection of a compromise or network intrustion
spyware/malware on the vault nodes
### seal and unseal option
Vault offers severals options for how sealing and unsealing are handled depending on your environment and security needs.
1. Shamir sharing secret algorithm (default mechanism)
   The root key here is going to be split up into what's called unseal keys also known as key shards.And that's going to be using Shamir's secret sharing algorithm.Now when you do this you can have a configurable number of those keys.And they must be provided to reconstruct the root key and ultimately unseal vault.
   
2. Cloud Auto Unseal (recommended option) :Using this method, vault can integrate with cloud based key management services like AWS, KMS, AzureKey Vault or Google Cloud KMS.These services will help vault securely store and automatically decrypt the root key during startup,so no manual sealing is required.
 
3. Transit Auto Unseal : This option is going to allow you to use another vault cluster to perform the unseal process through the **Transit Secrets engine**. This is really useful in multi-cluster or hybrid environments, where a central vault can securely manage the unsealing of others clusters.
This is also extremely handy if you are working in environments where your vault nodes do not have the
access to access public KMS services, like the ones we talked about before.KMS key vault.Google cloud.
If your environment does not permit your vault clusters to hit those APIs, it may be useful to useTransit Auto Unseal so you can still have that auto unseal functionality.But now you're not required to permit your vault nodes to go out to the internet to hit those publiccloud provider APIs.

default mechanism : unseal key
auto unsel : recovery key

### Pros and Cons of unseal options
1. Pros

| Méthode        | Caractéristiques                                                     |
|:---------------|:---------------------------------------------------------------------|
| Keys Shards    | - Simplest form of unsealing                                         |
|                | - Works on any platform                                              |
|                | - Configuration options make it flexible                             |
| Auto Unseal    | - Automatic unsealing of Vault                                       |
|                | - Set and forget                                                     |
|                | - Integration benefits for running on same platform                  |
| Transit Unseal | - Automatic unsealing of Vault                                       |
|                | - Set and forget                                                     |
|                | - Platform agnostic                                                  |
|                | - Useful when running many Vault clusters across clouds/data centers |

2.Cons
| Méthode        | Inconvénients / Défis                                         |
|:---------------|:--------------------------------------------------------------|
| Keys Shards    | - Introduces risk for storing keys                            |
|                | - Requires manual intervention for unsealing                  |
|                | - Keys can be inadvertently shared and require rotation       |
| Auto Unseal    | - Regional requirements for cloud HSMs                        |
|                | - Cloud/vendor lock-in                                        |
| Transit Unseal | - Requires a centralized Vault cluster                        |
|                | - Centralized Vault cluster needs the highest level of uptime |

## Vault Initialization 
Initialization is what vault uses to prepare the storage backend to start receiving and managing our data.
The initialization is a one time operation that's tied to the storage backend and not the nodes themselves.
During the initialization,this is where vault is going to create the <span style="color:red">**root key**</span>. and <span style="color:red">** key shares**</span>.
By default vault will create 5 key shares in a threshold of 3. However you can customize theses values .
Vault provides options to define threshold, key-shares, recovery keys and encryptions and it also generate the <span style="color:green">**initial root token**</span> 
Vault can be initialized via cli api or ui

```bash
vault operator init \
    -key-shares=5 \
    -key-threshold=3
```
Now, another powerful option during initialization is something I mentioned before. It's using PGP encryption.
Now, if you don't want the person running the vault operator init command to see the unseal or recovery
keys in plain text, you can provide a list of PGP public keys ahead of time.Vault will encrypt each unseal key or recovery key using the matching public key, the PGP key, so only the intended recipient is able to decrypt their particular key.Now you can even encrypt the initial route token using PGP for additional protection.
## Vault Config file
Vault config file is used when we want to use vault on a long term basis instead of dev mode.
The config file can be written either in HCL or JSON format.
The config file includes different stanzas( {} ) and parameters to define a variety of configuration options
config file is specified when starting vault using <span style="color:blue">**--config flag**</span> 
usually stored somewhere in <span style="color:yellow">**/etc**</span> 
### What is configured in the file
- storage backend
- listeners and port
- tls certif
- seal type & config
- cluster name
- log level
- UI (enable or not)
- cluster IP and Port
### whats not
- secret engine
- auth method
- audit device
- policies
- entities & group
### available stanzas
- seal : seal type
- listener: addresses/ports for vault
- storage: storage backend
- telemetry: where to publish metrics to upstream systems
### ex of parameters
- cluster_name identifier for the cluster
- log_level specifies the log level to use - trace debug warn info error
- ui  enables the built-in web ui
- api_addr address to advertise to other vault servers for client redirection
- cluster_addr address to advertise to other vault servers for request forwarding

## Vault Storage Backend
Configures location for storage vault data
Open source users can choose a storage back in based upon their preference.
Remember that some of the storage backends support ha and some don't
Enterprise vault clusters should use either HashiCorp consul or integrated storage (raft)
### Storage Backends option list

| Liste A-G | Liste I-Z |
| :--- | :--- |
| Aerospike | In-Memory |
| Azure | Manta |
| Cassandra | MSSQL |
| CockroachDB | MySQL |
| Consul | OCI Object Storage |
| CouchDB | PostgreSQL |
| Etcd | Integrated Storage (Raft) |
| Filesystem | Amazon S3 |
| FoundationDB | Swift |
| Google Cloud Spanner | Zookeeper |
| Google Cloud Storage | |

So you can have different storage backends on your vault clusters just depending on your needs.
So again, really the purpose here that I wanted you to showcase is that there's only one storage backend
per vault cluster.
So every time you stamp out a new cluster, you're going to create a storage backend for it.
## Vault Audit Device
Keep a detailed log of all authenticated requests and responses to vault
Audit log is formatted using **JSON**
Sensitive information is hashed with salt using HMACi-SHA256 to ensure secrets and tokens are not stored in plain text
Log files should be protected as a user with permission can still check the value of those secrets via the **/sts/audit-hash API and compare it to the log file
```bash
vault audit enable file file_path=/var/log/vault_audit.log
```
| Périphérique | Caractéristiques et fonctionnement |
| :--- | :--- |
| **File** | • Écrit dans un fichier (ajoute les logs à la fin du fichier)<br>• Ne gère **pas** la rotation des logs<br>• Utiliser `fluentd` ou un outil similaire pour envoyer les logs vers un collecteur |
| **Syslog** | • Écrit les logs d'audit dans un service syslog<br>• Envoie les données uniquement à un agent local |
| **Socket** | • Écrit vers un socket TCP, UDP ou Unix<br>• Non fiable (dépend du protocole sous-jacent)<br>• Doit être utilisé lorsque des garanties fortes de livraison ne sont pas requises |

Vault can and should have more that one audit device.ùif there are any audit device enabled, vault requires that it can write to the log before completing the client request => prioritize safety over availability

## Vault Auth Methods 
Auth methods are vault components that perform authentication and manage identities
Responsible for assigning identity and policies to a user
Multiple auth methode can be enabled depending on your use case
When you authenticate into vault, you're going to get that token.
You're going to have an identity that's associated with your user, and then that token is going to
have a policy attached to it that dictates what you can and cannot do within vaults.
Auth method can be differentiated by human vs system methods.
The fundamental goal of all auth methods is to obtain a token and each token has an associated policies and TTL
By default the token auth method is enabled and you cannot enable another nor disable the tokens auth method
Each auth method is enabled at a path
you can choose the path name when (and only when) you enabled the auth method
if you do not provide a name the auth method will be enabled at its default path
vault auth enable approle
There are a few ways to auth to vault when using the cli : **vault login** and **vault token environment variable**
token helper (.vault-token): caches the token after authen. stores the token in a local file so it can be referenced for subsequent requests.

## Vault Entities
An **entity** is a representation of a single person or system used to log into vault. Each entity has a unique value. each entity is made up of zero or more **aliases**. On y attache des politiques (policies) de sécurité et des metadata.
Alias is a combintion of the auth method plus some identification. It is a mapping between an entity and auth method(s).
When we log in for the first time using auth method vault will create an entity and it will attach an alias to it if the entity does not exist
This is done using the **identity secrets engines**which anages internal identities that are recognized by vault
