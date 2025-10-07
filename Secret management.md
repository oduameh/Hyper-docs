# Secret management {#secret-management}

## Overview

Secure storage and management of cryptographic keys and sensitive data is critical for SSI systems. Hyperledger Identus integrates with Hashicorp Vault for enterprise-grade secret management, supporting both single-tenant and multi-tenant deployments with logical isolation.

This section covers secret storage architecture, key derivation, seed generation using BIP32/BIP39, and operational workflows for managing secrets across different deployment scenarios.

---

## Secret storage

**Status**: draft

**Date**: 2021-06-02

**NOTE**: this document is a draft and is not implemented yet. Statement in this document might be changed in the future.

## Introduction

Secrets are sensitive data that should be stored securely. There are following types of the secrets in the Identus Platform:

- seed: a secret used to derive cryptographic keys  
- private key: a secret used to sign data  
- any other entities that contain sensitive data (for instance, `credential-definition` and the `link-secret` used by the AnonCreds)

**NOTE**: public keys are not considered as secrets and can be stored in the same of other storage depending on the needs

The Identus Platform provides a secure storage for secrets. Hashicorp Vault is used as a secret storage service and provides a REST API, Web UI and command client to interact with the service.

**NOTE:** The Identus Platform uses a single Vault instance for all tenants per environment. Logical data separation is achieved by using Vault namespaces and policies applied to the tenant.

## Terminology

### Vault

Vault is a secrets management service developed by HashiCorp. It can be used as the default secret storage for the Identus Platform as well as for authentication and account management.

**NOTE**: The Identus Platform must not be dependent on the Vault service and must be able to use other services for the same purposes

### Agent

The Cloud agent is a service that provides an APIs to interact with the Identus Platform and use the SSI capabilities.

### Wallet

Logical component of the Agent that holds secrets and provides the logical or physical isolation of the data.

## Technical overview

### Cloud agent logical isolation

Each instance of the Cloud agent needs to have access to the secrets but must be isolated from other agents at the same environment. For horizontal scalability the group of agents can be configured to share the same namespace, so they can access the same secrets, but they still need to use different Vault account to authenticate themselves to the Vault service.

### Cloud agent authentication

Each instance of the Cloud agent needs to authenticate itself to the Vault service. The Vault service uses a token-based authentication mechanism. The Cloud agent uses a Vault [AppRole](https://developer.hashicorp.com/vault/docs/auth/approle) authentication method to authenticate itself to the Vault service. The token issued to the agent has the expiration time set in the application configuration. After the token expires, the agent needs to re-authenticate itself to the Vault service.

### Wallet authentication

Each instance of the Wallet needs to authenticate itself to the Vault service. The Cloud agent issues the authentication token to the Wallet based on the tenant ID.

### Secrets engine configuration

The Vault service uses a secrets engine to store secrets. KV2 secrets engine is used to store secrets in the Vault service and provides the following features:

- secrets are encrypted at rest  
- secrets are encrypted in transit  
- secrets are versioned  
- secrets can be deleted, restored and rolled back to a previous version  
- secrets are available via REST API, WEB UI, and command client  
- secrets are logically separated by tenants

### Single and multi-tenant configuration

The Identus Platform supports single and multi-tenant configurations. In the single-tenant configuration, the Cloud agent uses a single Wallet and a single Vault account to authenticate itself to the Vault service. In the multi-tenant configuration, the Cloud agent manages multiple Wallets, each Wallet is associated with a single tenant. Multi-tenant configuration is used to achieve logical data separation between tenants, so each Wallet can access only its own secrets. The Wallet is identified by the tenant ID and represented by the account in the Vault service.

The common sequence diagram for the single and multi-tenant configurations is shown below:

```
sequenceDiagram
    Tenant->>+Agent: REST/DIDComm API
    Agent->>+Wallet: route the request
    Wallet->>+Vault: manage secrets
    Vault->>+Wallet: secrets
    Wallet->>+Wallet: business logic
    Wallet->>+Agent: route the response
    Agent->>+Tenant: REST/DIDComm API
```

### Key derivation

The Identus Platform uses HD key derivation to derive cryptographic keys from the seed. The Wallet is initialized with the seed and uses it to derive cryptographic keys for managed DIDs. Key derivation path is conventional and is defined as follows:

```
m / wallet-purpose / DID-index / key-purpose / key-index
```

### Naming convention

To store the assets in the Vault service each asset is assigned a unique name. The Vault is a key/value store with metadata attached to the key and versioning.

The naming convention for the Vault assets is a matter of the implementation, but for the multi-tenant configuration all the assets of the Wallet must be stored under the path that contains the `tenant-id`.

For example, the `seed` can be stored by the following path:

```
<tenant-id>/seed value=<base64-encoded-value> <metadata>
```

The private keys for the DID can be stored by the following path:

```
<tenant-id>/dids/prism/<did-ref>/keys/<key-purpose>/<key-index>/<operation-hash> value=<base64-encoded-value> <metadata>
```

where:

- `tenant-id` is the tenant ID assigned to the Wallet  
- `did-ref` is the DID ref  
- `key-purpose` is the key purpose according to the PRISM DID Method specification  
- `key-index` is the key index. Starts from 0 and grows incrementally after each key rotation  
- `operation-hash` is the reference to the update DID document operation  
- `base64-encoded-value` is the base64-encoded value of the key  
- `metadata` is the key/value metadata attached the key that can be used to store additional information about the key such as `seed` or `key-derivation-path`

The keys material of the DID peer can be stored by the following path:

```
<tenant-id>/dids/peer/<did-ref>/keys/<key-purpose> value=<base64-encoded-value> <metadata>
```

Links:

- [PRISM DID Method Specification](https://github.com/input-output-hk/prism-did-method-spec)  
- [Vault Secrets Engine API](https://www.vaultproject.io/api/secret/kv/kv-v2)  
- [Vault Namespaces](https://www.vaultproject.io/docs/concepts/namespaces)  
- [HD Key Derivation ADR](https://github.com/input-output-hk/atala-prism-building-blocks/blob/main/docs/decisions/20230516-hierarchical-deterministic-key-generation-algorithm.md)  
- [Vault AppRole Authentication](https://www.vaultproject.io/docs/auth/approle)  
- [Vault Token Authentication](https://www.vaultproject.io/docs/auth/token)  
- [Vault KV Secrets Engine](https://www.vaultproject.io/docs/secrets/kv/kv-v2)  
- [Vault Entities](https://www.vaultproject.io/docs/concepts/entities)

---

## Secret operations

## Introduction

The Cloud agent offers a DID (Decentralized Identifier) management solution which involves creating, storing and using key materials. To generate a DID key material, the software relies on a seed, following the BIP32 / BIP39 standards. The system operators have the option to either provide their own seed or allow the software to generate one automatically. However, in a production environment, it is crucial for the system operators to explicitly supply the seed to the agent. This ensures full control over the DID key material and guarantees secure management of user identities.

The Cloud agent uses the following environment variables for secret management.

| Name | Description | Default |
| :---- | :---- | :---- |
| `SECRET_STORAGE_BACKEND` | The storage backend that will be used for the secret storage | `vault` |
| `VAULT_ADDR` | The address which the Cloud agent can reach the Vault | `http://localhost:8200` |
| `VAULT_TOKEN` | The token for accessing HashiCorp Vault | \- |
| `VAULT_APPROLE_ROLE_ID` | The `role_id` for HashiCorp Vault authentication with AppRole | \- |
| `VAULT_APPROLE_SECRET_ID` | The `secret_id` for HashiCorp Vault authentication with AppRole | \- |
| `VAULT_USE_SEMANTIC_PATH` | Enable full path convention for vault secret path | true |
| `DEFAULT_WALLET_SEED` | The seed used for DID key management for the default wallet | \- |

## Storage backend configuration

Secret storage supports various backends like the Vault service or Postgres database. By default, the backend chosen for secret storage is Vault, which is suitable for production environments. There are multiple supported backend implementations, each catering to specific use cases.

### HashiCorp Vault

When operating in a production environment, the agent has the option to utilize Vault as a secure secret storage backend. This choice is deemed suitable for production because all data is encrypted and it also offers additional security-related capabilities. By default, the agent uses this backend but the option is configurable. To utilize this backend, set the `SECRET_STORAGE_BACKEND` variable to `vault`.

**Authentication and Authorization**

The agent expects to read and write secrets to the path `/secret/*`, so ensure the appropriate permissions are provisioned.

Example Vault policy

```
path "secret/*" {
    capabilities = ["create", "read", "update", "patch", "delete", "list"]
}
```

HashiCorp Vault provides multiple authentication methods. One of the simplest methods is [token authentication](https://developer.hashicorp.com/vault/docs/auth/token). To authenticate using the token, set the environment variable `VAULT_TOKEN`. The agent prefers token authentication if provided with multiple authentication methods.

Another method is [AppRole authentication](https://developer.hashicorp.com/vault/docs/auth/approle) which is suitable for automatic workflows. To use AppRole authentication, set the environment variable `VAULT_APPROLE_ROLE_ID` and `VAULT_APPROLE_SECRET_ID`.

**Storage Backend**

HashiCorp Vault supports multiple backends for storage, such as filesystem, Etcd, PostgreSQL, or Integrated Storage (Raft). Each backend has different properties, which have implications for how secrets can be stored. The agent logically stores secrets in the following hierarchies.

```
# Wallet seed
/secret/<wallet-id>/seed

# Peer DID keys
/secret/<wallet-id>/dids/peer/<peer-did>/keys/<key-id>

# Generic secrets
/secret/<wallet-id>/generic-secrets/<specific-path>
```

Each storage backend has certain limitations, such as size, number of sub-paths, or path length. Some backends can support path lengths of up to 32,768 characters, while others only allow a few hundred characters. In some cases, the storage backends might not support the above logical convention due to excessively long paths.

To address this issue, the agent supports path shortening. This feature can be toggled by setting the environment variable `VAULT_USE_SEMANTIC_PATH=false`. When it is disabled, the unbounded portion of the path will be replaced by a SHA-256 digest of the original relative path. Additionally, the original path will be stored in the secret metadata.

### Postgres

Postgres is an alternative backend option for secret storage. However, this option must be explicitly chosen and will replace Vault. By opting for Postgres, there is no need for an additional service like Vault, which simplifies the process of setting up a local development instance. It utilizes the same database instance as the agent itself. To enable this option, set the `SECRET_STORAGE_BACKEND` to `postgres`, and it will utilize the same database configuration as the agent's database. It is important to note that while this option facilitates an easier development experience, it does not provide a secure method of storing secrets. The data is not encrypted, making it unsuitable for production use.

---

## Seed generation

## Introduction

The Cloud agent utilizes a hierarchical-deterministic key derivation algorithm for managing PRISM DIDs, which follows the BIP32 standard. In order to generate the required keys, BIP32 uses a master binary seed serving as the root of the derivation tree, and all other keys are derived from this seed. Given that the Cloud agent employs BIP32, it expects a 64-byte binary seed as input. Various methods exist for generating a byte sequence, each serving different purposes.

The Cloud agent does not have any opinion on how the seed should be generated as long as a valid hex string is provided. However, it is strongly recommended to use high entropy for generating the master seed. The Cloud agent allows customizing the default wallet seed by using the environment variable `DEFAULT_WALLET_SEED`. Other wallet seeds can also be configured when creating a wallet using REST API. The variable must contain a 64-byte value encoded in hexadecimal format.

### Static seed

The Cloud agent expects any valid 64-byte input for a wallet seed. Any static 128-character hexadecimal string can be used to simplify the testing.

For example

```shell
# Any of these are valid
DEFAULT_WALLET_SEED=00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
DEFAULT_WALLET_SEED=11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
DEFAULT_WALLET_SEED=0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a
```

**Note: Do not use method this in production.**

### Simple dynamic seed

Users have the option to create a random hexadecimal string of a desired length using scripting languages like Bash or Python. An example of a Bash script is shown below.

```shell
DEFAULT_WALLET_SEED=$(tr -dc a-f0-9 </dev/urandom | head -c 128 ; echo '')
```

This approach is suitable for basic testing scenarios requiring dynamically generated seeds. However, for production use, it is advisable to employ a reputable implementation of BIP39 with a high level of entropy. (Refer to the details below for further information.)

### Use BIP39 implementation to generate a seed (recommended)

The [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki#user-content-From_mnemonic_to_seed) protocol can be utilized to generate a BIP32 master seed, offering a human-friendly approach to seed management. Instead of noting down a seemingly random hexadecimal string, users can write down their mnemonic phrase, making it more convenient to keep track of them.

By using BIP39, users have options to choose a mnemonic phrase length as well as a passphrase. There are many tools for generating a BIP39 seed including but not limited to:

- [BIP39](https://iancoleman.io/bip39/) (use the BIP39 seed field which provides a 128-chars hex string)  
- [BIP39 \- implementations section](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki#other-implementations) also provides a list of implementations

---

## Secret storage flows

**Status**: draft

**Date**: 2021-06-02

**NOTE**: this document is a draft and is not implemented yet. Statement in this document might be changed in the future.

## Introduction

The current document describes the sequence diagrams of the Identus Platform components: APISIX, Cloud agent, Wallet, Vault, Tenant. The diagrams are stated from the simplest scenarios to the more complex ones to enforce the security and privacy of the data.

## Sequence diagrams

### Phase \#1: Basic Flow for Secret Management

This diagram describes the basic flow for the secret management.

Goal : the Agent stores the secrets using the `root` token to access the Vault service

Context:

- Tenant uses the access token to access the REST API via APISIX.  
- The Agent uses root Vault token to communicate with the Vault.  
- Tenant represented by any REST API client, Web or Mobile application.

```
sequenceDiagram
    actor User
    User ->> Application: run
    Application->>+APISIX: Agent REST API request + API token
    loop
        APISIX->>APISIX: validate API token
    end
    APISIX->>+Agent: Agent REST API request
    activate Agent
    loop Manage Secrets
        Agent->>+Vault: Vault REST API request
        Vault->>+Agent: Vault REST API response
    end
    Agent->>+Agent: Execute Business Logic
    Agent->>+APISIX: Agent REST API response
    deactivate Agent
    APISIX->>+Application: Agent REST API response
    Application->>+ User: react
```

### Phase \#2: Single Tenant Flow for Secret Management

This diagram describes the flow for the secret management for the single tenant.

Goal: AppRole authentication method is used to authenticate the Agent to the Vault service.

Context:

- The Agent is authenticated to the Vault using the AppRole authentication method.  
- Tenant uses the access token to access the REST API via APISIX.  
- Tenant represented by any REST API client, Web or Mobile application.

```
sequenceDiagram
    actor User
    User ->> Application: run
    Application->>+APISIX: Agent REST API request + API token
    loop
        APISIX->>APISIX: validate API token
    end
    APISIX->>+Agent: Agent REST API request
    activate Agent
    loop Authentication
        Agent->>+Vault: Get auth token
        Vault->>+Agent: Return auth token
    end
    loop Manage Secrets
        Agent->>+Vault: Vault REST API request
        Vault->>+Agent: Vault REST API response
    end
    Agent->>+Agent: Execute Business Logic
    Agent->>+APISIX: Agent REST API response
    deactivate Agent
    APISIX->>+Application: Agent REST API response
    Application->>+ User: react
```

### Phase \#3: Single Tenant Flow for Secret Management with Wallet

This diagram describes the flow for the secret management for the single tenant with the Wallet.

Goal: Tenant uses JWT token to authenticate to the Identus Platform.

Context:

- The Agent is authenticated to the Vault using the AppRole authentication method.  
- Tenant uses the access token to access the REST API via APISIX (probably this might be removed, we need to decide what to do with the `api-token`)  
- Tenant represented by any REST API client, Web or Mobile application authenticated to the Identus Platform using JWT token.  
- Tenant uses the Wallet to communicate with the Vault

```
sequenceDiagram
    participant User
    participant Application
    participant APISIX
    participant Agent
    participant Wallet
    participant Agent
    participant Wallet
    participant Vault
    actor User

    User->>+Application: run
    loop Authentication
        Application ->> APISIX: get jwt-token
        APISIX ->> Vault: get jwt-token
        Vault ->> APISIX: jwt-token
        APISIX ->> Application: jwt-token
    end
    Application->>+APISIX: REST API + api-token + jwt-token
    loop
        APISIX->>APISIX: validate api-token
    end
    APISIX->>+Agent: Agent REST API request + jwt-token
    Agent ->>+ Wallet: handle request
    loop Manage Secrets
        Wallet->>+Vault: Vault REST API request
        Vault->>+Wallet: Vault REST API response
        Wallet->>+Wallet: Execute Business Logic
    end
    Wallet ->>+ Agent: response
    Agent ->>+ APISIX: Agent REST API response
    APISIX ->>+ Application: Agent REST API response
    Application ->>+ User: react
```

### Phase \#4: Multi Tenant Flow for Secret Management

This diagram describes the flow for the secret management for the multi tenant.

## //TBD
